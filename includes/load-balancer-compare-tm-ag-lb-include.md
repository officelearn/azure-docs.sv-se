## <a name="load-balancer-differences"></a>Skillnader i belastningsutjämnare

Det finns olika alternativ för att distribuera nätverkstrafik med Microsoft Azure. De här alternativen fungerar på olika sätt, har olika funktionsuppsättningar och stöder olika scenarier. De kan användas var för sig eller i kombination med varandra.

* **Azure Load Balancer** fungerar på transportlagret (Layer 4 i OSI-nätverksreferensstacken). Den ger trafikdistribuering på nätverksnivå över programinstanser som körs på samma Azure-datacenter.
* **Application Gateway** fungerar på programlagret (Layer 7 i OSI-nätverksreferensstacken). Den fungerar som en omvänd proxy-tjänst och avslutar klientanslutningen och vidarebefordrar begäranden till slutpunkter i serverdelen.
* **Traffic Manager** fungerar på DNS-nivå.  Den använder sig av DNS-svar för att dirigera slutanvändartrafik till globalt distribuerade slutpunkter. Klienterna ansluter därefter till dessa slutpunkter direkt.

Följande tabell sammanfattar de funktioner som erbjuds av varje tjänst:

| Tjänst | Azure Load Balancer | Application Gateway | Traffic Manager |
| --- | --- | --- | --- |
| Teknologi |Transportnivå (Layer 4) |Programnivå (Layer 7) |DNS-nivå |
| Programprotokoll som stöds |Alla |HTTP och HTTPS |Alla (en HTTP-slutpunkt krävs för slutpunktsövervakning) |
| Slutpunkter |Azure VM:ar och Cloud Services-rollinstanser |Alla interna Azure-IP-adresser eller offentliga Internet-IP-adresser |Azure VM:ar, Cloud Services, Azure Web Apps och externa slutpunkter |
| Vnet-stöd |Kan användas för både Internetriktade och interna (Vnet)-program |Kan användas för både Internetriktade och interna (Vnet)-program |Stöder enbart Internetriktade program |
| Slutpunktsövervakning |Stöds via avsökningar |Stöds via avsökningar |Stöds via HTTP/HTTPS GET |

Azure Load Balancer och Application Gateway styr nätverkstrafiken till slutpunkter, men de har olika användningsscenarier avseende vilken trafik de hanterar. Tabellen nedan hjälper dig att förstå skillnaden mellan de två belastningsutjämnarna:

| Typ | Azure Load Balancer | Application Gateway |
| --- | --- | --- |
| Protokoll |UDP/TCP |HTTP/HTTPS |
| IP-reservation |Stöds |Stöds inte |
| Belastningsutjämningsläge |5-tuppel(käll-IP, källport, mål-IP, målport, protokolltyp) |Resursallokering<br>Routning baserat på URL |
| Belastningsutjämningsläge(käll-IP/fästa sessioner) |2-tuppel (käll-IP och mål-IP), 3-tuppel (käll-IP, mål-IP och port). Kan skala upp eller ned baserat på antalet virtuella datorer |Cookie-baserad tillhörighet<br>Routning baserat på URL |
| Hälsotillståndsavsökningar |Standardvärde: avsökningsintervall - 15 sekunder. Tagen ur rotation: 2 kontinuerlig fel. Stöder användardefinierade avsökningar |Vilande avsökningsintervall 30 sekunder. Tas ut efter 5 efterföljande trafikfel live eller ett enda avsökningsfel i vilande läge. Stöder användardefinierade avsökningar |
| SSL-avlastning |Stöds inte |Stöds |


<!--HONumber=Nov16_HO2-->


