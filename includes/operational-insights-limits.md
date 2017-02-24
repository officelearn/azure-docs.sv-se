
>[!NOTE]
>Log Analytics kallades tidigare för Operational Insights.
>
>

Följande begränsningar gäller för Log Analytics-resurser per prenumeration:

| Resurs | Standardgräns | Kommentarer
| --- | --- | --- |
| Antal lediga arbetsytor per prenumeration | 10 | Den här gränsen kan inte höjas. |
| Antal betalda arbetsytor per prenumeration | Saknas | Du är begränsad till antalet resurser i en resursgrupp och antalet resursgrupper per prenumeration | 


Följande begränsningar gäller för varje Log Analytics-arbetsyta:

|  | Kostnadsfri | Standard | Premium | Fristående | OMS |
| --- | --- | --- | --- | --- | --- |
| Datavolym som samlas in per dag |500 MB<sup>1</sup> |Ingen |Ingen | Ingen | Ingen
| Datakvarhållningstid |7 dagar |1 månad |12 månader | 1 månad <sup>2</sup> | 1 månad <sup>2</sup>|

<sup>1</sup> När kunderna når den dagliga dataöverföringsgränsen på 500 MB stoppas dataanalysen och återupptas i början av nästa dag. En dag baseras på UTC.

<sup>2</sup> Datakvarhållningstiden för prisalternativen Fristående och OMS kan förlängas med 730 dagar.

| Kategori | Begränsningar | Kommentarer
| --- | --- | --- |
| API för datainsamling | Maximal storlek för ett enskilt inlägg är 30 MB<br>Maximal storlek för fältvärden är 32 kB | Dela större volymer i flera olika inlägg<br>Fält som är längre än 32 kB trunkeras. |
| Sök-API | 5&000; poster returneras för ej sammanräknade data<br>500&000; poster för sammanräknade data | Sammanräknade data är en sökning som innefattar kommandot `measure`
 


<!--HONumber=Feb17_HO3-->


