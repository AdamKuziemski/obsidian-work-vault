<%*
const today = new Date();
const year = today.getFullYear();
const month = today.getMonth();
const day = today.getDay();

tR += tp.app.vault.getMarkdownFiles().filter(file => {
  const cdate = new Date(file.stat.ctime);
  return cdate.getFullYear() === year && cdate.getMonth() === month && cdate.getDay() === day;
}).map(file => `[[${file.basename}]]`).join('\n');
_%>