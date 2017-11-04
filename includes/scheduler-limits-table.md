I följande tabell beskrivs viktiga kvoter, gränser, standarder och begränsningar i Azure Schemaläggaren.

| Resurs | Beskrivning av gränsen |
| --- | --- |
| **Jobbstorlek** |Maximala jobbstorlek är 16 kB. Om en PUT- eller KORRIGERINGSFIL resulterar i ett jobb som är större än dessa gränser, returnerade statuskoden 400 Felaktig begäran. |
| **Begäran om URL-storlek** |Maximal storlek för den begärda Webbadressen är 2048 tecken. |
| **Sammanställd huvudstorlek** |Maximal sammanställd huvudstorlek är 4096 tecken. |
| **Antal sidhuvud** |Maximal sidhuvud antalet är 50 huvuden. |
| **Textstorleken** |Maximal textstorleken är 8192 tecken. |
| **Återkommande intervall** |Största antal upprepningar är 18 månader. |
| **Tid för starttid** |Största ”time starttiden” är 18 månader. |
| **Jobbhistorik** |Maximal svarstexten lagras i jobbhistoriken är 2 048 byte. |
| **Frekvens** |Max frekvens standardkvot är 1 timme i en fri jobbsamling och 1 minut i en standard jobbsamlingen. Max replikeringsfrekvensen kan konfigureras på en jobbsamling vara lägre än maximalt. Alla jobb i jobbsamlingen är begränsade värdet på jobbsamlingen. Om du försöker skapa ett jobb med en frekvens som är högre än den maximala frekvensen på jobbsamlingen misslyckas begäran med statuskod 409 konflikt. |
| **Jobb** |Högsta antal jobb standardkvot är 5 jobb i en fri jobbsamling och 50 jobb i en standard jobbsamling. Det maximala antalet jobb kan konfigureras på en jobbsamling. Alla jobb i jobbsamlingen är begränsade värdet på jobbsamlingen. Om du försöker skapa fler jobb än den maximala jobb kvoten, misslyckas denna begäran med statuskod 409 konflikt. |
| **Jobbsamlingar** |Maximalt antal jobbsamlingen per prenumeration är 200 000. |
| **Kvarhållning av jobb historik** |Jobbhistorik bevaras i upp till 2 månader eller upp till de senaste 1 000 körningar. |
| **Kvarhållning av slutfördes och en felaktig jobb** |Slutfördes och en felaktig jobb finns kvar i 60 dagar. |
| **Timeout** |Det finns en statisk (kan inte konfigureras) begäran-timeout 60 sekunder för HTTP-åtgärder. För längre körs, följ asynkron HTTP-protokoll; till exempel returnera en 202 omedelbart men fortsätta arbeta i bakgrunden. |

