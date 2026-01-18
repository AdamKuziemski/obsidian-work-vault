## Unfinished Tasks

### Last 7 Days
```dataview
TASK
FROM "Daily"
SORT file.name DESC
WHERE !completed AND file.ctime >= date(today) - dur(1 week)
GROUP BY file.name
```
### Older 👀
```dataview
TASK
FROM "Daily"
SORT file.name DESC
WHERE !completed AND file.ctime < date(today) - dur(1 week)
GROUP BY file.name
```
### Projects
```dataview
TASK
FROM "Projects"
SORT file.name DESC
GROUP BY file.name
```
## Meetings

### This Week

```dataview
LIST date
FROM "Meetings"
WHERE date >= date(sow) AND date <= date(eow)
SORT date ASC
```

### Next Week

```dataview
LIST date
FROM "Meetings"
WHERE date >= (date(eow) + dur(1 day)) AND date <= (date(eow) + dur(1 week))
SORT date ASC
```

### Last Week

```dataview
LIST date
FROM "Meetings"
WHERE date <= (date(sow) - dur(1 day)) AND date >= (date(sow) - dur(1 week))
SORT date ASC
```