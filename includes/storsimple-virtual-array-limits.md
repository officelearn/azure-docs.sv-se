

| **Gränsen identifierare** | **Gränsen** | **Kommentarer** |
| --- | --- | --- |
| Total kapacitet (inklusive moln) |Upp till 64 TB per virtuell enhet |Du kan växla över en fullständig StorSimple virtuell matris till en annan tom matris. Se till att du har tillräckligt med utrymme på enheten för att slutföra den här åtgärden om du försöker återställa till samma enhet. När du har överskridit 32 TB, kan du inte återställa på samma enhet. |
| Maximalt antal lagringskontouppgifter per enhet |1 | |
| Maximalt antal volymer/resurser |16 | |
| Minsta storlek på en skiktad resurs |500 GB | |
| Minsta storlek på en nivåindelad volym |500 GB | |
| Maximal storlek för en skiktad resurs |20 TB | |
| Maximal storlek för en nivåindelad volym |5 TB | |
| Minsta storleken för en lokalt Fäst resurs |50 GB | |
| Minsta storleken för en lokalt Fäst volym |50 GB | |
| Maximal storlek för en lokalt Fäst resurs |2 TB | |
| Maximal storlek för en lokalt Fäst volym |200 GB | |
| Maximalt antal iSCSI klientanslutningar från initierarna |512 | |
| Högsta antal access control poster per enhet |64 | |
| Maximalt antal säkerhetskopior som behålls av den virtuella enheten i *.backups* mapp för filserver |5 |Detta inkluderar den senaste schemalagda (som genereras av standardprincip för säkerhetskopiering) och manuella säkerhetskopieringar. |
| Maximalt antal schemalagda säkerhetskopieringar sparas av enheten |55 |30 daglig säkerhetskopiering<br>12 månatliga säkerhetskopior<br>13 årlig säkerhetskopiering |
| Maximalt antal manuella säkerhetskopieringar sparas av enheten |45 | |
| Maximalt antal filer per resurs för en filserver |1 miljon |När du utför en återställning av enheten, är återställa tiderna proportionell mot antalet filer över alla resurser på enheten. |
| Maximalt antal filer per volym för en iSCSI-server |1 miljon | |
| Maximalt antal filer per virtuell matris |4 miljoner | |
| Återställning återställa tid |Snabb återställning |Återställningen är baserad på termisk karta och beror på volymens storlek.<br>Säkerhetskopieringsåtgärder kan inträffa när en återställningsåtgärd pågår. |

