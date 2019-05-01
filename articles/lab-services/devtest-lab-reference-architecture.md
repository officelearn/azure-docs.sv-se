---
title: Enterprise-Referensarkitektur för Azure DevTest Labs
description: Den här artikeln innehåller vägledning för referens arkitektur för Azure DevTest Labs i ett företag.
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
ms.openlocfilehash: 73a3d426e9040525b0c631db273e59c49a6a9eb0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705878"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs-Referensarkitektur för företag
Den här artikeln innehåller Referensarkitektur för att hjälpa dig att distribuera en lösning baserad på Azure DevTest Labs i ett företag. Det omfattar följande:
- Lokal anslutning via Azure ExpressRoute
- En fjärrskrivbordsgateway att logga in via fjärranslutning på virtuella datorer
- Anslutning till en artefaktcentrallagret för privata artefakter
- Andra PaaS-tjänster som används i labb

![Referens för Arkitekturdiagram](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arkitektur
Det här är de viktigaste elementen i referensarkitekturen:

- **Azure Active Directory (Azure AD)**: DevTest Labs använder den [Azure AD-tjänsten för Identitetshantering](../active-directory/fundamentals/active-directory-whatis.md). Överväg att dessa två viktiga aspekter när du ger användare åtkomst till en miljö, baserat på DevTest Labs:
    - **Resurshantering**: Det ger åtkomst till Azure portal för att hantera resurser (skapa virtuella datorer, skapa miljöer; starta, stoppa, starta om, ta bort, och tillämpa artefakter; och så vidare). Resurshantering görs i Azure med hjälp av rollbaserad åtkomstkontroll (RBAC). Du tilldela roller till användare och ange resurs- och åtkomst-behörighet.
    - **Virtuella datorer (på nätverksnivå)**: I standardkonfigurationen, virtuella datorer för att använda ett lokalt administratörskonto. Om det finns en domän ([Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), en lokal domän eller en molnbaserad domän), datorer kan kopplas till domänen. Användarna kan sedan använda domänbaserade identiteter för att ansluta till de virtuella datorerna.
- **Lokal anslutning**: I vår arkitekturdiagrammet [ExpressRoute](../expressroute/expressroute-introduction.md) används. Men du kan också använda en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Även om ExpressRoute krävs inte för DevTest Labs, används den ofta i företag. ExpressRoute krävs endast om du behöver åtkomst till företagets resurser. Vanliga scenarier är:
    - Du har lokala data som inte kan flyttas till molnet.
    - Du föredrar att ansluta virtuella labbdatorer till den lokala domänen.
    - Du vill tvinga all nätverkstrafik in och ut ur molnmiljö via en brandvägg för lokal säkerhet/efterlevnad.
- **Nätverkssäkerhetsgrupper**: Ett vanligt sätt att begränsa trafiken till molnmiljön (eller molnmiljön) baserat på källa och mål-IP-adresser är att använda en [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md). Till exempel att du vill tillåta endast trafik som kommer från företagsnätverket till den testmiljön nätverk.
- **Fjärrskrivbordsgateway**: Företag vanligtvis blockera utgående anslutningar till fjärrskrivbord på företagets brandvägg. Det finns flera alternativ för att aktivera anslutningar till molnbaserade miljön i DevTest Labs, inklusive:
  - Använd en [fjärrskrivbordsgateway](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture), och godkända statiska IP-adressen för gatewayen belastningsutjämnare.
  - [Dirigera alla inkommande RDP-trafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) via ExpressRoute/plats-till-plats-VPN-anslutningen. Den här funktionen är en vanliga överväganden när företag planera en distribution för DevTest Labs.
- **Nätverkstjänster (virtuella nätverk, undernät)**: Den [Azure-nätverk](../networking/networking-overview.md) topologin är en annan nyckelfaktor i DevTest Labs-arkitekturen. Den kontrollerar om resurser från labbet kan kommunicera och få åtkomst till lokala och internet. Vår arkitekturdiagrammet innehåller de vanligaste sätten som kunder använder Labb: Alla labs ansluta via [virtuell nätverkspeering](../virtual-network/virtual-network-peering-overview.md) med hjälp av en [modell av typen hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) på ExpressRoute/plats-till-plats VPN-anslutning till lokalt. Men DevTest Labs använder Azure Virtual Network direkt, så det finns inga begränsningar för hur du ställer in nätverksinfrastrukturen.
- **DevTest Labs**:  DevTest Labs är en viktig del av den övergripande arkitekturen. Mer information om tjänsten finns [om DevTest Labs](devtest-lab-overview.md).
- **Virtuella datorer och andra resurser (SaaS, PaaS, IaaS)**:  Virtuella datorer är en viktig arbetsbelastning som har stöd för DevTest Labs tillsammans med andra Azure-resurser. DevTest Labs gör det enkelt och snabbt för företaget att ge åtkomst till Azure-resurser (inklusive virtuella datorer och andra Azure-resurser). Mer information om åtkomst till Azure för [utvecklare](devtest-lab-developer-lab.md) och [Testare](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden
Även om DevTest Labs saknar inbyggd kvoter eller begränsningar kan andra Azure-resurser som används i ett labb normala funktion har [prenumerationsnivå kvoter](../azure-subscription-service-limits.md). I en typisk företags-distribution måste därför flera Azure-prenumerationer som täcker en stor distribution för DevTest Labs. Kvoter som företag når oftast är:

- **Resursgrupper**: I standardkonfigurationen DevTest Labs skapas en resursgrupp för alla nya virtuella datorn eller användaren skapar en miljö med hjälp av tjänsten. Prenumerationer kan innehålla [upp till 980 resursgrupper](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). Det är gränsen på virtuella datorer och miljöer i en prenumeration. Det finns två andra konfigurationer som du bör tänka på:
    - **[Alla virtuella datorer går du till samma resursgrupp](resource-group-control.md)**: Även om den här konfigurationen hjälper dig att uppfylla gränsen för gruppen, påverkar resurs-typ-per-resource-group-gränsen.
    - **Med hjälp av delade offentliga IP-adresser**: Alla virtuella datorer av samma storlek och region går du till samma resursgrupp. Den här konfigurationen är en ”mellanting” mellan gruppen resurskvoter och resurs-typ-per-resource-group-kvoter, om de virtuella datorerna kan ha offentliga IP-adresser.
- **Resurser per resurs Gruppera efter resurstyp**: Standardgränsen för [resurser per resursgrupp per resurstypen är 800](../azure-subscription-service-limits.md#resource-group-limits).  När du använder den *alla virtuella datorer går du till samma resursgrupp* konfiguration, användare trycker på den här prenumerationen begränsa mycket snabbare, särskilt om de virtuella datorerna har många extra diskar.
- **Storage-konton**: Ett labb i DevTest Labs levereras med ett storage-konto. Azure-kvoten för [antalet storage-konton per region per prenumeration är 250](../azure-subscription-service-limits.md#storage-limits). Det maximala antalet DevTest Labs i samma region är också 250.
- **Rolltilldelningar**: En rolltilldelning är hur du ge användaren eller huvudnamn åtkomst till en resurs (ägare, resurs, behörighetsnivå). I Azure, det finns en [högst 2 000 rolltilldelningar per prenumeration](../azure-subscription-service-limits.md#role-based-access-control-limits). Som standard skapas en resursgrupp för varje virtuell dator i DevTest Labs-tjänsten. Ägare beviljas *ägare* behörighet för den virtuella datorn DevTest-labb och *läsare* behörighet till resursgruppen. På så vis använder varje ny virtuell dator som du skapar två rolltilldelningar utöver de tilldelningar som används när du ge användare behörighet till labbet.
- **API: et läsningar/skrivningar**: Det finns olika sätt att automatisera Azure och DevTest Labs, inklusive REST API: er, PowerShell, Azure CLI och Azure SDK. Automatiseringen kan du når en annan gränsen för API-begäranden: Varje prenumeration ger upp till [12 000 Läs begäranden och 1 200 skrivförfrågningar per timme](../azure-resource-manager/resource-manager-request-limits.md). Tänk på den här gränsen när du automatisera DevTest Labs.

## <a name="manageability-considerations"></a>Överväganden för hantering
DevTest Labs har en bra administrativa användargränssnitt för att arbeta med ett enda labb. Men i ett företag du sannolikt har flera Azure-prenumerationer och många labs. Konsekvent gör ändringar i dina labb kräver skript/automation. Här följer några exempel och metodtips för hantering för en DevTest Labs-distribution:

- **Ändringar i inställningarna för labb**: Ett vanligt scenario är att uppdatera en specifik labbmiljö i alla labs i distributionen. Till exempel en ny virtuell datorinstans-storlek är tillgänglig och alla labs måste uppdateras så att den. Det är bäst att automatisera dessa ändringar med hjälp av PowerShell-skript, CLI eller REST API: er.  
- **Personlig åtkomsttoken för artefakten databasen**:  Personlig åtkomsttoken för en Git-lagringsplats normalt går ut inom 90 dagar, ett, eller två år. För att säkerställa affärskontinuitet, är det viktigt att utöka din personliga åtkomsttoken eller skapa en ny. Använda automation för att tillämpa nya personliga åtkomsttoken på alla labs.
- **Begränsa ändringar i en labbmiljö**: En inställning måste ofta begränsas (till exempel tillåter användning av marketplace-avbildningar). Du kan använda Azure Policy för att förhindra att ändringar till en resurstyp. Du kan skapa en anpassad roll och tilldela användare rollen i stället för den *ägare* rollen för övningen. Du kan göra detta för de flesta inställningar i labbet (intern support, labb-meddelande, tillåtna storlekar för Virtuella datorer och så vidare).
- **Kräv virtuella datorer för att följa en namngivningskonvention**: Chefer vill ofta enkelt identifiera virtuella datorer som ingår i en molnbaserade utvecklings- och testmiljö. Du kan göra detta med hjälp av [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Det är viktigt att Observera att labb använder underliggande Azure-resurser som hanteras på samma sätt: nätverk, diskar, beräkning och så vidare. Till exempel gäller Azure Policy för virtuella datorer som skapas i ett labb. Azure Security Center kan rapportera på VM-kompatibilitet. Och Azure Backup-tjänsten kan ge regelbundna säkerhetskopieringar för virtuella datorer i labbet.

## <a name="security-considerations"></a>Säkerhetsöverväganden
Azure DevTest Labs använder befintliga resurser i Azure (beräkning, nätverk och så vidare). Så automatiskt dra nytta av säkerhetsfunktioner som är inbyggda i plattformen. Till exempel för att kräva inkommande anslutningar till fjärrskrivbord till kommer endast från företagsnätverket, Lägg bara till en nätverkssäkerhetsgrupp till det virtuella nätverket på fjärrskrivbordsgateway. Den enda ytterligare säkerhetsåtgärd är behörighetsnivån som du tilldelar till gruppmedlemmar som använder labs på basis dagliga. De vanligaste behörigheterna är [ *ägare* och *användaren*](devtest-lab-add-devtest-user.md). Mer information om de här rollerna finns i [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [Skala upp din Azure DevTest Labs-infrastruktur](devtest-lab-guidance-scale.md).
