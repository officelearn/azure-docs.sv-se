<!--author=alkohli last changed: 12/15/15-->

| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal lagringskontouppgifter |64 | |
| Maximalt antal volymbehållare |64 | |
| Maximalt antal volymer |255 | |
| Maximalt antal scheman per bandbreddsmall |168 |Ett schema för varje timme, varje dag i veckan (24 * 7). |
| Maximal storlek för en nivåindelad volym på fysiska enheter |64 TB för 8100 och 8600 |8100 och 8600 är fysiska enheter. |
| Maximal storlek för en nivåindelad volym på virtuella enheter i Azure |30 TB för 8010 <br></br> 64 TB för 8020 |8010 och 8020 är virtuella enheter i Azure som använder standardlagring respektive Premium-lagring. |
| Maximal storlek för en lokalt Fäst volym på fysiska enheter |9 TB för 8100 <br></br> 24 TB för 8600 |8100 och 8600 är fysiska enheter. |
| Maximalt antal iSCSI-anslutningar |512 | |
| Maximalt antal iSCSI klientanslutningar från initierarna |512 | |
| Högsta antal access control poster per enhet |64 | |
| Maximalt antal volymer per princip för säkerhetskopiering |24 | |
| Maximalt antal säkerhetskopior behålls per princip för säkerhetskopiering |64 | |
| Maximalt antal scheman per princip för säkerhetskopiering |10 | |
| Maximalt antal ögonblicksbilder av typer som kan behållas per volym |256 |Detta inkluderar lokala ögonblicksbilder och molnbaserade ögonblicksbilder. |
| Maximalt antal ögonblicksbilder som kan finnas i valfri enhet |10 000 | |
| Maximalt antal volymer som kan bearbetas parallellt för säkerhetskopiering, återställning eller klona |16 |<ul><li>Om det finns fler än 16 volymer kan bearbetas de sekventiellt när bearbetningen fack blir tillgängliga.</li><li>Nya säkerhetskopior av en klonade eller en återställd nivåindelad volym kan inte ske förrän åtgärden har slutförts. Men för en lokal volym är säkerhetskopior tillåtna när volymen är online.</li></ul> |
| Återställa och klona Återställ tid för nivåindelade volymer |< 2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter för återställning eller klona igen, oavsett volymens storlek.</li><li>Den volymen först gå långsammare än normalt som de flesta data och metadata finns fortfarande i molnet. Prestanda kan öka som data flödar från molnet till StorSimple-enhet.</li><li>Total tid för att hämta metadata är beroende av allokerade volymens storlek. Metadata hämtas automatiskt till enheten i bakgrunden i frekvens på 5 minuter per TB allokerade volymdata. Detta värde kan påverkas av Internet-bandbredd till molnet.</li><li>Restore- eller kopieringen är klar när alla metadata finns på enheten.</li><li>Säkerhetskopieringsåtgärder kan inte utföras förrän återställningen eller kopieringen är helt klar. |
| Återställning återställa tid för lokalt fästa volymer |< 2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter efter återställningen, oavsett volymens storlek.</li><li>Den volymen först gå långsammare än normalt som de flesta data och metadata finns fortfarande i molnet. Prestanda kan öka som data flödar från molnet till StorSimple-enhet.</li><li>Total tid för att hämta metadata är beroende av allokerade volymens storlek. Metadata hämtas automatiskt till enheten i bakgrunden i frekvens på 5 minuter per TB allokerade volymdata. Detta värde kan påverkas av Internet-bandbredd till molnet.</li><li>Till skillnad från nivåindelade volymer för lokalt fästa volymer hämtas även volymens data lokalt på enheten. Återställningen är klar när alla volymdata har trätt till enheten.</li><li>Återställningsåtgärderna kan bli långa och den totala tid att slutföra återställningen beror på storleken på den etablerade volymen för lokala och Internet-bandbredd och befintliga data på enheten. Säkerhetskopieringsåtgärder på lokalt Fäst volym tillåts medan återställningen pågår. |
| Tunn-återställning tillgänglighet |Senaste redundans | |
| Maximalt antal klienter läsning och skrivning genomströmning (när hanteras från SSD-nivån) * |920/720 MB/s med en enda 10GbE nätverksgränssnittet |Upp till 2 x med MPIO och två nätverksgränssnitt. |
| Maximalt antal klienter läsning och skrivning genomströmning (när hanteras från hårddisknivån) * |120/250 MB/s | |
| Maximalt antal klienter läsning och skrivning genomströmning (när hanteras från molnnivån) * |11/41 MB/s |Läs genomströmning beroende klienter skapa och upprätthålla tillräcklig i/o-ködjup. |

&#42;Maximalt dataflöde per i/o-typ har mätt med 100 procent Läs- och 100 procent skrivåtgärder scenarier. Faktiska genomflöde kan vara lägre och beror på i/o blanda och nätverk villkor.

