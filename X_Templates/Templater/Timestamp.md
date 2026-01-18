<%*
	const clocks = ['🕛','🕧','🕐','🕜','🕑','🕝','🕒','🕞','🕓','🕟','🕔','🕠','🕕','🕡','🕖','🕢','🕗','🕣','🕘','🕤','🕙','🕥','🕚','🕦'];
	const now = new Date();
	const time = Number((2 * (now.getHours() + now.getMinutes() / 60)).toFixed()) % 24;
	tR += tp.date.now('LT ') + clocks[time];
%>