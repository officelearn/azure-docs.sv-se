En DNS-zon används som värd åt DNS-posterna för en viss domän. För att kunna börja vara värd för din domän så måste du skapa en DNS-zon. DNS-poster som skapats för en viss domän kommer att finnas inuti DNS-zonen för domänen. 

Domänen "contoso.com" kan till exempel innehålla flera DNS-poster, till exempel "mail.contoso.com" (för en e-postserver) och "www.contoso.com" (för en webbplats). 


## <a name="names"></a>Om DNS-zonnamn
 
- Namnet på zonen måste vara unikt inom resursgruppen och zonen får inte redan finnas. Annars misslyckas åtgärden.

- Samma zonnamn kan återanvändas i en annan resursgrupp eller Azure-prenumeration. 

- Då flera zoner delar samma namn, kommer varje instans att tilldelas olika adresser för namnservern och bara en instans kan delegeras från den överordnade domänen. Mer information finns i [Delegera en domän till Azure DNS](../articles/dns/dns-domain-delegation.md).

<!--HONumber=Sep16_HO3-->


