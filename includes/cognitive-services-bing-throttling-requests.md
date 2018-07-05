Antalet frågor du kan ställa per sekund (QPS) beror på tjänsten och typen av prenumeration. Försäkra dig om att programmet har den logik som behövs för att hålla dig inom kvoten. Om du överskrider dina QPS misslyckas begäran med HTTP-statuskoden 429. Svaret innehåller även Retry-After-huvudet, som innehåller antal sekunder som du bör vänta innan du skickar en ny begäran.  
  
### <a name="denial-of-service-dos-versus-throttling"></a>Överbelastning (DOS) eller begränsning?

Tjänster skiljer sig mellan en DOS-attack och en QPS-överträdelse. Begäran lyckas om tjänsten misstänker en DOS-attack (HTTP-statuskod 200 OK), men brödtexten i svaret är tom.