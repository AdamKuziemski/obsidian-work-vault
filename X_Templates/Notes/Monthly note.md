## Wins

## Lessons

## Unfinished Business

```dataview
TASK
FROM "Daily"
SORT file.name DESC
WHERE !completed AND file.ctime >= date(today) - dur(1 month)
GROUP BY file.name
```