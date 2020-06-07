---
layout: post
title: Implementing INotifyDataErrorInfo for complex models in WPF
date: 2015-09-14 22:55:57.000000000 -07:00
---
This is part one of a two part series, in which I'll detail an effective approach for architecting a
validation framework for complex objects in WPF. In this part, I'll talk about what is necessary to 
fully implement INotifyDataErrorInfo. There are a number of other tutorials about this interface, but
I don't feel any go into enough depth about what to do when facing a complex hierarchies of models. 
For this tutorial, I'm going to assume you've seen a few of these tutorials or have tried implementing 
INotifyDataErrorInfo, so I may skip over some of the more mundane aspects.

# How INotifyDataErrorInfo Works

INotifyDataErrorInfo only has three members:

```csharp
bool HasErrors { get; }
IEnumerable GetErrors(string propertyName);
event EventHandler<DataErrorsChangedEventArgs> ErrorsChanged
```

Given a simplified ViewModel as such:

```csharp
public class ViewModel1 //Assume some implementation of IPropertyNotifyChanged
{
  private int _maxChildren;
  public int MaxChildren
  {
    get { return _maxChildren; }
    set
    {
      _maxChildren = value;
      NotifyPropertyChanged();
    }
  }

  private string _someProperty;
  public string SomeProperty
  {
    get { return _someProperty; }
    set 
    { 
      _someProperty = value;
      OnPropertyChanged();
    }
  }

  public List<ChildViewModel1> ChildViewModels
  {
    get;
    set;
  }
}

public class ChildViewModel1
{
  private string _anotherProperty;
  public string AnotherProperty
  {
    get { return _anotherProperty; }
    set 
    { 
      _anotherProperty = value;
      OnPropertyChanged();
    }
  }
  
  public ChildViewModel2 { get; set; }
}

public class ChildViewModel2
{
  private string _yetAnotherProperty;
  public string YetAnotherProperty
  {
    get { return _yetAnotherProperty; }
    set 
    { 
      _yetAnotherProperty = value;
      OnPropertyChanged();
    }
  }
}
```

To implement this correctly, your viewmodel needs to handle the appropriate items:

1. Whenever a property that you want validated is modified, it has to notify the ErrorsChanged event. 
2. For example, if we wanted ChildViewModel1 to correctly notify that its AnotherProperty field is too 
3. short, it might look like this:

```csharp
public class ChildViewModel1
{
  private string _anotherProperty;
  public string AnotherProperty
  {
    get { return _anotherProperty; }
    set 
    { 
      _anotherProperty = value;
      OnPropertyChanged();
      OnErrorsChanged("AnotherProperty");
    }
  }
  
  public ChildViewModel2 { get; set; }
  
  protected OnErrorsChanged(string propertyName)
  {
    ErrorsChanged(new DataErrorsChangedEventArgs(propertyName));
  }
}
```

Important to note that my example is a hierarchy, and I'm going to assume the views are as well. This
can open up a lot of complexities if validation of one model requires checking other models in the 
hierarchy. I'll cover this later.

2. HasErrors must return whether any errors exist on the view model. This is usually straightforward, 
but in the case of ViewModel1 above, what if you wanted it to return true if any there are any errors 
in its children or its children's child?

3. GetErrors is the easiest to implement in most cases, as it just needs to return the validation errors 
for the given view model.

So above I mentioned what can be some major problems implementing INotifyDataErrorInfo in this example, 
but here is a full list I've come up with:

1. How to best deal with HasErrors if it's necessary to account for child errors?
1. If an error occurs in a child view model, how will the parent view get notified to rebind?
1. If a parent/child viewmodel property is modified, how would the child/parent be notified to rerun 
validation, assuming there are dependencies in executing validation?
1. When should OnErrorsChanged(String.Empty) ever be called?

# Sample Implementation

Here's a naive implementation of ViewModel1 and ChildViewModel1 to detail how ugly this can all get
(no guarantees this would compile):

```csharp
public class ViewModel1 : IPropertyNotifyChanged, INotifyDataErrorInfo //Assume there are simple implementations
{
  private List<ValidationResult> validationResult;

  public ViewModel1()
  {
    ChildViewModels = new ObservableCollection<ChildViewModel1>(); 
    ChildViewModels.CollectionChanged += childViewModels_changed;
    validationResult = new List<ValidationResult>();
  }

  public void childViewModels_changed(object sender, NotifyCollectionChangedEventArgs args)
  {
    //Have to watch for changes in the children :(
    foreach(var newItem in args.NewItems.OfType<IPropertyNotifyChanged>())
    {
      newItem.PropertyChanged += childViewModels_itemchanged;
    }
    
    foreach(var oldItem in args.OldItems.OfType<IPropertyNotifyChanged>())
    {
      oldItem.PropertyChanged -= childViewModels_itemchanged;
    }
    
    Validate(); //To have less code, just revalidate
  }

  private string _someProperty;
  public string SomeProperty
  {
    get { return _someProperty; }
    set 
    { 
      _someProperty = value;
      OnPropertyChanged();
      Validate();
    }
  }
  
  public IEnumerable GetErrors(string propertyName)
  {
    return validationResult.Where(p => p.MemberNames.Any(q => q == propertyName));
  }
  
  public bool HasErrors
  {
    get { return validationResult.Any() || ChildViewModels.Any(p => p.HasError); }
  }
  
  public void Validate()
  {
    //Have to keep old one around so we can notify the view that errors have disappeared as well :(
    var existing = validationResult; 
    validationResult = new List<ValidationResult>(); 
  
    if (SomeProperty.Length > 50)
    {
      validationResult.Add(new ValidationResult("SomeProperty must be 50 characters or less", new string[] { "SomeProperty" }));
    }
   
    if (MaxChildren < ChildViewModels.Count())
    {
      validationResult.Add(new ValidationResult("There can be no more than " + MaxChildren + " ChildViewModels", new string[] { "ChildViewModels" }));
    }
   
    foreach(var result in existing)
    {
      OnErrorsChanged(result.MemberNames.First());
    }
    
    foreach(var result in validationResult)
    {
      OnErrorsChanged(result.MemberNames.First());
    }
    
    OnErrorsChanged(String.Empty);
  }

  public ObservableCollection<ChildViewModel1> ChildViewModels
  {
    get;
    set;
  }
}

public class ChildViewModel1 : INotifyDataErrorInfo, IPropertyNotifyChanged //Assume missing functions are implemented
  public ViewModel1 Parent { get; set; }

  public ChildViewModel1(ViewModel1 parent)
  {
    parent.PropertyChanged += parent_propertyChanged;
  }

  private string _anotherProperty;
  public string AnotherProperty
  {
    get { return _anotherProperty; }
    set 
    { 
      _anotherProperty = value;
      OnPropertyChanged();
      Validate();
    }
  }
  
  public ChildViewModel2 { get; set; }
  
  public void Validate()
  {
    var existing = validationResult; 
    validationResult = new List<ValidationResult>(); 
    
    if (Parent.SomeProperty == AnotherProperty)
    {
      validationResult.Add(new ValidationResult("SomeProperty cannot equal AnotherProperty", new string[] { "AnotherProperty" }));
    }
    
    foreach(var result in existing)
    {
      OnErrorsChanged(result.MemberNames.First());
    }
    
    foreach(var result in validationResult)
    {
      OnErrorsChanged(result.MemberNames.First());
    }
    
    OnErrorsChanged(String.Empty);
  }
  
  public IEnumerable GetErrors(string propertyName)
  {
    return validationResult.Where(p => p.MemberNames.Any(q => q == propertyName));
  }
  
  public bool HasErrors
  {
    get { return validationResult.Any(); }
  }
}
```

So I'm sure you can imagine, in a full-blown production application, this would get really ugly, really 
fast. Also, it can over-notify due to it not checking if each property has changed error state. At least 
it would work and behave relatively predictably with any bugs worked out. Hopefully this gave you a good 
idea of what is needed to correctly INotifyDataErrorInfo. In the next part, I'll cover my solution using 
FluentValidation and ReactiveUI.