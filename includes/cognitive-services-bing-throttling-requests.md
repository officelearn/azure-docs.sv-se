Antalet frågor du kan ställa per sekund (QPS) beror på tjänsten och typen av prenumeration. Se till att programmet har den logik som behövs för att hålla dig inom kvoten. Om QPS-gränsen uppnås eller överskrids leder det till att begäran misslyckas och då returneras HTTP-statuskoden 429. Svaret innehåller `Retry-After`-huvudet, som anger hur länge du bör vänta innan du skickar en ny begäran.

## <a name="denial-of-service-versus-throttling"></a>Överbelastning eller begränsning?

Tjänsten skiljer på en överbelastningsattack (DoS) och en QPS-överträdelse. Om tjänsten misstänker en DoS-attack slutförs begäran (HTTP-statuskoden är 200 OK). Brödtexten i svaret är dock tom.