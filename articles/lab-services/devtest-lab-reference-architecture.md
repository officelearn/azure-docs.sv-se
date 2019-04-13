---
title: Referensarkitektur för Azure DevTest Labs för ett företag
description: Den här artikeln innehåller vägledning för arkitektur en referens för Azure DevTest Labs i ett företag.
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
ms.openlocfilehash: 61e76369a4d73bd171c9e5c2462b3f261681ba00
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551389"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Azure DevTest Labs - Referensarkitektur för ett företag
Den här artikeln innehåller en Referensarkitektur för att distribuera en lösning baserad på Azure DevTest Labs i ett företag. Den innehåller en lokal anslutning via Express Route, en gateway för fjärrskrivbord till via en fjärranslutning loggar in på virtuella datorer, Anslut till en artefaktcentrallagret för privata artefakter och andra PaaS-tjänster som används i ett labb.

![Referensarkitektur](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arkitektur
De viktigaste elementen i referensarkitekturen är:

- **Azure Active Directory (AAD)**: Azure DevTest Labs använder den [Azure Active Directory-tjänst för Identitetshantering](../active-directory/fundamentals/active-directory-whatis.md). Det finns två viktiga saker att tänka på att erbjuda åtkomst till en miljö, baserat på DevTest Labs för användare:
    - **Resurshantering**:  Det ger åtkomst till Azure portal för att hantera resurser (skapa virtuella datorer, skapa miljöer, Starta/Stoppa/starta om/delete/applyartifacts och så vidare). Det är klart i Azure med Roble rollbaserad åtkomstkontroll (RBAC) och genom att använda en rolltilldelning för användaren, inställningen resurs och åtkomstbehörighet för nivån.
    - **Virtuella datorer (på nätverksnivå)**:  I standardkonfigurationen, virtuella datorer för att använda ett lokalt administratörskonto.  Om det finns en domän ([AAD Domain services](../active-directory-domain-services/active-directory-ds-overview.md), en lokal domän eller en molnbaserad domän), datorer kan kopplas till domänen. När ansluten, använder användare domänbaserade identiteter för att ansluta till de virtuella datorerna.
- **Lokal anslutning**: I arkitekturdiagrammet ovan, [Express Route](../expressroute/expressroute-introduction.md) i användas, men du kan också använda en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Även om det inte krävs för DevTest Labs, visas den ofta i företag. Det krävs endast om det finns en anledning för att ansluta till företagets resurser. Vanliga orsaker är: 
    - Lokala data som inte kan flyttas till molnet
    - Inställning för att ansluta virtuella labbdatorer till den lokala domänen
    - Tvinga all nätverkstrafik in och ut ur molnmiljö via en lokal brandvägg för säkerhet/efterlevnadsskäl
- **Nätverkssäkerhetsgrupper**: Ett vanligt sätt att begränsa trafiken till molnmiljön (eller molnmiljön) baserat på källa och mål-IP-adresser är att använda en [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md). Till exempel så att endast den nätverkstrafik som kommer från företagsnätverket till den testmiljön nätverk.
- **Fjärrskrivbordsgateway**:  Företag vanligtvis blockera utgående anslutningar till fjärrskrivbord på företagets brandvägg. Om du vill aktivera anslutningen till den molnbaserade miljön i DevTest Labs, det finns flera alternativ som att använda en [fjärrskrivbordsgateway](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture) (statisk IP-adress för belastningsutjämnaren på gateway-vitlistan) eller [dirigera all inkommande RDP-trafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) via Express Route/plats-till-plats VPN-anslutningen. Det är en vanliga överväganden när du planerar en distribution av DevTest Labs i företaget.
- **Azure-nätverk (virtuella nätverk, undernät)**:  Den [Azure-nätverk](../networking/networking-overview.md) topologin är en annan nyckelfaktor i den övergripande arkitekturen för DevTest Labs. Det gör det möjligt för resurser från labs kommunicera (eller inte), åtkomst till den lokala (eller inte) och åtkomst till internet (eller inte). Arkitekturdiagrammet innehåller det vanligaste sättet som kunder använder labb (alla labs som är anslutna via [VNet-Peering](../virtual-network/virtual-network-peering-overview.md) med hjälp av en [modell av typen hub-spoke](/architecture/reference-architectures/hybrid-networking/hub-spoke) på Express Route/plats-till-plats VPN-anslutning lokalt), men sedan DevTest Labs med hjälp av Azure Networking direkt det inte finns några begränsningar för hur du ställer in nätverksinfrastrukturen.
- **DevTest Labs**:  DevTest Labs är en viktig del av den övergripande arkitekturen. Mer information om tjänsten, se [om DevTest Labs](devtest-lab-overview.md).
- **Virtuella datorer och andra resurser (SaaS, PaaS, IaaS)**:  En av de viktiga arbetsbelastningar som stöds av DevTest Labs är virtuella datorer tillsammans med andra Azure-resurser.  DevTest Labs gör det enkelt och snabbt för företaget att ge åtkomst till Azure-resurser (inklusive virtuella datorer och andra Azure-resurser).  Mer information om åtkomst till Azure för [utvecklare](devtest-lab-developer-lab.md) och [Testare](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden
Även om DevTest Labs inte har någon inbyggd kvoter eller begränsningar, andra Azure-resurser som används i en typisk åtgärderna i ett labb behöver [prenumerationsnivå kvoter](../azure-subscription-service-limits.md). I en typisk företags-distribution måste du därför har flera Azure-prenumerationer som täcker en stor distribution för DevTest Labs. Kvoter som oftast nått som företag är:

- **Resursgrupper**:  I standardkonfigurationen skapar DevTest Labs en resursgrupp för varje ny virtuell dator eller en miljö som en användare skapar med hjälp av tjänsten. Prenumerationer kan innehålla en [högst 980 resursgrupper](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), så den här gränsen är den övre gränsen för virtuella datorer och miljöer i en prenumeration. Det finns två andra konfigurationer som du bör tänka på:
    - **[Alla virtuella datorer går du till samma resursgrupp](resource-group-control.md)**:  Även om det hjälper dig med gruppen resursgräns påverkar resurstypen per gräns för gruppen.
    - **Med hjälp av delade offentliga IP-adresser**:  Alla virtuella datorer av samma storlek och samma region går du till samma resursgrupp. Det är en ”mellanting' mellan gruppen resurskvoter och resurstypen per grupp resurskvoter om virtuella datorer ska kunna har offentliga IP-adresser. 
- **Resurser per resurs Gruppera per resurstypen**: Standardgränsen för [resurser per resurs grupp per resurstypen är 800](../azure-subscription-service-limits.md#resource-group-limits).  När via alla virtuella datorer går du till konfiguration i samma resursgrupp, överskrids användare den här prenumerationsgränsen mycket snabbare särskilt om de virtuella datorerna har många extra diskar.
- **Storage-konton**: Ett labb i DevTest Labs levereras med ett storage-konto och Azure-kvoten för [antalet storage-konton per region per prenumeration är 250](../azure-subscription-service-limits.md#storage-limits). Det innebär att den övre gränsen för antal DevTest Labs i samma region är 250.
- **Rolltilldelningar**: En rolltilldelning är att ge användaren eller huvudnamn åtkomst till en resurs (ägare, resurs, behörighetsnivå). I Azure, det en [högst 2 000 rolltilldelningar per prenumeration](../azure-subscription-service-limits.md#role-based-access-control-limits). DevTest Labs-tjänsten (i standardkonfigurationen) skapas en resursgrupp för varje virtuell dator och ägaren kommer att beviljas den **ägare** behörighet för DevTest Labs VM och **läsare** behörighet till den resursgrupp.  På så sätt kan använder varje ny virtuell dator skapas två ytterligare rolltilldelningar till rolltilldelningar som skapas när ge användare behörighet till labbet.
- **API: et läsningar/skrivningar**: Det finns olika sätt (REST API: er, PowerShell, CLI, Azure SDK och så vidare) för att automatisera Azure och labb och via automation är det möjligt att du har nått gränsen för en annan på API-begäranden. Varje prenumeration ger upp till [12000 läsa begäranden och 1200-skrivåtgärder per timme](../azure-resource-manager/resource-manager-request-limits.md).  Det finns en gräns ska vara medveten om när automatisera DevTest Labs.

## <a name="manageability-considerations"></a>Överväganden för hantering
DevTest Labs har en bra administrativa användargränssnitt när du arbetar med ett enda labb. I ett företag finns det troligen flera Azure-prenumerationer och många labs. Det innebär att göra ändringar i alla labs konsekvent kräver skript/automation.  Här följer några exempel och bästa metoder för att hantera DevTest Labs-distributionen:

- **Ändringar i inställningarna för labb**: Ett vanligt scenario är att uppdatera en specifik labbmiljö i alla labs i distributionen. Till exempel en ny virtuell datorinstans-storlek är tillgänglig och alla labs måste uppdateras så att den.  Det är bäst att automatisera dessa ändringar med hjälp av Azure PowerShell-skript, Azure CLI eller REST API: er.  
- **Personlig åtkomsttoken för artefakten databasen**:  Normalt gälla personlig åtkomsttoken för en Git-lagringsplats (90 dagar, 1 år, 2 år). För att säkerställa affärskontinuitet, är det viktigt att utöka din personliga åtkomsttoken eller skapa en ny och för att använda automation för att tillämpa nya personliga åtkomsttoken till alla övningar.
- **Begränsa ändringar till en labbmiljö**:  Det är ofta fallet där det finns en viss inställning (till exempel så att Marketplace-avbildningar som ska användas) som måste vara begränsad. Det kan göras via Azure policy (förhindrar ändringar till den resurstypen) eller genom att skapa en anpassad roll och tilldela rollen i stället för ”ägare” till labbet. Det kan göras för de flesta av inställningarna i labbet (intern Support, labb-meddelande, tillåtna storlekar och så vidare)
- **Kräver virtuella datorer följer en namngivningskonvention**: Det är en gemensam begäran om att enkelt identifiera virtuella datorer som ingår i Microsofts molnbaserade utvecklings- och testmiljö. Du kan göra det [med Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Det är viktigt att Observera att labb använder underliggande resurser i Azure (nätverk, diskar, beräkning, osv) som hanteras på samma sätt i Azure.  Till exempel gäller Azure policy för virtuella datorer som skapats i ett labb. I Azure Security center kan rapportera på VM-kompatibilitet. Azure Backup-tjänsten kan du ge regelbundna säkerhetskopieringar för virtuella datorer i labbet och så vidare. 

## <a name="security-considerations"></a>Säkerhetsöverväganden
Azure DevTest Labs använder befintliga resurser i Azure (beräkning, nätverk och så vidare) och därmed dra nytta automatiskt av alla bra säkerhetsfunktionerna inbyggda för plattformen. Till exempel om du vill skydda alla inkommande anslutningar till fjärrskrivbord till endast kommer från företagsnätverket, är det lika enkelt som att lägga till en nätverkssäkerhetsgrupp till det virtuella nätverket på fjärrskrivbordsgateway. Den enda ytterligare säkerhet bör tänka på vid Azure DevTest Labs är behörighetsnivån som angetts för gruppmedlemmar som kommer att använda labbarna på basis dagliga.  De vanliga behörigheter är [”ägare” och ”DevTest Labs-användare”](devtest-lab-add-devtest-user.md). Mer information om de här rollerna finns i [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [Skala upp din Azure DevTest Labs-infrastruktur](devtest-lab-guidance-scale.md)
