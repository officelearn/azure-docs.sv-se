---
title: Överväganden för allmänna datacenter-integrering för Azure Stack-integrerade system | Microsoft Docs
description: Lär dig vad du kan göra för att planera nu och förbereda för datacenter-integrering med flera noder Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: afb4e634b7e255ef8f2cfc84319029af7412372e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251885"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Överväganden för datacenter-integrering för integrerade Azure Stack-system
Om du är intresserad av i ett integrerat Azure Stack-system, bör du förstå några av stora planeringsöverväganden kring distribution och hur systemet passar in i ditt datacenter. Den här artikeln innehåller en översikt över dessa överväganden för att fatta viktiga infrastruktur för din Azure Stack-system med flera noder. Förstå dessa överväganden hjälper när du arbetar med maskinvaruleverantören OEM som de distribuerar Azure Stack till ditt datacenter.  

> [!NOTE]
> Azure Stack-system med flera noder kan endast köpas från auktoriserade maskinvarutillverkare. 

För att distribuera Azure Stack, måste du ge din lösningsleverantör planeringsinformationen innan distributionen startar kan processen gå snabbt och smidigt. Intervall upplysningar över nätverk, säkerhet och identitetsinformation med många viktiga beslut som kan kräva kunskap från många olika områden och beslutsfattare. Därför kan behöva du hämta personer från flera team i din organisation så att du har all nödvändig information som är redo innan du påbörjar distributionen. Det kan bidra till att kommunicera med maskinvaruleverantören vid insamling av den här informationen som de kan ha råd som är bra att fattar ditt beslut.

När du undersöker och samla in informationen som krävs, kan du behöva göra vissa före konfigurationsändringar i din nätverksmiljö. Detta kan omfatta reservation av IP-adressutrymmen för Azure Stack-lösningen och konfigurera dina routrar, växlar och brandväggar för att förbereda för anslutning till de nya Azure Stack-lösningen växlarna. Se till att ha ämne området expert justera upp till dig med din planering.

## <a name="capacity-planning-considerations"></a>Överväganden vid kapacitetsplanering
När du utvärderar en Azure Stack-lösning för inköp, måste maskinvara konfigurationsalternativ göras som har en direkt inverkan på den totala kapaciteten i deras Azure Stack-lösning. Dessa inkluderar klassiska val av CPU, minne densitet, lagringskonfiguration och övergripande lösningen skala (t.ex. antal servrar). Till skillnad från en traditionell virtualiseringslösning gäller inte det enkla aritmetiskt av dessa komponenter för att fastställa användbar kapacitet. Det första skälet är att Azure Stack har byggts för att vara värd för infrastruktur eller hantering av komponenterna i själva lösningen. Den andra orsaken är att några av lösningens kapaciteten är reserverad till stöd för återhämtning; uppdatering av lösningens programvara på ett sätt som minimerar avbrott i klienternas arbetsbelastningar. 

Den [Azure Stack kapacitet planner kalkylblad](https://aka.ms/azstackcapacityplanner) sätt att göra informerade beslut om att planera kapaciteten på två sätt: antingen den genom att välja ett erbjudande för maskinvara och försök att anpassa en kombination av resurser eller genom att definiera den arbetsbelastning som Azure Stack är avsedd att köras för att visa tillgänglig maskinvara SKU: er som har stöd för den. Slutligen är kalkylbladet avsedd som en vägledning för att fatta beslut relaterade till Azure Stack planering och konfigurering. 

Kalkylbladet är inte avsedd att fungera som en ersättning för undersökning och analys.  Microsoft lämnar inga garantier, uttryckliga eller underförstådda, avseende informationen i kalkylbladet.



## <a name="management-considerations"></a>Hanteringsanmärkningar
Azure Stack är ett förseglat system där infrastrukturen som är låsta både från en behörigheter och perspektiv. Nätverket åtkomstkontrollistor (ACL) används för att blockera all inkommande trafik för obehörig och all onödig kommunikation mellan infrastrukturkomponenter. På så sätt blir det svårare för obehöriga användare för åtkomst till systemet.

Det finns inga obegränsad administratörsåtkomst till infrastrukturen för daglig hantering och åtgärder. Azure Stack-operatörer måste hantera systemet via administratörsportalen eller via Azure Resource Manager (via PowerShell eller REST API). Det finns ingen åtkomst till systemet med andra hanteringsverktyg, till exempel Hyper-V Manager eller Klusterhanterare för växling vid fel. För att skydda systemet, kan inte programvara från tredje part (till exempel agenter) installeras i komponenterna i Azure Stack-infrastruktur. Samverkan med externa hanterings- och programvara som görs via PowerShell eller REST API.

När en högre nivå av åtkomst krävs för att felsöka problem som inte är löst avisering förmedling stegen, måste du samarbeta med Microsoft Support. Tack vare stöd kan finns det en metod för att ge tillfälliga fullständig administratörsåtkomst till systemet för att utföra mer avancerade åtgärder. 

## <a name="identity-considerations"></a>Överväganden för identitet

### <a name="choose-identity-provider"></a>Välj identitetsprovider
Du behöver tänka på vilken identitetsleverantör som du vill använda för Azure Stack-distribution, Azure AD eller AD FS. Du kan inte byta identitetsleverantörer efter distributionen utan omdistribution av hela systemet. Om du inte äger Azure AD-kontot och använder ett konto som du fått av din Molntjänstleverantör, och om du vill byta providern och använda en annan Azure AD-konto, nu behöver du kontakta leverantören för att distribuera lösningen f eller på dina kostnader.



Valfri identity-providern har inte någon bäring på virtuella maskiner, identitetssystem och konton som de använder, oavsett om de kan ansluta till en Active Directory-domän, osv. Det här är separat.

Du kan lära dig mer om hur du väljer en identitetsprovider i den [Azure Stack integrerade system anslutning modeller artikeln](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>AD FS och Graph-integrering
Om du väljer att distribuera Azure Stack med hjälp av AD FS som identitetsleverantör, måste du integrera i AD FS-instans på Azure Stack med en befintlig AD FS-instans via ett federationsförtroende. På så sätt kan identiteter i en befintlig Active Directory-skog för att autentisera med resurser i Azure Stack.

Du kan också integrera Graph-tjänsten i Azure Stack med befintliga Active Directory. På så sätt kan du hantera rollbaserad åtkomstkontroll (RBAC) i Azure Stack. När åtkomst till en resurs har delegerats kan söker komponenten Graph användarkonto i den befintliga Active Directory-skogen med LDAP-protokollet.

Följande diagram visar integrerad trafikflödet i AD FS och Graph.
![Diagram över AD FS och Graph trafikflöde](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Licensieringsmodell
Du måste bestämma vilka licensieringsmodell som du vill använda. De tillgängliga alternativen beror på om du distribuerar Azure Stack som är ansluten till internet:
- För en [anslutna distribution](azure-stack-connected-deployment.md), du kan välja antingen betalning som du-användning eller kapacitetsbaserad licensiering. Betalning som du-användning kräver en anslutning till Azure rapportera användning, som sedan faktureras via Azure commerce. 
- Endast kapacitetsbaserad licensiering stöds om du [distribuera frånkopplad](azure-stack-disconnected-deployment.md) från internet. 

Läs mer om licensieringsmodeller finns [Microsoft Azure Stack förpackning och priser](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Namnge beslut

Du måste tänka på hur du vill planera namnområdet Azure Stack, särskilt regionnamn och extern domän. Det externa fullständigt kvalificerade domännamnet (FQDN) för din Azure Stack-distribution för offentliga slutpunkter är en kombination av dessa två namn: &lt; *region*&gt;.&lt; *fqdn*&gt;. Till exempel *east.cloud.fabrikam.com*. Azure Stack-portaler skulle vara tillgängligt på följande URL: er i det här exemplet:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Regionnamn som du väljer för din Azure Stack-distribution måste vara unikt och kommer att visas i portalen adresser. 

I följande tabell sammanfattas dessa domän namngivning beslut.

| Name | Beskrivning | 
| -------- | ------------- | 
|Regionsnamn | Namnet på din första Azure Stack-region. Det här namnet används som en del av det fullständiga Domännamnet för de offentliga virtuella IP-adresser (VIP) som hanteras av Azure Stack. Normalt är områdesnamnet en identifierare för fysisk plats, till exempel en datacenterplats.<br><br>Region-namnet måste bestå av endast bokstäver och siffror mellan 0 – 9. Inga specialtecken som ”-” eller ”#” och så vidare tillåts.| 
| Externt domännamn | Namnet på zonen System DNS (Domain Name) för slutpunkter med utåtriktade virtuella IP-adresser. Används i det fullständiga Domännamnet för de här offentliga virtuella IP-adresser. | 
| Domännamn för privat (internt) | Namnet på domänen (och interna DNS-zon) skapade på Azure Stack för infrastrukturhantering. 
| | |

## <a name="certificate-requirements"></a>Certifikatkrav

För distribution måste du ange Secure Sockets Layer (SSL)-certifikat för offentliga slutpunkter. På en hög nivå har certifikat följande krav:

- Du kan använda en enda jokerteckencertifikat eller du kan använda en uppsättning dedikerade certifikat och använder jokertecken endast för slutpunkter, till exempel lagring och Key Vault.
- Certifikat kan utfärdas av en offentlig betrodd certifikatutfärdare (CA) eller en kundhanterad Certifikatutfärdare.

Mer information om vilka PKI certifikat krävs för att distribuera Azure Stack och hur du hämta dem finns i, [certifikatkrav för Azure Stack Public Key Infrastructure](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Informationen för PKI-certifikat ska användas som en allmän vägledning. Innan du skaffar några PKI-certifikat för Azure Stack fungera med din partner för OEM-maskinvara. De ger mer detaljerade riktlinjer och krav.


## <a name="time-synchronization"></a>Tidssynkronisering
Du måste välja en viss tid servern med används för att synkronisera Azure Stack.  Tid symbolization är viktigt att Azure Stack och dess infrastruktur-roller som används för att generera Kerberos-biljetter som används för att autentisera interna tjänster med varandra.

Du måste ange en IP-adress för synkroniseringsserver tid även om de flesta av komponenterna i infrastrukturen kan matcha URL: en, några stöder bara IP-adresser. Om du är med frånkopplade, måste du ange en tid-server i företagsnätverket som du är säker kan nås från nätverkets infrastruktur i Azure Stack.

## <a name="connect-azure-stack-to-azure"></a>Ansluta Azure Stack till Azure

För hybridmolnscenarierna måste du planera hur du vill ansluta Azure Stack till Azure. Det finns två sätt att ansluta virtuella nätverk i Azure Stack till virtuella nätverk i Azure: 
- **Plats-till-plats**. Ett virtuellt privat nätverk (VPN)-anslutning via IPsec (IKE v1 och IKE v2). Den här typen av anslutning kräver en VPN-enhet eller Routning och fjärråtkomst (RRAS). Mer information om VPN-gatewayer i Azure finns i [om VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Kommunikationen via den här tunnel krypteras och är säkra. Bandbredd är dock begränsat av maximalt dataflöde för tunneln (100 – 200 Mbit/s).
- **Utgående NAT**. Som standard är alla virtuella datorer i Azure Stack anslutna till externa nätverk via utgående NAT. Varje virtuellt nätverk som skapats i Azure Stack hämtar en offentlig IP-adress som tilldelats. Om den virtuella datorn direkt har tilldelats en offentlig IP-adress, eller är bakom en belastningsutjämnare med en offentlig IP-adress, har utgående åtkomst via utgående NAT med hjälp av VIP för det virtuella nätverket. Detta fungerar endast för kommunikation som initieras av den virtuella datorn och är avsedd för externa nätverk (internet eller intranätet). Det kan inte användas för att kommunicera med den virtuella datorn från utanför.

### <a name="hybrid-connectivity-options"></a>Alternativ för hybridanslutning

För hybridanslutning är det viktigt att tänka på vilken typ av distribution du vill erbjuda och där det ska distribueras. Du måste avgöra om du behöver isolera nätverkstrafik per klient, och om du har ett intranät eller internet-distribution.

- **Azure Stack för enstaka klientorganisationer**. Ett Azure Stack-distribution som ser ut, minst från ett nätverk perspektiv, som om det är en klient. Det kan finnas många klient-prenumerationer, men som valfri intranät-tjänst, överförs all trafik via samma nätverk. Nätverkstrafik från en prenumeration överförs via samma nätverksanslutning som en annan prenumeration och behöver inte vara isolerade via en krypterad tunnel.

- **Azure Stack för flera innehavare**. Ett Azure Stack-distributioner där varje klientprenumeration trafik som är bunden till nätverk som är externa för Azure Stack måste isoleras från andra klienter nätverkstrafik.
 
- **Användning i intranät**. Ett Azure Stack-distribution som är placerad i ett företags intranät, vanligtvis på privata IP-adressutrymme och bakom en eller flera brandväggar. Offentliga IP-adresser är inte helt offentlig eftersom de inte kan dirigeras direkt via det offentliga internet.

- **Distribution av Internet**. Ett Azure Stack-distribution som är anslutet till offentligt internet och använder internet-dirigerbara offentliga IP-adresser för det offentliga virtuella IP-adressintervallet. Distributionen kan fortfarande finns bakom en brandvägg, men det offentliga virtuella IP-adressintervallet som kan nås direkt från den offentliga internet och Azure.
 
I följande tabell sammanfattas hybridscenarier för anslutning, med experter, nackdelar och användningsfall.

| Scenario | Anslutningsmetod | Experter | Nackdelar | Bra för |
| -- | -- | --| -- | --|
| Enkel klient Azure Stack, intranät-distribution | Utgående NAT | Bättre bandbredd för överföring av snabbare. Enkel att implementera; Inga gatewayer som krävs. | Trafik som inte är krypterade; Ingen isolering eller -kryptering utanför stacken. | Enterprise-distributioner där alla klienter är lika tillförlitliga.<br><br>Företag som har en Azure ExpressRoute-krets till Azure. |
| Flera innehavare Azure Stack, intranät-distribution | Plats-till-plats-VPN | Trafik från klienten VNet till mål är säker. | Bandbredd begränsas av plats-till-plats VPN-tunnel.<br><br>Kräver en gateway i det virtuella nätverket och en VPN-enhet i målnätverket. | Enterprise-distributioner där vissa klienttrafik måste skyddas från andra klienter. |
| Enkel klient Azure Stack, internet-distribution | Utgående NAT | Bättre bandbredd för överföring av snabbare. | Trafik som inte är krypterade; Ingen isolering eller -kryptering utanför stacken. | Värdscenario där klienten hämtar sina egna Azure Stack-distribution och en dedikerad krets i Azure Stack-miljön. Till exempel ExpressRoute och Multiprotocol etikett växlar (MPLS).
| Flera innehavare Azure Stack, internet-distribution | Plats-till-plats-VPN | Trafik från klienten VNet till mål är säker. | Bandbredd begränsas av plats-till-plats VPN-tunnel.<br><br>Kräver en gateway i det virtuella nätverket och en VPN-enhet i målnätverket. | Värdscenario där providern vill erbjuda ett moln för flera innehavare, där klienterna inte litar på varandra och trafik måste vara krypterat.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Med ExpressRoute

Du kan ansluta Azure Stack till Azure via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) för både enda klient intranät- och scenarier med flera innehavare. Du behöver en etablerad ExpressRoute-krets via [en anslutningsprovider](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Följande diagram visar ExpressRoute för en enda klient-scenariot (där ”kundens anslutning” är ExpressRoute-kretsen).

![Diagram som visar enda klient ExpressRoute scenario](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Följande diagram visar ExpressRoute för flera klientorganisationer.

![Diagram som visar scenario med flera klientorganisationer ExpressRoute](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Externa övervakning
Att hämta en enda vy över alla aviseringar från dina Azure Stack-distributioner och enheter, och integrera aviseringar i befintliga IT service management-arbetsflöden för biljetter, kan du [integrera Azure Stack med externa datacenter övervakningslösningar](azure-stack-integrate-monitor.md).

Ingår i Azure Stack-lösningen, är maskinvara livscykel värden en dator utanför Azure Stack som kör OEM-tillverkarens hanteringsverktyg för maskinvara. Du kan använda dessa verktyg eller andra lösningar som integreras direkt med befintliga övervakningslösningar i ditt datacenter.

I följande tabell sammanfattas en lista över tillgängliga alternativ.

| Område | Externa lösningen för fjärrövervakning |
| -- | -- |
| Azure Stack-programvara | [Azure Stack-hanteringspaket för Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios-plugin-programmet](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST-baserad API-anrop | 
| Fysiska servrar (bmc via IPMI) | Maskinvara för OEM - hanteringspaket för Operations Manager-leverantör<br>OEM-tillverkarens maskinvarulösning<br>Maskinvaruleverantören Nagios plugin-program | OEM-partner stöds övervakningslösning (ingår) | 
| Nätverksenheter (SNMP) | Operations Manager identifiering av nätverksenheter<br>OEM-tillverkarens maskinvarulösning<br>Nagios växla plugin-programmet |
| Hälsoövervakning för klient-prenumeration | [System Center Management Pack för Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Observera följande krav:
- Lösningen som du använder måste vara utan Agent. Du kan inte installera agenter från tredje part i Azure Stack-komponenterna. 
- Om du vill använda System Center Operations Manager krävs Operations Manager 2012 R2 eller Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och haveriberedskap

Planera för säkerhetskopiering och haveriberedskap innebär att planera för både den underliggande Azure Stack-infrastruktur som är värd för IaaS-datorer och PaaS-tjänster och för klient-program och data. Du måste planera för dessa separat.

### <a name="protect-infrastructure-components"></a>Skydda infrastrukturkomponenter

Du kan [säkerhetskopiera Azure Stack](azure-stack-backup-back-up-azure-stack.md) infrastrukturkomponenter till en SMB dela som du anger:

- Du behöver en extern SMB-filresurs på en befintlig Windows-baserad filserver eller en tredje parts-enhet.
- Du bör använda samma resursen för säkerhetskopiering av nätverksväxlar och maskinvara livscykel värden. Maskinvaruleverantören OEM kan ge vägledning för säkerhetskopiering och återställning av dessa komponenter som de är externa för Azure Stack. Du är ansvarig för att köra säkerhetskopiering arbetsflöden baserat på OEM-leverantör rekommendation.

Om oåterkallelig dataförlust inträffar, kan du använda infrastruktur för säkerhetskopiering till reseed distributionsdata, till exempel distribution indata och identifierare, tjänstkonton Certifikatutfärdarens rotcertifikat, externa resurser (i frånkopplade distributioner), planer, erbjudanden, prenumerationer, kvoter, RBAC-principen och rollen uppgifter och Key Vault-hemligheter.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Skydda program för klienter på IaaS-datorer

Azure Stack inte tillbaka upp klient program och data. Du måste planera för säkerhetskopiering och disaster recovery-skydd till ett mål som är externa för Azure Stack. Klient-skydd är en klient-drivna aktivitet. För IaaS-datorer, kan klienterna använda på gäster tekniker som skyddar filmappar, programdata och systemtillstånd. Dock som en enterprise- eller service-providern, kan du erbjuda en säkerhetskopierings- och lösningen i samma datacenter eller externt i ett moln.

Om du vill säkerhetskopiera Linux eller Windows-IaaS-datorer måste du använda säkerhetskopiering produkter med åtkomst till gästoperativsystemet för att skydda fil, mapp, operating system-tillstånd och programdata. Du kan använda Azure Backup, System Center Data Center Protection Manager, eller produkter från tredje part som stöds.

För att replikera data till en sekundär plats och dirigera program redundans om en olycka inträffar, kan du använda Azure Site Recovery eller produkter som stöds från tredje part. Program som stöder intern replikering, som Microsoft SQL Server, kan också replikera data till en annan plats där programmet körs.

## <a name="learn-more"></a>Läs mer

- Information om användningsfall, inköp, partner och OEM maskinvaruleverantörer finns i den [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktsidan.
- Information om plan och geo-tillgänglighet för Azure Stack integrerade system finns i dokumentet: [Azure Stack: En utökning av Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Nästa steg
[Azure Stack-anslutning distributionsmodeller](azure-stack-connection-models.md)
