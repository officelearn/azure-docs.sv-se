<properties
   pageTitle="Asymmetrisk routning | Microsoft Azure"
   description="Den här artikeln beskriver problem som kan uppstå i samband med asymmetrisk routning i kundens nätverk när det finns flera länkar till ett mål."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"/>

# Asymmetrisk routning med flera nätverksvägar

Den här artikeln förklarar hur utgående och återvändande trafik kan ta olika vägar när det finns flera vägar mellan källan och målet.

För att förstå asymmetrisk routning är det viktigt att förstå två begrepp. Det ena är effekten av flera nätverksvägar. Det andra är hur enheterna som hanterar tillstånd beter sig, t.ex. brandväggar. Dessa kallas tillståndskänsliga enheter. En kombination av dessa två faktorer ger upphov till scenarier där trafiken ignoreras av en tillståndskänslig enhet eftersom den inte ser att den ursprungligen kom genom den själv.

## Flera nätverksvägar

När ett företagsnätverk bara har en länk till Internet via sin Internetleverantör kommer all trafik till och från Internet via samma väg. Företag köper ofta flera kanaler, som redundanta sökvägar, för att förbättra nätverkets aktiva tid. I dessa fall är det möjligt att trafik som går ut från nätverket mot Internet passerar en länk, medan returtrafiken kommer via en annan länk. Detta kallas asymmetrisk routning, där den återvändande trafiken tar en annan väg än det ursprungliga flödet.

![Routing3](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Föregående beskrivning gäller Internet, men även för andra kombinationer av flera vägar. Exempel är en Internetväg och en privat väg till samma mål, flera privata vägar till samma mål osv. 

Varje router längs vägen från källan till målet beräknar den bästa vägen för att nå ett mål baserat på dess beräkning av den bästa vägen till målet. Den bästa möjliga vägen fastställs baserat på två huvudfaktorer.

1.  Routning mellan externa nätverk baseras på routningsprotokollet BGP (Border Gateway Protocol). BGP hämtar in meddelanden från grannar och kör dem genom flera steg för att avgöra den bästa vägen till målet och lägger till den i sin routningstabell.
2.  Längden på nätmasken för en väg. Om flera meddelanden för samma IP-adress men olika nätmasker tas emot prioriteras meddelandet med den längre nätmasken eftersom den anses vara en mer specifik väg.

## Tillståndskänsliga enheter

Routrar tittar på paketets IP-huvud för att bestämma routningen. Det finns dock enheter som tittar ännu djupare i paketet. Vanligtvis tittar dessa enheter på Layer4-huvudet (TCP/UDP) eller till och med Layer7-huvudet (Application Layer). Dessa enheter är antingen säkerhetsenheter eller enheter för bandbreddsoptimering. En brandvägg är ett vanligt exempel på en tillståndskänslig enhet. Brandväggar tillåter eller nekar ett paket passage genom sina gränssnitt baserat på olika fält som protokoll, TCP/UDP-port, URL-huvuden. Den här paketinspektionen medför hög belastning på enheten. För att förbättra prestanda inspekterar brandväggen det första paketet i ett flöde. Om paketet tillåts sparas flödesinformationen i tillståndstabellen. Alla efterföljande paket som är relaterade till detta flöde tillåts baserat på det första beslutet. Det betyder att om ett paket, som är en del av ett befintligt flöde, kommer till brandväggen och brandväggen inte har någon befintlig tillståndsinformation om det, så ignoreras paketet.

## Asymmetrisk routning med ExpressRoute

När du ansluter till Microsoft via ExpressRoute händer följande med ditt nätverk:

1.  Du har flera länkar till Microsoft. En länk är din befintliga Internetanslutning och en annan är via ExpressRoute. En del trafik till Microsoft kan gå genom Internet men komma tillbaka via ExpressRoute eller tvärtom.
2.  Du får mer specifika IP-adresser via ExpressRoute. Trafik från ditt nätverk till Microsoft för tjänster som erbjuds via ExpressRoute föredrar därför alltid ExpressRoute. 

För att förstå effekten av de två punkterna ovan ska vi titta på några scenarier. Anta att du bara har en kanal till Internet och att du använder alla Microsofts tjänster via Internet. Trafiken från nätverket till Microsoft och tillbaka passerar genom samma Internetlänk och passerar genom brandväggen. Brandväggen registrerar flödet när den ser det första paketet och returpaket tillåts eftersom flödet finns i tillståndstabellen.

![Routing1](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


Nu aktiverar du ExpressRoute och använder tjänster som erbjuds av Microsoft via ExpressRoute. Alla andra tjänster från Microsoft används via Internet. Du distribuerar en separat brandvägg vid din nätverksgräns som ansluter till ExpressRoute. Microsoft annonserar mer specifika prefix till ditt nätverk via ExpressRoute för specifika tjänster. Din routningsinfrastruktur väljer ExpressRoute som primär väg för dessa prefix. Om du inte annonserar dina offentliga IP-adresser till Microsoft via ExpressRoute kommunicerar Microsoft med dina offentliga IP-adresser via Internet. Det betyder att trafik från ditt nätverk till Microsoft använder ExpressRoute medan returtrafik från Microsoft använder Internet. När brandväggen vid din nätverksgräns ser ett svarspaket för ett flöde som inte kan hittas i tillståndstabellen ignorerar den returtrafiken. 

Om du väljer att använda samma NAT-pool för ExpressRoute och för Internet ser du liknande problem med klienter på privata IP-adresser i nätverket. Begäranden av tjänster som Windows Update går genom Internet eftersom IP-adresser för dessa tjänster inte annonseras via ExpressRoute. Returtrafiken kommer dock tillbaka via ExpressRoute. Om Microsoft tar emot en IP-adress med samma nätmask från Internet och ExpressRoute, prioriteras ExpressRoute framför Internet. Om en brandvägg eller en annan tillståndskänslig enhet vid din nätverksgräns, som använder ExpressRoute, inte har någon befintlig information om flödet, ignorerar den paket som hör till det flödet. 

## Lösningar för problem med asymmetrisk routning

Det finns två sätt att lösa problemet med asymmetrisk routning. Det ena är via routning och det andra via källbaserad NAT (SNAT). 

1. Routning 

    - Du bör se till att dina offentliga IP-adresser annonseras till relevanta WAN-länkar. Till exempel kanske du vill använda Internet för autentiseringstrafik och ExpressRoute för e-posttrafik. I så fall ska du inte annonsera dina offentliga IP-adresser för AD FS via ExpressRoute. På samma sätt ska den lokala AD FS-servern inte exponeras för IP-adresser som tas emot via ExpressRoute. Routning som tas emot via ExpressRoute är mer specifik och gör ExpressRoute till förstahandsvalet för autentiseringstrafik till Microsoft, vilket leder till asymmetrisk routning.

    - Om du vill använda ExpressRoute för autentisering måste du annonsera offentliga IP-adresser för ADFS via ExpressRoute utan NAT. Det gör att trafiken från Microsoft till den lokala AD FS-servern går via ExpressRoute, och returtrafik från kunden till Microsoft använder ExpressRoute eftersom den vägen prioriteras framför Internet. 

2. Källbaserad NAT

    Ett annat sätt att lösa problem med asymmetriska routning är att använda källbaserad NAT (SNAT). Ett exempel är om du inte har annonserat den offentliga IP-adressen för den lokala SMTP-servern via ExpressRoute och vill använda Internet för den här kommunikationen. En begäran som kommer från Microsoft till din lokala SMTP-server går via Internet. Du använder källbaserad NAT för att skicka den inkommande begäran till en intern IP-adress. Returtrafiken från SMTP-servern skickas genom gränsbrandväggen (som används för NAT) i stället för ExpressRoute. På så sätt skickas returtrafiken tillbaka via Internet. 


![Routing2](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## Identifiering av asymmetrisk routning

Traceroute är det bästa sättet att kontrollera att trafik skickas genom rätt väg. Om du förväntar dig att trafik från din lokala SMTP-server till Microsoft ska ta Internetvägen så använder du traceroute från SMTP-servern till Office 365. Resultatet bekräftar att trafiken verkligen lämnar ditt nätverk mot Internet och inte mot ExpressRoute. 





<!--HONumber=sep16_HO1-->


