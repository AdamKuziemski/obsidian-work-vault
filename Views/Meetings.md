## This Week

```dataview
LIST date
FROM "Meetings"
WHERE date >= date(sow) AND date <= date(eow)
SORT date ASC
```

## Next Week

```dataview
LIST date
FROM "Meetings"
WHERE date >= (date(eow) + dur(1 day)) AND date <= (date(eow) + dur(1 week))
SORT date ASC
```

## Last Week

```dataview
LIST date
FROM "Meetings"
WHERE date <= (date(sow) - dur(1 day)) AND date >= (date(sow) - dur(1 week))
SORT date ASC
```