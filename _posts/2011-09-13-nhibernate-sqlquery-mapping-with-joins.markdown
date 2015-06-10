---
layout: post
title: NHibernate SQLQuery mapping with joins
date: 2011-09-13 23:09:40.000000000 -08:00
---
We had a more complicated hql query that needed many joins, and included one join that didn’t involve primary keys. It ended up being pretty slow and the SQL generated was terrible, so I rewrote it into a custom SQL query. The model I needed mapped was an inherited table, so I’d somehow have to get NHibernate to map using data from both tables. There was a gotcha that took be a bit of time to get around. Here’s a kind of obfuscated version of the query.

~~~ csharp
var blocks = CurrentSession
.CreateSQLQuery("SELECT {stb.*}, {ev.*}, {ei.*}, {sm.*}, {v.*}, {p.*} " +
                "FROM TableA stb_1_ " +
                //Aliased funny because nhibernate doesn't map inherited tables automatically
                "JOIN TableB stb ON stb_1_.Id = stb.tbId " +
                "JOIN TableC ev ON ev.Id = stb_1_.evId " +
                "JOIN TableD ei ON ei.eiId = ev.Id " +
                "JOIN TableE sm ON sm.Id = ev.smId " +
                "JOIN TableF v ON v.Id = sm.vId " +
                "JOIN TableG p ON p.Id = ev.ppId " +
                "JOIN TableH zc ON zc.pc = v.pc " +
                "JOIN TableI mr ON mr.Id = zc.mrId " +
                "WHERE mr.Id = :mrId " +
                "AND stb_1_.ColumnA > 1 AND stb_1_.ColumnB > :now AND ev.DateTime <= :maxDate " +
                "AND stb_1_.ColumnC > 0 AND stb_1_.ColumnC <= 6 " +
                "AND (sm.ir = 1 AND ev.DateTime > :minDate OR sm.ir = 0 AND ev.DateTime > :minParamA) " +
                "AND (:rcId IS NULL OR :rcId IN (SELECT cId FROM TableJ WHERE pId = TableJ.Id)) " +
                "AND (:scId IS NULL OR :scId IN (SELECT cId FROM TableJ WHERE pId = TableJ.Id)) " +
                "AND (:pId IS NULL OR :pId = p.Id)")

.AddEntity("stb", typeof (TableB))
.AddJoin("ev", "stb.TableC")
.AddJoin("ei", "ev.TableD")
.AddJoin("sm", "ev.TableE")
.AddJoin("v", "sm.TableF")
.AddJoin("p", "ev.TableG")
.SetGuid("mrId", mrId)
.SetParameter("minDate", DateTime.UtcNow.AddDays(1))
.SetParameter("minParamA", DateTime.UtcNow.AddDays(5))
.SetParameter("maxDate", DateTime.UtcNow.AddDays(90))
.SetParameter("now", DateTime.UtcNow)
.SetParameter("rcId", rcId.HasValue ? rcId : (object) DBNull.Value, NHibernateUtil.Guid)
.SetParameter("scId", scId.HasValue ? scId : (object) DBNull.Value, NHibernateUtil.Guid)
.SetParameter("pId", pId.HasValue ? pId : (object) DBNull.Value, NHibernateUtil.Guid);
~~~

So I had to alias TableA as stb_1_ to get nhibernate to map that to my entity TableB. The reason is that NHibernate automatically generates aliases for the sources of your entity data. TableB fields mapped directly to stb, while fields that are a part of TableA were supposed to map to stb_1_. If I left out the alias on that table, NHibernate would complain that it can’t map any of the fields because stb_1_.FieldA (for however many fields you have) doesn’t exist. To get the alias I had to run the query without any aliasing and check the error message. It has a consistent naming convention, so it will continue working unless you change or add tables mapped to the entity.
