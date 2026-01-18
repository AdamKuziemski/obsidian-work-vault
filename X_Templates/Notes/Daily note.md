<%*
const day = new Date().getDay();
if (day === 1) {-%>  
## Meetings this Week

```dataview
LIST date
FROM "Meetings"
WHERE date >= date(sow) AND date <= date(eow)
SORT date ASC
```

<%* } else if (day === 2) { -%>  
Tuesday stuff
<%* } else if (day === 3) { -%>  
Wednesday stuff
<%* } else if (day === 4) { -%>  
Thursday stuff
<%* } else if (day === 5) { -%>  
Make some time at the end of the day to write your weekly note.
<%* } -%>
## Meetings Today

### Daily Standup


### Other Meetings

```dataview
LIST date
FROM "Meetings"
WHERE date = date(today)
SORT date ASC
```
## Things to Do


## Notes

