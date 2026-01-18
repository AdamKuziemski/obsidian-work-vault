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
## Weeks in this Month

<%*
// Get year and month from note name, assumes format of "YYYY-MM"
const [year, month] = tp.file.title.split("-");

// Momentjs works with months starting at 0 index (January is month 0, February is month 1, etc)
// Subtract one to convert to starting at 0 index
const monthZeroIndex = month - 1;

// Get number of days in month
const monthDate = moment([year, monthZeroIndex]);
const daysInMonth = monthDate.daysInMonth();

// For each day in month
const weeks = [];
for (let dayOfMonth = 1; dayOfMonth <= daysInMonth; ++dayOfMonth) {
  // Get week for day of month
  const week = moment([year, monthZeroIndex, dayOfMonth]).week();

  // If week is a new week, add to list of weeks for month
  if (!weeks.some(x => x === week)) {
    weeks.push(week);
  }
}

// Format weeks to be links, formatted at "[[YYYY-[W]WW]]"
const weeksFormatted = weeks.map(week => (
  `[[${year}-W${String(week).padStart(2, "0")}]]`
)).join("\n");
-%>

<% weeksFormatted %>