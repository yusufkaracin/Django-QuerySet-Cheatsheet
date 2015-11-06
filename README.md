# Cheatsheet for Django QuerySets
Current Django Version: [1.8](https://docs.djangoproject.com/en/1.8/ref/models/querysets/)

Methods that return new [QuerySets](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#methods-that-return-new-querysets)

**Can be chained:**

```python
Entry.objects.filter(**kwargs).exclude(**kwargs).order_by(**kwargs)
```

 * [filter](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#filter)
 ```python
 Entry.objects.filter(id__in=[1, 3, 4])
 SELECT ... WHERE id IN (1, 3, 4);
 
 inner_qs = Blog.objects.filter(name__contains='Cheddar')
 entries = Entry.objects.filter(blog__in=inner_qs)
 SELECT ... WHERE blog.id IN (SELECT id FROM ... WHERE NAME LIKE '%Cheddar%')
 ``` 
 
 * [exclude](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#exclude)
 ```python
 Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3), headline='Hello')
 SELECT ... WHERE NOT (pub_date > '2005-1-3' AND headline = 'Hello')
 
 Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3)).exclude(headline='Hello')
 SELECT ... WHERE NOT pub_date > '2005-1-3' AND NOT headline = 'Hello'
 ```
 * [annotate](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#annotate)
 ```python
 >>> from django.db.models import Count
 >>> q = Blog.objects.annotate(Count('entry'))
 # The name of the first blog
 >>> q[0].name
 'Blogasaurus'
 # The number of entries on the first blog
 >>> q[0].entry__count
 42
 
 >>> q = Blog.objects.annotate(number_of_entries=Count('entry'))
 # The number of entries on the first blog, using the name provided
 >>> q[0].number_of_entries
 42
 ```
 * [order_by](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#order-by)
 ```python
 Entry.objects.filter(pub_date__year=2005).order_by('-pub_date', 'headline')
 The result above will be ordered by pub_date descending, then by headline ascending. 
 The negative sign in front   of "-pub_date" indicates descending order. 
 Ascending order is implied. 
 
 # No Join
 Entry.objects.order_by('blog_id')
 # Join
 Entry.objects.order_by('blog__id')
 ```
 * [reverse](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#reverse)
 ```python
 Use the reverse() method to reverse the order in which a queryset’s elements are returned. 
 Calling reverse() a second time restores the ordering back to the normal direction.
 
 my_queryset.reverse()[:5]
 ```
 * [distinct](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#distinct)
 ```python
 When using distinct() and values() together, be careful when ordering by fields
 not in the values() call.
 
 When you specify field names, you must provide an order_by() in the QuerySet, 
 and the fields in order_by() must  start with the fields in distinct(), in the same order.
 
 Author.objects.distinct()

 Entry.objects.order_by('pub_date').distinct('pub_date')
 
 Entry.objects.order_by('blog__name', 'mod_date').distinct('blog__name', 'mod_date')
 ```
 * [values](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#values)
 ```python
 Blog.objects.filter(name__startswith='Beatles').values()
 [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]
 
 Entry.objects.values('blog_id')
 [{'blog_id': 1}, ...]
 
 Blog.objects.values('name', 'entry__headline')
 [{'name': 'My blog', 'entry__headline': 'An entry'},
     {'name': 'My blog', 'entry__headline': 'Another entry'}, ...]
 ```
 * [values_list](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#values-list)
 ```python
 Entry.objects.values_list('id', 'headline')
 [(1, 'First entry'), ...]
 
 Entry.objects.values_list('id', flat=True).order_by('id')
 [1, 2, 3, ...]
 ```
 * [dates](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#dates)
 * [datetimes](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#datetimes)
 * [none](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#none)
 * [all](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#all)
 * [select_related](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#select-related)
 * [prefetch_related](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#prefetch-related)
 * [extra](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#extra)
 * [defer](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#defer)
 * [only](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#only)
 * [using](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#using)
 * [select_for_update](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#select-for-update)
 * [raw](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#raw)

## Methods that do not return QuerySets

 * [get](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#get)
 * [create](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#create)
 * [get_or_create](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#get-or-create)
 * [update_or_create](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#update-or-create)
 * [bulk_create](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#bulk-create)
 * [count](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#count)
 * [in_bulk](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#in-bulk)
 * [iterator](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#iterator)
 * [latest](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#latest)
 * [earliest](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#earliest)
 * [first](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#first)
 * [last](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#last)
 * [aggregate](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#aggregate)
 * [exists](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#exists)
 * [update](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#update)
 * [delete](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#delete)
 * [as_manager](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#as-manager)

## Field lookups

**Field lookups are how you specify the meat of an SQL WHERE clause. They’re specified as keyword arguments to the QuerySet methods *filter()*, *exclude()* and *get()*.**

```python
Example: Entry.objects.get(id__exact=14)  # note double underscore.
```

 * [exact](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#exact)
 * [iexact](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#iexact)
 * [contains](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#contains)
 * [icontains](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#icontains)
 * [in](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#in)
 * [gt](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#gt)
 * [gte](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#gte)
 * [lt](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#lt)
 * [lte](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#lte)
 * [startswith](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#startswith)
 * [istartswith](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#istartswith)
 * [endswith](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#endswith)
 * [iendswith](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#iendswith)
 * [range](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#range)
 * [year](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#year)
 * [month](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#month)
 * [day](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#day)
 * [week_day](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#week_day)
 * [hour](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#hour)
 * [minute](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#minute)
 * [second](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#second)
 * [isnull](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#isnull)
 * [search](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#search)
 * [regex](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#regex)
 * [iregex](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#iregex)

**Protip: Use [in](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#in) to avoid chaining [filter()](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#filter) and [exclude()](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#exclude)**




```python
Entry.objects.filter(status__in=['Hung over', 'Sober', 'Drunk'])
```

## Aggregation functions

 * [expression (new in 1.8)](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#expression)
 * [output_field (new in 1.8)](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#output_field)
 * [Avg](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#avg)
 * [Count](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#id6)
 * [Max](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#max)
 * [Min](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#min)
 * [StdDev](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#stddev)
 * [Sum](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#sum)
 * [Variance](https://docs.djangoproject.com/en/1.6/ref/models/querysets/#variance)

## Query-related classes

 * [Q() objects](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#q-objects)
 * [Prefetch() objects](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#prefetch-objects)

- - -

<a rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/deed.en_US"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/3.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">Django-QuerySet-Cheatsheet</span> by <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">@chrisdl and @briandant</span> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/deed.en_US">Creative Commons Attribution-ShareAlike 3.0 Unported License</a>.<br />

The Django web framework referenced in the Django-QuerySet-Cheatsheet is ​© 2005-2015 Django Software Foundation.
Django is a registered trademark of the Django Software Foundation.
