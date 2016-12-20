---
title: Asymmetrisk routning | Microsoft Docs
description: "Den här artikeln beskriver problem som kan uppstå i samband med asymmetrisk routning i en kunds nätverk som har flera länkar till ett mål."
documentationcenter: na
services: expressroute
author: osamazia
manager: carmonm
editor: 
ms.assetid: a754bff9-95c9-44b5-9796-377fc21e8322
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: osamam
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 10166e6b8b9144f0a5dde4f2fc10b492120e85d7


---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Asymmetrisk routning med flera nätverksvägar
Den här artikeln förklarar hur utgående och återvändande nätverkstrafik kan ta olika vägar när det finns flera vägar tillgängliga mellan nätverkskällan och målet.

Det är viktigt att förstå två begrepp för att förstå asymmetrisk routning. Ett är effekten av flera nätverksvägar. Det andra är hur enheter, som en brandvägg, behåller tillstånd. Dessa typer av enheter kallas tillståndskänsliga enheter. En kombination av dessa två faktorer skapar scenarier där trafik ignoreras av en tillståndskänslig enhet eftersom den tillståndskänsliga enheten inte kunde identifiera att trafik hade sitt ursprung i själva enheten.

## <a name="multiple-network-paths"></a>Flera nätverksvägar
När ett företagsnätverk bara har en länk till Internet via sin Internetleverantör reser all trafik till och från Internet via samma väg. Företag köper ofta flera kanaler, som redundanta sökvägar, för att förbättra nätverkets aktiva tid. När detta sker, kan det hända att trafik går utanför nätverket, till Internet, via en länk, och den returnerade trafiken passerar via en annan länk. Detta kallas asymmetrisk routning. Vid omvänd nätverkstrafik tar asymmetrisk routning en annan väg från det ursprungliga flödet.

![Nätverk med flera sökvägar](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Även om det främst uppstår på Internet, gäller även asymmetrisk routning för andra kombinationer av flera vägar. Det gäller till exempel både för en Internet-väg och en privat väg som går till samma mål och för flera privata vägar till samma mål.

Varje router på vägen från källan till målet, beräknar den bästa vägen för att nå ett mål. Den bästa möjliga vägen fastställs av routern baserat på två huvudfaktorer:

* Routning mellan externa nätverk baseras på ett routningsprotokoll, Border Gateway Protocol (BGP). BGP tar annonser från grannar och kör dem via ett antal åtgärder för att avgöra den bästa vägen till det avsedda målet. Bästa vägen lagras i dess routningstabell.
* Längden på en nätmask som är associerad med en väg påverkar routningsvägarna. Om en router tar emot flera annonser för samma IP-adress men med olika nätmasker, föredrar routern annonsen med en längre nätmask eftersom den betraktas som en mer specifik väg.

## <a name="stateful-devices"></a>Tillståndskänsliga enheter
Routrar tittar på ett pakets IP-huvud för att bestämma routningen. Vissa enheter leta djupare i paketet. Normalt tittar enheterna på Layer4-huvuden (Transmission Control Protocol eller TCP; eller User Datagram Protocol eller UDP), eller Layer7-huvuden (Application Layer). Dessa typer av enheter är antingen säkerhetsenheter eller enheter för bandbreddsoptimering. 

En brandvägg är ett vanligt exempel på en tillståndskänslig enhet. En brandvägg tillåter eller nekar ett paket passage genom sina gränssnitt baserat på olika fält som protokoll, TCP/UDP-port och URL-huvuden. Den här nivån av paketinspektion placerar kraftig belastningen på enheten. För att förbättra prestanda inspekterar brandväggen det första paketet i ett flöde. Om paketet tillåts fortsätta, sparas flödesinformationen i tillståndstabellen. Alla efterföljande paket som är relaterade till detta flöde tillåts baserat på det första beslutet. Ett paket som är en del av ett befintligt flöde kan nå brandväggen. Om brandväggen inte har någon tidigare statusinformation om paketet, utelämnar brandväggen paketet.

## <a name="asymmetric-routing-with-expressroute"></a>Asymmetrisk routning med ExpressRoute
När du ansluter till Microsoft via Azure ExpressRoute, ändras nätverket så här:

* Du har flera länkar till Microsoft. En länk är din befintliga Internetanslutning och den andra är via ExpressRoute. En del trafik till Microsoft kan gå genom Internet men komma tillbaka via ExpressRoute eller tvärtom.
* Du får mer specifika IP-adresser via ExpressRoute. För trafik från ditt nätverk till Microsoft för tjänster som erbjuds via ExpressRoute föredrar routrar därför alltid ExpressRoute.

För att förstå den effekt som dessa två ändringar har på ett nätverk kan vi tänka oss vissa scenarier. Anta att du bara har en kanal till Internet och att du använder alla Microsofts tjänster via Internet. Trafiken från nätverket till Microsoft och tillbaka passerar samma Internetlänk och passerar genom brandväggen. Brandväggen registrerar flödet när den ser det första paketet och returpaket tillåts eftersom flödet finns i tillståndstabellen.

![Asymmetrisk routning med ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Sedan aktiverar du ExpressRoute och använder tjänster som erbjuds av Microsoft via ExpressRoute. Alla andra tjänster från Microsoft används via Internet. Du distribuerar en separat brandvägg vid din nätverksgräns som är ansluten till ExpressRoute. Microsoft annonserar mer specifika prefix till ditt nätverk via ExpressRoute för specifika tjänster. Din routningsinfrastruktur väljer ExpressRoute som primär väg för dessa prefix. Om du inte annonserar dina offentliga IP-adresser till Microsoft via ExpressRoute kommunicerar Microsoft med dina offentliga IP-adresser via Internet. Vidarebefordran av trafiken från nätverket till Microsoft görs via ExpressRoute och omvänd trafik från Microsoft görs via Internet. När brandväggen vid din nätverksgräns ser ett svarspaket för ett flöde som den inte hittar i tillståndstabellen ignorerar den returtrafiken.

Om du väljer att använda samma NAT-pool för ExpressRoute och för Internet ser du liknande problem med klienter i ditt nätverk på privata IP-adresser. Begäranden av tjänster som Windows Update går genom Internet eftersom IP-adresser för dessa tjänster inte annonseras via ExpressRoute. Returtrafiken kommer dock tillbaka via ExpressRoute. Om Microsoft tar emot en IP-adress med samma nätmask från Internet och ExpressRoute, prioriteras ExpressRoute framför Internet. Om en brandvägg eller en annan tillståndskänslig enhet vid din nätverksgräns och som använder ExpressRoute, inte har någon befintlig information om flödet, ignorerar den paket som hör till det flödet.

## <a name="asymmetric-routing-solutions"></a>Lösningar för asymmetrisk routning
Det finns två olika sätt att lösa problemet med asymmetriska routning. Ett är via routning och det andra är med hjälp av källbaserad NAT (SNAT).

### <a name="routing"></a>Routning
Se till att dina offentliga IP-adresser annonseras till relevanta WAN-länkar. Om du t.ex. vill använda Internet för autentiseringstrafik och ExpressRoute för e-posttrafik bör du inte annonseras din offentliga IP-adresser för Active Directory Federation Services (AD FS) över ExpressRoute. Se även till att exponera lokal AD FS-servern till IP-adresser som routern som tar emot via ExpressRoute. Routning som tas emot via ExpressRoute är mer specifik och gör ExpressRoute till förstahandsvalet för autentiseringstrafik till Microsoft. Detta leder till asymmetrisk routning.

Om du vill använda ExpressRoute för autentisering måste du annonsera offentliga IP-adresser för ADFS via ExpressRoute utan NAT. På det här sättet går trafik som kommer från Microsoft och går till lokal AD FS-servern över ExpressRoute. Returtrafik från kund till Microsoft använder ExpressRoute eftersom det är den prioriterade vägen via Internet.

### <a name="source-based-nat"></a>Källbaserad NAT
Ett annat sätt att lösa problem med asymmetrisk routning är att använda SNAT. Exempelvis har du inte annonserat den offentliga IP-adressen för en lokal SMTP-server över ExpressRoute eftersom du planerar att använda Internet för den här typen av kommunikation. En begäran som har sitt ursprung hos Microsoft och sedan går till den lokala SMTP-servern passerar via Internet. Du använder SNAT för att skicka den inkommande begäran till en intern IP-adress. Omvänd trafik från SMTP-servern går till gränsbrandväggen (som du använder för NAT) i stället för via ExpressRoute. Returtrafiken går tillbaka via Internet.

![Nätverkskonfiguration för källbaserad NAT](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Identifiering av asymmetrisk routning
Traceroute är det bästa sättet att kontrollera att nätverkstrafik skickas via rätt väg. Om du förväntar dig att trafik från din lokala SMTP-server till Microsoft ska ta Internetvägen så är förväntad traceroute från SMTP-servern till Office 365. Resultatet bekräftar att trafiken verkligen lämnar ditt nätverk mot Internet och inte mot ExpressRoute.




<!--HONumber=Dec16_HO1-->


