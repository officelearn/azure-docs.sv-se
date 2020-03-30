---
title: Företagsreferensarkitektur för Azure DevTest Labs
description: Den här artikeln innehåller referensarkitekturvägledning för Azure DevTest Labs i ett företag.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132841"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Referensarkitektur för Azure DevTest Labs för företag
Den här artikeln innehåller referensarkitektur som hjälper dig att distribuera en lösning baserad på Azure DevTest Labs i ett företag. Den innehåller följande:
- Lokal anslutning via Azure ExpressRoute
- En fjärrskrivbordsgateway för att fjärrinteckna på virtuella datorer
- Anslutning till en artefaktdatabas för privata artefakter
- Andra PaaS-tjänster som används i laboratorier

![Diagram över referensarkitektur](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arkitektur
Dessa är de viktigaste inslagen i referensarkitekturen:

- **Azure Active Directory (Azure AD)**: DevTest Labs använder [Azure AD-tjänsten för identitetshantering](../active-directory/fundamentals/active-directory-whatis.md). Tänk på dessa två viktiga aspekter när du ger användarna åtkomst till en miljö baserat på DevTest Labs:
    - **Resurshantering**: Det ger åtkomst till Azure-portalen för att hantera resurser (skapa virtuella datorer, skapa miljöer; starta, stoppa, starta om, ta bort och tillämpa artefakter och så vidare). Resurshantering görs i Azure med hjälp av rollbaserad åtkomstkontroll (RBAC). Du tilldelar roller till användare och anger resurs- och åtkomstnivåbehörigheter.
    - **Virtuella datorer (nätverksnivå)**: I standardkonfigurationen använder virtuella datorer ett lokalt administratörskonto. Om det finns en domän tillgänglig ([Azure AD Domain Services](../active-directory-domain-services/overview.md), en lokal domän eller en molnbaserad domän) kan datorer anslutas till domänen. Användare kan sedan använda sina domänbaserade identiteter för att ansluta till de virtuella datorerna.
- **Lokal anslutning**: I vårt arkitekturdiagram används [ExpressRoute.](../expressroute/expressroute-introduction.md) Men du kan också använda en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Även om ExpressRoute inte krävs för DevTest Labs, är det vanligt förekommande i företag. ExpressRoute krävs endast om du behöver åtkomst till företagets resurser. Vanliga scenarier är:
    - Du har lokala data som inte kan flyttas till molnet.
    - Du föredrar att ansluta till labbets virtuella datorer till den lokala domänen.
    - Du vill tvinga all nätverkstrafik in och ut ur molnmiljön via en lokal brandvägg för säkerhet/efterlevnad.
- **Nätverkssäkerhetsgrupper:** Ett vanligt sätt att begränsa trafiken till molnmiljön (eller inom molnmiljön) baserat på käll- och mål-IP-adresser är att använda en [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md). Du vill till exempel bara tillåta trafik som kommer från företagsnätverket till labbets nätverk.
- **Gateway för fjärrskrivbord:** Företag blockerar vanligtvis utgående fjärrskrivbordsanslutningar i företagets brandvägg. Det finns flera alternativ för att aktivera anslutning till den molnbaserade miljön i DevTest Labs, inklusive:
  - Använd en [fjärrskrivbordsgateway](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)och tillåt den statiska IP-adressen för gatewaybelastningsutjämnaren.
  - [Dirigera all inkommande RDP-trafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) via ExpressRoute/site-to-site VPN-anslutningen. Den här funktionen är en vanlig faktor när företag planerar en DevTest Labs-distribution.
- **Nätverkstjänster (virtuella nätverk, undernät)**: [Azure-nätverkstopologin](../networking/networking-overview.md) är ett annat nyckelelement i arkitekturen DevTest Labs. Den styr om resurser från labbet kan kommunicera och ha tillgång till lokala och internet. Vårt arkitekturdiagram innehåller de vanligaste sätten som kunder använder DevTest Labs: Alla labb ansluter via [virtuell nätverks peering](../virtual-network/virtual-network-peering-overview.md) med hjälp av en [hub-spoke-modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) till ExpressRoute/site-to-site VPN-anslutning till lokala. Men DevTest Labs använder Azure Virtual Network direkt, så det finns inga begränsningar för hur du konfigurerar nätverksinfrastrukturen.
- **DevTest Labs:** DevTest Labs är en viktig del av den övergripande arkitekturen. Mer information om tjänsten finns i [Om DevTest Labs](devtest-lab-overview.md).
- **Virtuella datorer och andra resurser (SaaS, PaaS, IaaS)**: Virtuella datorer är en viktig arbetsbelastning som DevTest Labs stöder tillsammans med andra Azure-resurser. DevTest Labs gör det enkelt och snabbt för ett företag att ge åtkomst till Azure-resurser (inklusive virtuella datorer och andra Azure-resurser). Läs mer om åtkomst till Azure för [utvecklare](devtest-lab-developer-lab.md) och [testare](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden
Även om DevTest Labs inte har inbyggda kvoter eller begränsningar, har andra Azure-resurser som används i den vanliga driften av ett labb [kvoter på prenumerationsnivå](../azure-resource-manager/management/azure-subscription-service-limits.md). Så i en typisk företagsdistribution behöver du flera Azure-prenumerationer för att täcka en stor distribution av DevTest Labs. De kvoter som företagen oftast når är:

- **Resursgrupper**: I standardkonfigurationen skapar DevTest Labs en resursgrupp för varje ny virtuell dator, eller så skapar användaren en miljö med hjälp av tjänsten. Prenumerationer kan innehålla [upp till 980 resursgrupper](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). Så, det är gränsen för virtuella datorer och miljöer i en prenumeration. Det finns två andra konfigurationer som du bör tänka på:
    - **[Alla virtuella datorer går till samma resursgrupp:](resource-group-control.md)** Även om den här inställningen hjälper dig att uppfylla resursgruppsgränsen påverkar den gränsen för resurstyp per resursgrupp.
    - **Använda delade offentliga IPs:** Alla virtuella datorer av samma storlek och region går in i samma resursgrupp. Den här konfigurationen är en "medelväg" mellan resursgruppskvoter och resurs-typ-per-resursgruppskvoter, om de virtuella datorerna tillåts ha offentliga IP-adresser.
- **Resurser per resursgrupp per resurstyp**: Standardgränsen för [resurser per resursgrupp per resurstyp är 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  När du använder *alla virtuella datorer går till samma resursgruppskonfiguration,* når användarna den här prenumerationsgränsen mycket tidigare, särskilt om de virtuella datorerna har många extra diskar.
- **Lagringskonton**: Ett labb i DevTest Labs levereras med ett lagringskonto. Azure-kvoten för [antal lagringskonton per region och prenumeration är 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Det maximala antalet DevTest Labs i samma region är också 250.
- **Rolltilldelningar**: En rolltilldelning är hur du ger en användare eller huvuduppgift till en resurs (ägare, resurs, behörighetsnivå). I Azure finns det en [gräns på 2 000 rolltilldelningar per prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits). Som standard skapar devtest labs-tjänsten en resursgrupp för varje virtuell dator. Ägaren beviljas *ägarbehörighet* för devtest labs-vm och *läsarbehörighet* till resursgruppen. På så sätt använder varje ny virtuell dator som du skapar två rolltilldelningar utöver de tilldelningar som används när du ger användarna behörighet till labbet.
- **API läser/skriver:** Det finns olika sätt att automatisera Azure och DevTest Labs, inklusive REST API:er, PowerShell, Azure CLI och Azure SDK. Genom automatisering kan du nå en annan gräns för API-begäranden: Varje prenumeration tillåter upp till [12 000 läsbegäranden och 1 200 skrivbegäranden per timme](../azure-resource-manager/management/request-limits-and-throttling.md). Var medveten om den här gränsen när du automatiserar DevTest Labs.

## <a name="manageability-considerations"></a>Överväganden för hantering
DevTest Labs har ett bra administrativt användargränssnitt för att arbeta med ett enda labb. Men i ett företag har du förmodligen flera Azure-prenumerationer och många labb. För att göra ändringar konsekvent i alla dina labb krävs skript/automatisering. Här är några exempel och metodtips för bästa hantering för en DevTest Labs-distribution:

- **Ändringar i labbinställningar:** Ett vanligt scenario är att uppdatera en specifik labbinställning i alla labb i distributionen. En ny VM-instansstorlek är till exempel tillgänglig och alla labb måste uppdateras för att tillåta den. Det är bäst att automatisera dessa ändringar med hjälp av PowerShell-skript, CLI- eller REST-API:er.  
- **Artefaktdatabasens personliga åtkomsttoken**: Vanligtvis upphör personliga åtkomsttoken för en Git-lagringsplats att gälla om 90 dagar, ett år eller två år. För att säkerställa kontinuitet är det viktigt att utöka den personliga åtkomsttoken eller skapa en ny. Använd sedan automatisering för att tillämpa den nya personliga åtkomsttoken på alla labb.
- **Begränsa ändringar till en labbinställning**: Ofta måste en viss inställning begränsas (t.ex. tillåta användning av marketplace-bilder). Du kan använda Azure Policy för att förhindra ändringar i en resurstyp. Du kan också skapa en anpassad roll och ge användare den rollen i stället för *ägarrollen* för labbet. Du kan göra detta för de flesta inställningar i labbet (intern support, labbmeddelande, tillåtna VM-storlekar och så vidare).
- **Kräv att virtuella datorer följer en namngivningskonvention:** Chefer vill ofta enkelt identifiera virtuella datorer som ingår i en molnbaserad utvecklings- och testmiljö. Du kan göra detta med hjälp av [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Det är viktigt att notera att DevTest Labs använder underliggande Azure-resurser som hanteras på samma sätt: nätverk, diskar, beräkning och så vidare. Azure-principen gäller till exempel för virtuella datorer som skapas i ett labb. Azure Security Center kan rapportera om vm-efterlevnad. Och Azure Backup-tjänsten kan tillhandahålla regelbundna säkerhetskopior för de virtuella datorerna i labbet.

## <a name="security-considerations"></a>Säkerhetsöverväganden
Azure DevTest Labs använder befintliga resurser i Azure (beräkning, nätverk och så vidare). Så det drar automatiskt nytta av de säkerhetsfunktioner som är inbyggda i plattformen. Om du till exempel vill att inkommande fjärrskrivbordsanslutningar endast ska komma från företagsnätverket lägger du bara till en nätverkssäkerhetsgrupp i det virtuella nätverket på fjärrskrivbordsgatewayen. Den enda ytterligare säkerhetsövervägande är nivån på de behörigheter som du beviljar till gruppmedlemmar som använder labben på en daglig basis. De vanligaste behörigheterna är [ *ägare* och *användare*](devtest-lab-add-devtest-user.md). Mer information om dessa roller finns [i Lägga till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [Skala upp din Azure DevTest Labs-infrastruktur](devtest-lab-guidance-scale.md).
