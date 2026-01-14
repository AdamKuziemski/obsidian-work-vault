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