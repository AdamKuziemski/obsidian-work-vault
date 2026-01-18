## Wins


## Lessons


## Unfinished Tasks

```dataview
TASK
FROM "Daily"
SORT file.name DESC
WHERE !completed AND file.ctime >= date(today) - dur(1 week)
GROUP BY file.name
```
## Days in this Week

<%*
tR += Array.from({ length: 5 })
  .map((_, i) => {
    const d = new Date();
    d.setDate(d.getDate() - (4 - i));
    const month = `${d.getMonth() + 1}`.padStart(2, '0'); // JS dates ffs
    const day = `${d.getDate()}`.padStart(2, '0');
    return `* [[${d.getFullYear()}-${month}-${day}]]`;
}).join('\n');
%>
