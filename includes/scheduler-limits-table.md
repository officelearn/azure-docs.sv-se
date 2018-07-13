I följande tabell beskrivs de större kvoter, gränser, standardinställningar och begränsningar i Azure Scheduler.

| Resurs | Beskrivning av gränsen |
| --- | --- |
| **Jobbstorlekar** |Maximal jobbstorlekar är 16 kB. Om en PUT- eller en PATCH resulterar i ett jobb som är större än de här gränserna, returneras statuskod 400 Felaktig begäran. |
| **Storleksförfrågningar med URL: en** |Maximal storlek för fråge-URL är 2048 tecken. |
| **Sammanställd huvudstorlek** |Högsta aggregerade huvudstorlek är 4096 tecken. |
| **Huvud-antal** |Maximal rubrik returneras 50 rubriker. |
| **Textstorleken** |Maximal textstorleken är 8192 tecken. |
| **Återkommande intervall** |Största antal upprepningar span är 18 månader. |
| **Tid för starttid** |Högsta ”time till starttid” är 18 månader. |
| **Jobbhistorik** |Maximal svarstexten som lagras i jobbhistoriken är 2 048 byte. |
| **Frekvens** |Max frekvens standardkvoten är 1 timme i en fri jobbsamling och 1 minut i en standard jobbsamling. Max frekvensen kan konfigureras på en jobbsamling är lägre än maximalt. Alla jobb i jobbsamlingen är begränsade värdet på jobbsamlingen. Om du försöker skapa ett jobb med en frekvens som är högre än den maximala frekvensen på jobbsamlingen att begäran misslyckas med statuskod 409 konflikt. |
| **Jobb** |Max jobb standardkvoten är 5 jobb i en fri jobbsamling och 50 jobb i en standard jobbsamling. Det maximala antalet jobb kan konfigureras på en jobbsamling. Alla jobb i jobbsamlingen är begränsade värdet på jobbsamlingen. Om du försöker skapa fler jobb än de maximala jobb kvoten, misslyckas denna begäran med statuskod 409 konflikt. |
| **Jobbsamlingar** |Maximalt antal jobb-samling per prenumeration är 200 000. |
| **Kvarhållning av jobb** |Jobbhistorik bibehålls i upp till 2 månader eller upp till den senaste 1 000 körningar. |
| **Kvarhållning av tillståndet Completed eller faulted jobb** |Tillståndet Completed eller faulted jobb finns kvar i 60 dagar. |
| **Timeout** |Det finns en statisk (inte kan konfigureras) begäran tidsgräns på 60 sekunder för HTTP-åtgärder. Följ HTTP asynkrona protokoll; för längre åtgärder, till exempel returnera 202 omedelbart men fortsätta arbeta i bakgrunden. |

