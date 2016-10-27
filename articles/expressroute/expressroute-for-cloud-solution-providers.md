<properties
   pageTitle="Azure ExpressRoute för Cloud Solution Providers | Microsoft Azure"
   description="Den här artikeln innehåller information om Cloud Solution Providers som vill inkludera Azure-tjänster och ExpressRoute i sina erbjudanden."
   documentationCenter="na"
   services="expressroute"
   authors="richcar"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="richcar"/>


# <a name="expressroute-for-cloud-solution-providers-(csp)"></a>ExpressRoute för Cloud Solution Providers (CSP)

Microsoft tillhandahåller storskaliga tjänster för traditionella återförsäljare och distributörer (CSP), för att de snabbt ska kunna etablera nya tjänster och lösningar för sina kunder utan att behöva investera i att utveckla dessa nya tjänster. Om du vill ge CSP:n (Cloud Solution Provider) möjlighet att direkt hantera dessa nya tjänster, erbjuder Microsoft program och API:er som tillåter CSP:n att hantera Microsoft Azure-resurser åt dina kunder. En av resurserna är ExpressRoute. Med ExpressRoute kan CSP:n ansluta befintliga kundresurser till Azure-tjänster. ExpressRoute är en privat kommunikationslänk med hög hastighet till tjänster i Azure. 

ExpressRoute består av ett par kretsar för hög tillgänglighet som är kopplade till en enskild kundprenumeration och som inte kan delas av flera kunder. Varje krets bör avslutas i en annan router för att bibehålla den höga tillgängligheten.

>[AZURE.NOTE] Det finns bandbredds- och anslutningsfunktioner i ExpressRoute, vilket innebär att stora/komplexa implementeringar kräver flera ExpressRoute-kretsar för en enda kund.

Microsoft Azure tillhandahåller ett växande antal tjänster som du kan erbjuda dina kunder.  För att bäst dra nytta av dessa tjänster krävs ExpressRoute-anslutningar som ger åtkomst med hög hastighet och låg latens till Microsoft Azure-miljön.

## <a name="microsoft-azure-management"></a>Microsoft Azure-hantering
Microsoft erbjuder API:er till CSP:er för att de ska kunna hantera kundprenumerationer på Azure, genom att tillåta att programmässig integrering sker med dina egna tjänsthanteringssystem. Hanteringsfunktioner som stöds finns [här](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure-resurshantering
Det är ditt avtal med kunden som avgör hur prenumerationen ska hanteras. CSP:n kan direkt hantera skapande och underhåll av resurser, eller kunden kan behålla kontrollen över Microsoft Azure-prenumerationen och skapa sådana Azure-resurser som de behöver. Om kunderna hanterar skapandet av resurser i sin Microsoft Azure-prenumeration, använder de en av två modeller: ”Anslut via” eller ”Direkt till”. Dessa modeller beskrivs i detalj i följande avsnitt.  

### <a name="connect-through-model"></a>Anslut via-modellen

![alternativ text](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

CSP:n skapar i Anslut via-modellen en direkt anslutning mellan ditt datacenter och kundens Azure-prenumeration. Direktanslutningen görs med hjälp av ExpressRoute, som ansluter nätverket med Azure. Därefter ansluter kunden till nätverket. Det här scenariot kräver att kunden passerar via CSP-nätverket för att få åtkomst till Azure-tjänsterna. 

Om kunden har andra Azure-prenumerationer som inte hanteras av dig, använder de Internet eller sin privata anslutning för att ansluta till de tjänster som etablerats med den prenumeration som inte hör till CSP:n. 

För CSP-hanterade Azure-tjänster förutsätts att CSP:n har ett tidigare upprättat kundidentitetsarkiv som ska replikeras till Azure Active Directory för hantering av CSP-prenumerationen via AOBO (Administrate-On-Behalf-Of). Viktiga faktorer för det här scenariot är där en viss partner eller en tjänstleverantör har en etablerad relation med kunden, kunden använder tjänsterna för närvarande eller partnern har en önskan att tillhandahålla en kombination av leverantörsvärdbaserade och Azure-värdbaserade lösningar som ger flexibilitet och hanterar de kundbehov som inte enbart uppfylls av CSP:n. Den här modellen illustreras i **figuren** nedan.

![alternativ text](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Anslut till-modellen

![alternativ text](./media/expressroute-for-cloud-solution-providers/connect-to.png)

I Anslut till-modellen skapar tjänstleverantören en direkt anslutning mellan sina kunders datacenter och den CSP-etablerade Azure-prenumerationen med hjälp av ExpressRoute via kundernas nätverk.

>[AZURE.NOTE] För ExpressRoute skulle kunden annars behöva skapa och underhålla ExpressRoute-kretsen.  

Det här anslutningsscenariot kräver att kunden ansluter direkt via ett kundnätverk för att få åtkomst till den CSP-hanterade Azure-prenumerationen, med hjälp av en direkt nätverksanslutning som skapas, ägs och hanteras helt eller delvis av kunden. För dessa kunder förutsätts att leverantören inte har upprättat något kundidentitetsarkiv och att leverantören ska hjälpa kunden att replikera deras aktuella identitetsarkiv till Azure Active Directory för hantering av prenumerationen via AOBO. Viktiga faktorer för det här scenariot är där en viss partner eller en tjänstleverantör har en etablerad relation med kunden, kunden använder tjänsterna för närvarande eller partnern har en önskan att tillhandahålla tjänster som enbart baseras på Azure-värdbaserade lösningar utan behov av ett befintligt leverantörsdatacenter eller en infrastruktur.

![alternativ text](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Valet mellan dessa två alternativ baseras på dina kunders behov och ditt aktuella behov att tillhandahålla Azure-tjänster. Information om dessa modeller och associerad rollbaserad åtkomststyrning, nätverk och identitetsdesignmönster beskrivs i informationen i följande länkar:
-   **Rollbaserad åtkomstkontroll (RBAC)** – RBAC baseras på Azure Active Directory.  Läs mer om Azure RBAC [här](../active-directory/role-based-access-control-configure.md).
-   **Nätverk** – Beskriver olika delar av nätverk i Microsoft Azure.
-   **Azure Active Directory (AAD)** – AAD innehåller identitetshantering för Microsoft Azure och SaaS-program från tredje part. Mer information om Azure AD finns [här](https://azure.microsoft.com/documentation/services/active-directory/).  


## <a name="network-speeds"></a>Nätverkshastigheter
ExpressRoute stöder nätverkshastigheter från 50 Mb/s till 10 Gb/s. Detta innebär att kunderna kan köpa den mängd bandbredd som krävs för deras unika miljö.

>[AZURE.NOTE] Nätverkets bandbredd kan ökas vid behov utan att avbryta kommunikationen, men för att minska nätverkets hastighet måste man bryta ner kretsen och återskapa den med en lägre nätverkshastighet.  

ExpressRoute stöder anslutning av flera vNets till en enda ExpressRoute-krets för att uppnå bättre användning av snabbare anslutningar. En enda ExpressRoute-krets kan delas av flera Azure-prenumerationer som ägs av samma kund.

## <a name="configuring-expressroute"></a>Konfigurera ExpressRoute
ExpressRoute kan konfigureras för att stödja tre typer av trafik ([routningsdomäner](#ExpressRoute-routing-domains)) över en enskild ExpressRoute-krets. Den här trafiken är indelad i Microsoft-peering, offentlig Azure-peering och privat peering. Du kan välja att en eller alla typer av trafik skickas via en enda ExpressRoute-krets eller använda flera ExpressRoute-kretsar, beroende på storleken på ExpressRoute-kretsen och den isolering som krävs av kunden. Kundens säkerhetsposition kanske inte tillåter offentlig och privat trafik som passerar över samma krets.

### <a name="connect-through-model"></a>Anslut via-modellen
I en Anslut via-konfiguration ansvarar du för alla nätverksassociationer som ansluter kundernas datacenterresurser till de prenumerationer som finns i Azure. Varje kund som vill använda Azure-funktioner måste ha sin egen ExpressRoute-anslutning, som kommer att hanteras av dig. Du använder samma metoder som kunden skulle använda för att skaffa ExpressRoute-kretsen. Du kan följa samma steg som beskrivs i artikeln [ExpressRoute-arbetsflöden](./expressroute-workflows.md) för kretsetablering och kretstillstånd. Du kommer sedan konfigurera BGP-vägar (Border Gateway Protocol) för att styra den trafik som passerar mellan det lokala nätverket och Azure vNet.

### <a name="connect-to-model"></a>Anslut till-modellen
I en Anslut till-konfiguration har kunden redan en befintlig anslutning till Azure eller kommer att initiera en anslutning till Internetleverantören med en länk till ExpressRoute från kundens eget datacenter direkt till Azure, i stället för ditt datacenter. För att påbörja etableringen kommer kunden följa stegen som beskrivs i Anslut via-modellen ovan. När kretsen har upprättats måste kunden konfigurera de lokala routrarna för att få åtkomst till både nätverket och Azure vNets.

Du kan hjälpa till med att konfigurera anslutningen och vägarna så att resurserna i ditt datacenter kan kommunicera med klientens resurser i ditt datacenter, eller med de resurser som finns i Azure.

## <a name="expressroute-routing-domains"></a>ExpressRoute-routningsdomäner
ExpressRoute erbjuder tre routningsdomäner: offentliga, privata och Microsoft-peering. Var och en av routningsdomänerna är konfigurerade med identiska routrar i en aktiv-aktiv konfiguration för hög tillgänglighet. Mer information om ExpressRoute-routningsdomänerna finns [här](./expressroute-circuit-peerings.md).

Du kan definiera anpassade vägfilter för att endast tillåta de vägar som du vill tillåta eller som du behöver. Mer information eller om du vill se hur du gör dessa ändringar finns i artikeln: [Skapa och ändra routning för en ExpressRoute-krets med PowerShell](./expressroute-howto-routing-classic.md) för mer information om routningsfilter.

>[AZURE.NOTE] Anslutningar med Microsoft-peering och offentlig peering måste ske via en offentlig IP-adress som ägs av kunden eller CSP:n och som följer alla definierade regler. Mer information finns på sidan [ExpressRoute-förutsättningar](expressroute-prerequisites.md).  

## <a name="routing"></a>Routning
ExpressRoute ansluter till Azure-nätverk via Azures virtuella nätverksgateway. Nätverksgateways innehåller routning för Azures virtuella nätverk.

När virtuella Azure-nätverk skapas, skapas även en standardroutningstabell för vNet som dirigerar trafiken till och från undernäten i vNet. Om standardvägtabellen inte är tillräcklig för lösningen kan anpassade vägar skapas för att dirigera utgående trafik till anpassad utrustning, eller för att blockera vägar till specifika undernät eller externa nätverk.

### <a name="default-routing"></a>Standardroutning
Standardvägtabellen innehåller följande vägar:

- Routning i ett undernät
- ”Undernät-till-undernät” i det virtuella nätverket
- Till Internet
- ”Virtuella nätverk-till-virtuella nätverk” via VPN-gateway
- ”Virtuella nätverk-till-lokala nätverk” via en VPN- eller ExpressRoute-gateway

![alternativ text](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-(udr)"></a>Användardefinierad routning
Med användardefinierade vägar tillåts kontroll av utgående trafik från det tilldelade undernätet till andra undernät i det virtuella nätverket, eller över någon annan fördefinierad gateway (ExpressRoute, Internet eller VPN). Routningstabellens standardsystem kan ersättas med en användardefinierad routningstabell som ersätter standardroutningstabellen med anpassade vägar. Med användardefinierad routning kan kunderna skapa specifika vägar till exempelvis brandväggar eller intrångsidentifieringutrustning, eller blockera åtkomst till specifika undernät från det undernät som är värd för den användardefinierade vägen. En översikt över användardefinierade vägar finns [här](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Säkerhet
Beroende på vilken modell som används, Anslut till eller Anslut via, definierar kunden säkerhetsprinciperna i sitt vNet eller anger säkerhetsprincipkraven till CSP:n som definierar sina vNets. Följande säkerhetskriterier kan definieras:

1.  **Kundisolering** – Azure-plattformen ger kundisolering genom att lagra information om kund-ID och vNet i en skyddad databas, som används för att kapsla in varje kunds trafik i en GRE-tunnel.
2.  Regler i **nätverkssäkerhetsgruppen (NSG)** används för att definiera tillåten trafik till och från undernät i vNets i Azure. Som standard innehåller NSG blockeringsregler som blockerar trafiken från Internet till vNet och tillåter regler för trafik i ett vNet. Mer information om nätverkssäkerhetsgrupper finns [här](https://azure.microsoft.com/blog/network-security-groups/)
3.  **Tvingad tunneltrafik** – Det här är ett alternativ för att omdirigera Internetbunden trafik med ursprung i Azure över ExpressRoute-anslutningen till ett lokalt datacenter. Mer information om tvingad tunneltrafik finns [här](./expressroute-routing.md#advertising-default-routes).  

4.  **Kryptering** – Även om ExpressRoute-kretsar har reserverats för en viss kund finns möjligheten att nätverksprovidern kan angripas, vilket innebär att en inkräktare kan få åtkomst till pakettrafiken. För att åtgärda det här potentiella problemet kan en kund eller CSP kryptera trafik via anslutningen, genom att definiera principer för IPSec-tunnelläge för all trafik som passerar mellan lokala resurser och Azure-resurser (se valfritt IPSec-tunnelläge för Kund 1 i Figur 5: ExpressRoute-säkerhet ovan). Det andra alternativet är att använda en brandväggsenhet på varje slutpunkt i ExpressRoute-kretsen. Detta kräver att ytterligare en tredjepartsbrandväggs VM/utrustning installeras i bägge ändar för att kryptera trafiken över ExpressRoute-kretsen.


![alternativ text](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Nästa steg
Med Cloud Solution Provider-tjänsten kan du öka värdet för kunderna utan att behöva dyr infrastruktur och kapacitetsinköp, samtidigt som du bibehåller din position som primär outsourcing-provider. Den sömlösa integreringen med Microsoft Azure kan utföras via CSP:ns API, så att du kan integrera hanteringen av Microsoft Azure i dina befintliga hanteringsramverk.  

Mer information finns i följande länkar:

[Microsoft Cloud Solution Provider-program](https://partner.microsoft.com/en-US/Solutions/cloud-reseller-overview).  
[Bli redo att agera som en Cloud Solution Provider](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Microsoft Cloud Solution Provider-resurser](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).



<!--HONumber=Oct16_HO3-->


