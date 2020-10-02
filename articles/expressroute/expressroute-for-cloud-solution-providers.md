---
title: ExpressRoute för molnlösningsleverantörer – Azure | Microsoft Docs
description: Den här artikeln innehåller information för molnlösningsleverantörer som vill inkludera Azure-tjänster och ExpressRoute i sina erbjudanden.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 17b8fc3824fb1c7e6cfcfc3d4333dc226b51724d
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653646"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute för Cloud Solution Providers (CSP)
Microsoft tillhandahåller storskaliga tjänster för traditionella återförsäljare och distributörer (CSP), för att de snabbt ska kunna etablera nya tjänster och lösningar för sina kunder utan att behöva investera i att utveckla dessa nya tjänster. Om du vill ge CSP:n (Cloud Solution Provider) möjlighet att direkt hantera dessa nya tjänster, erbjuder Microsoft program och API:er som tillåter CSP:n att hantera Microsoft Azure-resurser åt dina kunder. En av resurserna är ExpressRoute. Med ExpressRoute kan CSP:n ansluta befintliga kundresurser till Azure-tjänster. ExpressRoute är en höghastighets anslutning med privat kommunikation till tjänster i Azure. 

ExpressRoute består av ett par kretsar för hög tillgänglighet som är kopplade till en enskild kunds prenumeration (er) och inte kan delas av flera kunder. Varje krets bör avslutas i en annan router för att bibehålla den höga tillgängligheten.

> [!NOTE]
> Det finns bandbredds- och anslutningsfunktioner i ExpressRoute, vilket innebär att stora/komplexa implementeringar kräver flera ExpressRoute-kretsar för en enda kund.
> 
> 

Microsoft Azure tillhandahåller ett växande antal tjänster som du kan erbjuda dina kunder. ExpressRoute hjälper dig och dina kunder att dra nytta av dessa tjänster genom att tillhandahålla snabb fördröjning med låg fördröjning i Microsoft Azures miljön.

## <a name="microsoft-azure-management"></a>Microsoft Azure-hantering
Microsoft tillhandahåller kryptografiproviders med API: er för att hantera Azures kund prenumerationer genom att tillåta programmerings integrering med dina egna tjänst hanterings system. Hanteringsfunktioner som stöds finns [här](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure-resurshantering
Avtalet med kunden som avgör hur prenumerationen ska hanteras. CSP:n kan direkt hantera skapande och underhåll av resurser, eller kunden kan behålla kontrollen över Microsoft Azure-prenumerationen och skapa sådana Azure-resurser som de behöver. Om kunden hanterar skapandet av resurser i sin Microsoft Azure-prenumeration använder de en av två modeller: "*Anslut via*"-modellen eller "*direkt till"-* modellen. Dessa modeller beskrivs i detalj i följande avsnitt.  

### <a name="connect-through-model"></a>Anslut via-modellen
![Diagram som visar "Connect-through"-modellen.](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

CSP:n skapar i Anslut via-modellen en direkt anslutning mellan ditt datacenter och kundens Azure-prenumeration. Direktanslutningen görs med hjälp av ExpressRoute, som ansluter nätverket med Azure. Därefter ansluter kunden till nätverket. Det här scenariot kräver att kunden passerar via CSP-nätverket för att få åtkomst till Azure-tjänsterna. 

Om din kund har andra Azure-prenumerationer som inte hanteras av dig, skulle de använda det offentliga Internet eller sin egen privata anslutning för att ansluta till de tjänster som etablerades under prenumerationen som inte är KRYPTOGRAFIPROVIDER. 

För CSP som hanterar Azure-tjänster förutsätts att CSP: n har ett tidigare etablerat kund identitets lager, som sedan replikeras till Azure Active Directory för hantering av sin CSP-prenumeration genom att administrera på uppdrag av (ADMINISTRATE). Viktiga driv rutiner för det här scenariot omfattar var en viss partner eller tjänst leverantör har en etablerad relation med kunden. kunden förbrukar leverantörs tjänsterna för närvarande eller så har partnern en önskan att tillhandahålla en kombination av lösningar som värd tjänster och Azure-värdbaserade lösningar för att ge flexibilitet och åtgärda kund utmaningar som inte kan uppfyllas av en KRYPTOGRAFIPROVIDER ensam rätt. Den här modellen illustreras i **bilden** nedan.

![Diagram som visar ett detaljerat scenario för "Connect-through"-modellen.](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Anslut till-modellen
![Diagram som visar "Anslut till"-modellen.](./media/expressroute-for-cloud-solution-providers/connect-to.png)

I Anslut till-modellen skapar tjänstleverantören en direkt anslutning mellan sina kunders datacenter och den CSP-etablerade Azure-prenumerationen med hjälp av ExpressRoute via kundernas nätverk.

> [!NOTE]
> För ExpressRoute skulle kunden annars behöva skapa och underhålla ExpressRoute-kretsen.  
> 
> 

Det här anslutnings scenariot kräver att kunden ansluter direkt via ett kund nätverk för att få åtkomst till CSP-hanterad Azure-prenumeration, med en direkt nätverks anslutning som skapas, ägs och hanteras helt eller delvis av kunden. För dessa kunder förutsätts att leverantören för närvarande inte har ett kund identitets lager och att leverantören skulle hjälpa kunden att replikera sin aktuella identifierings lagring till Azure Active Directory för hantering av prenumerationen via ADMINISTRATE. Viktiga faktorer för det här scenariot är där en viss partner eller en tjänstleverantör har en etablerad relation med kunden, kunden använder tjänsterna för närvarande eller partnern har en önskan att tillhandahålla tjänster som enbart baseras på Azure-värdbaserade lösningar utan behov av ett befintligt leverantörsdatacenter eller en infrastruktur.

![Diagram som visar ett detaljerat scenario för "Anslut till"-modellen.](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Valen mellan dessa två alternativ baseras på kundens behov och ditt nuvarande behov av att tillhandahålla Azure-tjänster. Information om dessa modeller och associerad rollbaserad åtkomststyrning, nätverk och identitetsdesignmönster beskrivs i informationen i följande länkar:

* **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)** – RBAC baseras på Azure Active Directory.  Mer information om Azure RBAC finns [här](../role-based-access-control/role-assignments-portal.md).
* **Nätverk** – Beskriver olika delar av nätverk i Microsoft Azure.
* **Azure Active Directory (Azure AD)** – Azure AD tillhandahåller identitets hantering för Microsoft Azure och SaaS-program från tredje part. Mer information om Azure AD finns [här](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Nätverkshastigheter
ExpressRoute stöder nätverks hastigheter från 50 MB/s till 10 GB/s. Detta innebär att kunderna kan köpa den mängd bandbredd som krävs för deras unika miljö.

> [!NOTE]
> Nätverkets bandbredd kan ökas vid behov utan att avbryta kommunikationen, men för att minska nätverkets hastighet måste man bryta ner kretsen och återskapa den med en lägre nätverkshastighet.  
> 
> 

ExpressRoute stöder anslutning av flera vNets till en enda ExpressRoute-krets för att uppnå bättre användning av snabbare anslutningar. En enda ExpressRoute-krets kan delas av flera Azure-prenumerationer som ägs av samma kund.

## <a name="configuring-expressroute"></a>Konfigurera ExpressRoute
ExpressRoute kan konfigureras för att stödja tre typer av trafik ([routningsdomäner](#expressroute-routing-domains)) över en enskild ExpressRoute-krets. Den här trafiken är indelad i Microsoft-peering, offentlig Azure-peering och privat peering. Du kan välja att en eller alla typer av trafik skickas via en enda ExpressRoute-krets eller använda flera ExpressRoute-kretsar, beroende på storleken på ExpressRoute-kretsen och den isolering som krävs av kunden. Kundens säkerhetsposition kanske inte tillåter offentlig och privat trafik som passerar över samma krets.

### <a name="connect-through-model"></a>Anslut via-modellen
I en anslutnings konfiguration är du ansvarig för alla nätverks underfästningar för att ansluta dina kund Data Center resurser till de prenumerationer som finns i Azure. Varje kund som vill använda Azure-funktioner måste ha sin egen ExpressRoute-anslutning, som kommer att hanteras av dig. Du använder samma metoder som kunden skulle använda för att skaffa ExpressRoute-kretsen. Du kan följa samma steg som beskrivs i artikeln [ExpressRoute-arbetsflöden](expressroute-workflows.md) för kretsetablering och kretstillstånd. Du kommer sedan konfigurera BGP-vägar (Border Gateway Protocol) för att styra den trafik som passerar mellan det lokala nätverket och Azure vNet.

### <a name="connect-to-model"></a>Anslut till-modellen
I en Anslut till-konfiguration har kunden redan en befintlig anslutning till Azure eller kommer att initiera en anslutning till Internetleverantören med en länk till ExpressRoute från kundens eget datacenter direkt till Azure, i stället för ditt datacenter. För att påbörja etableringen kommer kunden följa stegen som beskrivs i Anslut via-modellen ovan. När kretsen har upprättats måste kunden konfigurera de lokala routrarna för att få åtkomst till både nätverket och Azure vNets.

Du kan hjälpa till med att konfigurera anslutningen och vägarna så att resurserna i ditt datacenter kan kommunicera med klientens resurser i ditt datacenter, eller med de resurser som finns i Azure.

## <a name="expressroute-routing-domains"></a>ExpressRoute-routningsdomäner
ExpressRoute erbjuder tre routningsdomäner: offentliga, privata och Microsoft-peering. Varje routningsdomän konfigureras med identiska routrar i en aktiv-aktiv konfiguration för hög tillgänglighet. Mer information om ExpressRoute-routningsdomänerna finns [här](expressroute-circuit-peerings.md).

Du kan definiera anpassade vägfilter för att endast tillåta de vägar som du vill tillåta eller som du behöver. Mer information eller om du vill se hur du gör dessa ändringar finns i artikeln: [Skapa och ändra routning för en ExpressRoute-krets med PowerShell](expressroute-howto-routing-classic.md) för mer information om routningsfilter.

> [!NOTE]
> Anslutningar med Microsoft-peering och offentlig peering måste ske via en offentlig IP-adress som ägs av kunden eller CSP:n och som följer alla definierade regler. Mer information finns på sidan [ExpressRoute-förutsättningar](expressroute-prerequisites.md).  
> 
> 

## <a name="routing"></a>Routning
ExpressRoute ansluter till Azure-nätverk via Azures virtuella nätverksgateway. Nätverksgateways innehåller routning för Azures virtuella nätverk.

När virtuella Azure-nätverk skapas, skapas även en standardroutningstabell för vNet som dirigerar trafiken till och från undernäten i vNet. Om standardvägtabellen inte är tillräcklig för lösningen kan anpassade vägar skapas för att dirigera utgående trafik till anpassad utrustning, eller för att blockera vägar till specifika undernät eller externa nätverk.

### <a name="default-routing"></a>Standardroutning
Standardvägtabellen innehåller följande vägar:

* Routning i ett undernät
* ”Undernät-till-undernät” i det virtuella nätverket
* Till Internet
* ”Virtuella nätverk-till-virtuella nätverk” via VPN-gateway
* ”Virtuella nätverk-till-lokala nätverk” via en VPN- eller ExpressRoute-gateway

![Diagram som visar standard alternativen för routning.](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Användardefinierad routning
Med användardefinierade vägar tillåts kontroll av utgående trafik från det tilldelade undernätet till andra undernät i det virtuella nätverket, eller över någon annan fördefinierad gateway (ExpressRoute, Internet eller VPN). Routningstabellens standardsystem kan ersättas med en användardefinierad routningstabell som ersätter standardroutningstabellen med anpassade vägar. Med användardefinierad routning kan kunderna skapa specifika vägar till exempelvis brandväggar eller intrångsidentifieringutrustning, eller blockera åtkomst till specifika undernät från det undernät som är värd för den användardefinierade vägen. En översikt över användardefinierade vägar hittar du [här](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Säkerhet
Beroende på vilken modell som används, Anslut till eller Anslut via, definierar kunden säkerhetsprinciperna i sitt vNet eller anger säkerhetsprincipkraven till CSP:n som definierar sina vNets. Följande säkerhetskriterier kan definieras:

1. **Kundisolering** – Azure-plattformen ger kundisolering genom att lagra information om kund-ID och vNet i en skyddad databas, som används för att kapsla in varje kunds trafik i en GRE-tunnel.
2. Regler i **nätverkssäkerhetsgruppen (NSG)** används för att definiera tillåten trafik till och från undernät i vNets i Azure. Som standard innehåller NSG spärr regler som blockerar trafik från Internet till det virtuella nätverket och tillåter regler för trafik i ett vNet. Mer information om nätverks säkerhets grupper hittar du [här](https://azure.microsoft.com/blog/network-security-groups/).
3. **Tvingad tunneltrafik** – Det här är ett alternativ för att omdirigera Internetbunden trafik med ursprung i Azure över ExpressRoute-anslutningen till ett lokalt datacenter. Mer information om tvingad tunneltrafik finns [här](expressroute-routing.md#advertising-default-routes).  
4. **Kryptering** – Även om ExpressRoute-kretsar har reserverats för en viss kund finns möjligheten att nätverksprovidern kan angripas, vilket innebär att en inkräktare kan få åtkomst till pakettrafiken. För att åtgärda det här potentiella problemet kan en kund eller CSP kryptera trafik via anslutningen, genom att definiera principer för IPSec-tunnelläge för all trafik som passerar mellan lokala resurser och Azure-resurser (se valfritt IPSec-tunnelläge för Kund 1 i Figur 5: ExpressRoute-säkerhet ovan). Det andra alternativet är att använda en brandväggsenhet på varje slutpunkt i ExpressRoute-kretsen. Detta kräver ytterligare virtuella brand väggar/apparater från tredje part som ska installeras i båda ändar för att kryptera trafiken över ExpressRoute-kretsen.

![alternativ text](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Nästa steg
Med Cloud Solution Provider-tjänsten kan du öka värdet för kunderna utan att behöva dyr infrastruktur och kapacitetsinköp, samtidigt som du bibehåller din position som primär outsourcing-provider. Den sömlösa integreringen med Microsoft Azure kan utföras via CSP:ns API, så att du kan integrera hanteringen av Microsoft Azure i dina befintliga hanteringsramverk.  

Mer information finns i följande länkar:

[Azure i Cloud Solution Provider-program](https://docs.microsoft.com/azure/cloud-solution-provider).  
[Bli redo att agera som en Cloud Solution Provider](https://partner.microsoft.com/solutions/cloud-reseller-pre-launch).  
[Microsoft Cloud Solution Provider-resurser](https://partner.microsoft.com/solutions/cloud-reseller-resources).
