---
title: Vad är Azure Stack? | Microsoft Docs
description: Lär dig hur Azure Stack kan du köra Azure-tjänster i ditt datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631315"
---
# <a name="azure-stack-overview"></a>Azure Stack-översikt

Azure Stack är en utökning av Azure som gör det möjligt att köra program i en lokal miljö och leverera Azure-tjänster i ditt datacenter. Organisationer kan tryggt med en konsekvent plattform göra Teknikval baserat på affärsbehov, snarare än affärsbeslut baserat på tekniska begränsningar.

## <a name="why-use-azure-stack"></a>Varför använda Azure Stack?

Azure tillhandahåller en omfattande plattform för utvecklare att bygga moderna program. Men inför vissa molnbaserade program hinder, till exempel svarstider, tillfällig anslutning och förordningar. Låsa upp nya användningsfall för hybridmoln för både kunder och interna verksamhetsspecifika program Azure och Azure Stack:

- **Edge-lösningar och frånkopplade lösningar**. Hantera latens och anslutningsbegäranden genom att bearbeta data lokalt i Azure Stack och sedan aggregera dem den i Azure för vidare analys, med samma programlogik på båda. Du kan även distribuera Azure Stack som är ansluten till internet utan anslutning till Azure. Tänk på oavsett, gäller levereras och Mina axlar som exempel.

- **Molnprogram som uppfyller olika bestämmelser**. Utveckla och distribuera program i Azure med total flexibilitet för att distribuera lokalt med Azure Stack och uppnå föreskrifter och policyer, utan kodändringar behövs. Programmet exempel är globala granskning, ekonomisk rapportering, valutaväxling handel, onlinespel och Utgiftsrapportering.

- **Molnbaserade program modellen lokalt**. Använda Azure-tjänster, utan server, behållare och arkitekturer för mikrotjänster för att uppdatera och utöka befintliga program eller skapa nya. Använd konsekvent DevOps-processer i Azure i molnet och Azure Stack lokalt för att påskynda programmodernisering för viktiga verksamhetskritiska program.

Azure Stack kan dessa Användningsscenarier genom att tillhandahålla:

- En integrerad leverans för att komma igång och snabbt igång med ändamålsspecifika Azure Stack integrerade system från betrodda maskinvarupartner. Efter leverans integreras enkelt Azure Stack i datacenter med övervakning via System Center Operations Manager-hanteringspaket eller Nagios-tillägget.

- Flexibla Identitetshantering med Azure Active Directory (Azure AD) för Azure och Azure Stack hybridmiljöer och utnyttjar Active Directory Federation Services (AD FS) för att kopplas bort distributioner. 

- En Azure-konsekventa programutvecklingsmiljö att maximera produktivitet för utvecklare och aktivera vanliga DevOps närmar sig i hybridmiljöer.

- Leverans av Azure-tjänster från på plats med hybrid molnbaserad databearbetning. Anta samma driftrutiner i Azure och Azure Stack för att distribuera och hantera Azure IaaS och PaaS-tjänster med samma administrativa upplevelser och verktyg som Azure. Microsoft levererar kontinuerliga innovationer i Azure till Azure Stack, inklusive nya Azure-tjänster, uppdateringar av befintliga tjänster och ytterligare Azure Marketplace-program och bilder.

## <a name="azure-stack-architecture"></a>Azure Stack-arkitektur
Azure Stack integrerade system består i rack med 4 – 16 servrar som skapats av betrodda maskinvarupartners och levereras direkt till ditt datacenter. Efter leverans fungerar en leverantör av lösningar med dig för att distribuera det integrerade systemet och kontrollera Azure Stack-lösning som uppfyller företagets krav. Du måste förbereda ditt datacenter genom att se till att alla nödvändiga ström och kylning, kantlinje anslutning och andra nödvändiga datacenter integration krav är uppfyllda. 

> Läs mer om Azure Stack-datacenter integrationsmiljö [datacenter-integrering med Azure Stack](azure-stack-customer-journey.md).

Azure Stack bygger på branschledande standardsupport för maskinvaran och hanteras med hjälp av samma verktyg som du redan använder för att hantera Azure-prenumerationer. Därför kan tillämpa du konsekventa DevOps-processer om du är ansluten till Azure eller inte. 

Arkitekturen i Azure Stack kan du ange Azure-tjänster på gränsen för fjärranslutna platser eller tillfällig anslutning, ansluten till internet. Du kan skapa hybridlösningar som bearbetar data lokalt i Azure Stack och sammanställa den på Azure för ytterligare bearbetning och analyser. Eftersom Azure Stack är installerade på plats, kan du slutligen uppfyller särskilda regler eller princip krav möjlighet att distribuera cloud program lokalt utan att ändra någon kod. 

## <a name="deployment-options"></a>Distributionsalternativ

### <a name="production-or-evaluation-environments"></a>Produktions-eller utvärdering
Azure Stack finns två alternativ för att uppfylla dina behov, integrerade Azure Stack-system för produktion och Azure Stack Development Kit (ASDK) för att utvärdera Azure Stack:

- **Integrerade Azure Stack-system**. Azure Stack integrerade system som erbjuds via ett partnerskap med Microsoft och maskinvara partner, skapa en lösning som erbjuder molnet självstudiekurs i innovation och databehandling management enkelhet. Eftersom Azure Stack erbjuds som ett integrerat maskinvaru- och programvarusystem får du den flexibilitet och kontroll du behöver och även möjligheten att förnya från molnet. Azure Stack-integrerat system i intervallet från 4 – 16 noder och stöds gemensamt av maskinvara partner och Microsoft. Med Azure Stack-integrerade system kan du skapa nya scenarier och distribuera nya lösningar för dina produktionsarbetsbelastningar.

- **Azure Stack Development Kit**. Den [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) är en kostnadsfri, nod distribution av Azure Stack som du kan använda för att utvärdera och lära dig om Azure Stack. Du kan också använda ASDK som en miljö för att skapa appar med hjälp av API: er och verktyg som är förenligt med Azure. ASDK är inte avsett att användas som en produktionsmiljö och har följande begränsningar jämfört med produktionsdistributionen fullständigt integrerade system:

    - ASDK kan bara associeras med en enda Azure Active Directory (AD Azure) eller en identitetsprovider för Active Directory Federation Services (AD FS).
    - Eftersom Azure Stack-komponenterna distribueras på en enda värddator, finns det begränsad fysiska resurser för klientens resurser. Den här konfigurationen är inte avsedd att skala eller prestanda.
    - Scenarier för nätverk är begränsade på grund av värddator och NIC-distributionskrav.

### <a name="connection-models"></a>Anslutningen modeller
Du kan välja att distribuera Azure Stack antingen **anslutna** till internet (och till Azure) eller **frånkopplad** från den. Det här alternativet definierar vilka alternativ som är tillgängliga för din Identitetslagret (Azure AD eller AD FS) och faktureringsmodell (betalar du användning-baserade fakturerings- eller kapacitetsbaserad fakturering).

> Mer information finns i överväganden för [anslutna](azure-stack-connected-deployment.md) och [frånkopplad](azure-stack-disconnected-deployment.md) distributionsmodeller. 

### <a name="identity-provider"></a>Identitetsprovider 
Azure Stack använder Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) som en identitetsleverantör för att upprätta identiteter för Azure Stack. 

> [!IMPORTANT]
> Det här är en viktig beslutspunkten! Välja Azure AD eller AD FS som identitetsprovider är ett enstaka beslut som du måste göra vid tidpunkten för distribution. Du kan inte ändra detta senare utan att distribuera hela systemet.

Azure AD är Microsofts molnbaserade, flera innehavare identitetsprovider. De flesta hybridscenarier med internet-anslutna distributioner kan du använda Azure AD som Identitetslagret. Du kan dock välja att använda Active Directory Federation Services (AD FS) för frånkopplade distributioner av Azure Stack. Azure Stack-resursprovidrar och andra program fungerar ungefär på samma sätt med AD FS som de gör med Azure AD. Azure Stack innehåller en egen Active Directory-instans och en Active Directory Graph API. 

> Du kan läsa mer om Azure Stack identitet överväganden vid [översikt över identitet för Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Hur hanteras Azure Stack?
När Azure Stack har distribuerats i en distribution av integrerade system eller en ASDK installation, är de huvudsakliga sätt att interagera med Azure Stack-administrationsportalen, användarportalen och PowerShell. Azure Stack-portaler backas var och en av separata instanser av Azure Resource Manager. En **Azure Stack-operatör** använder administrationsportalen för att hantera Azure Stack, och för att exempelvis skapa klient erbjudanden och underhåll av hälsotillstånd och övervaka statusen för det integrerade systemet. Användarportalen (kallas även klientportalen) tillhandahåller en Självbetjäning för användning av molnresurser, t.ex. virtuella datorer, lagringskonton och web apps. 

> Mer information om hur du hanterar Azure Stack med hjälp av portalen för enhetsadministration finns i den [snabbstarten för Azure Stack-administration](azure-stack-manage-portals.md).

Som en Azure Stack-operatör kan du leverera en mängd olika tjänster och program, till exempel [virtuella datorer](azure-stack-tutorial-tenant-vm.md), [webbprogram](azure-stack-app-service-overview.md)med hög tillgänglighet [SQL Server](azure-stack-tutorial-sql.md), och [MySQL-Server](azure-stack-tutorial-mysql.md) databaser. Du kan också använda [Azure Resource Manager-mallar för Azure Stack-Snabbstart](https://github.com/Azure/AzureStack-QuickStart-Templates) att distribuera SharePoint och Exchange. 

Administrationsportalen kan du [konfigurera Azure Stack för att leverera tjänster](azure-stack-plan-offer-quota-overview.md) för klienter som använder planer, kvoter, erbjudanden och prenumerationer. Klientanvändare kan prenumerera på flera erbjudanden. Erbjudanden kan ha en eller flera planer och planer kan ha en eller flera tjänster. Operatörer kan du också hantera kapacitet och svara på aviseringar. 

När Azure Stack konfigureras en **Azure Stack-användare** (kallas även en klient) använder tjänster som erbjuder operatorn. Användare kan etablera, övervaka och hantera tjänster som de som prenumererar på, t.ex web apps, lagring och virtuella datorer.

> Mer information om hur du hanterar Azure Stack, inklusive vad konton för att använda where-, vanliga operatorn ansvarsområden, vad du ska berätta för dina användare och få hjälp, granska [grundläggande om administration av Azure Stack](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Resursproviders 
Resursproviders är webbtjänster som utgör grunden för alla Azure Stack IaaS och PaaS-tjänster. Azure Resource Manager är beroende av olika resursprovidrar att ge åtkomst till tjänster. Varje resursprovider hjälper dig att konfigurera och styra dess respektive resurser. Administratörer kan också lägga till nya anpassade resursprovidrar. 

### <a name="foundational-resource-providers"></a>Grundläggande resursprovidrar 
Det finns tre grundläggande IaaS-resursprovidrar: Beräkning, nätverk och lagring:

- **Compute**. Compute-Resursprovidern gör att Azure Stack-klienter kan skapa sina egna virtuella datorer. Compute-Resursprovidern omfattar möjligheten att skapa virtuella datorer samt virtuella datorer, tillägg. Tjänsten VM-tillägget ger IaaS-funktioner för Windows och Linux-datorer.  Exempelvis kan du använda Compute-Resursprovidern att etablera en Linux-dator och kör Bash-skript under distributionen för att konfigurera den virtuella datorn.
- **Nätverks-Resursprovidern**. Nätverkets Resursprovider tillhandahåller en serie funktioner för Programvarudefinierad nätverksfunktion (SDN) och nätverk funktionen virtualisering (NFV) för det privata molnet. Du kan använda den Provider för nätverksresurser för att skapa resurser som programvarubelastningsutjämnare, offentliga IP-adresser, nätverkssäkerhetsgrupper och virtuella nätverk.
- **Lagringsresursprovidern**. Lagringsresursprovidern levererar fyra Azure-konsekventa lagringstjänster: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [kö](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabell](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage), och KeyVault kontohantering att tillhandahålla hantering och granskning av hemligheter som lösenord och certifikat. Lagringsresursprovidern erbjuder även en storage administration molntjänst för att underlätta administration av tjänsten Azure-konsekventa Storage-tjänster. Azure Storage tillhandahåller flexibilitet för att lagra och hämta stora mängder Ostrukturerade data, till exempel dokument och mediefiler med Azure Blobs och strukturerade NoSQL-baserade data med Azure-tabeller. 

### <a name="optional-resource-providers"></a>Valfritt resursprovidrar
Det finns tre valfritt PaaS resursprovidrar som du kan distribuera och använda med Azure Stack: Resursproviders för App Service, SQL Server och MySQL-Server:

- **App Service**. [Azure App Service i Azure Stack](azure-stack-app-service-overview.md) finns en platform-as-a-service (PaaS) som erbjuds med Microsoft Azure till Azure Stack. Tjänsten gör det möjligt för dina interna eller externa kunder att skapa webbappar, API och Azure Functions-program för valfri plattform eller enhet. 
- **SQL Server**. Använd den [SQL Server-resursleverantör](azure-stack-sql-resource-provider.md) att erbjuda SQL-databaser som en tjänst i Azure Stack. När du har installerat resursprovidern och ansluter den till en eller flera SQL Server-instanser, kan dig och dina användare skapa databaser för molnbaserade appar, webbplatser som använder SQL och andra arbetsbelastningar som använder SQL.
- **MySQL Server**. Använd den [MySQL Server-resursleverantör](azure-stack-mysql-resource-provider-deploy.md) att exponera MySQL-databaser som en tjänst för Azure Stack. MySQL-resursprovider körs som en tjänst på en Windows Server 2016 Server Core-dator (VM).

## <a name="providing-high-availability"></a>Tillhandahåller hög tillgänglighet
För att uppnå hög tillgänglighet för ett system för produktion av flera virtuella datorer i Azure virtuella datorer är placerade i en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) som sprider ut dem över flera feldomäner och uppdateringsdomäner. I mindre skala i Azure Stack definieras en feldomän i en tillgänglighetsuppsättning som en nod i skalningsenheten.  

Infrastrukturen i Azure Stack är redan elastisk vid fel, medför den underliggande tekniken (redundanskluster) fortfarande vissa avbrott för virtuella datorer på en fysisk server som påverkas om det uppstår ett maskinvarufel. Azure Stack har stöd för att ha en tillgänglighetsuppsättning med högst tre feldomäner för att överensstämma med Azure.

- **Feldomäner**. Virtuella datorer placeras i en tillgänglighetsuppsättning kommer att fysiskt isolerade från varandra genom att sprida dem så jämnt som möjligt via flera feldomäner (Azure Stack-noder). Om det finns ett maskinvarufel, kommer virtuella datorer från den misslyckade feldomänen att startas om i andra feldomäner, men om möjligt sparas i separata feldomäner från andra virtuella datorer i samma tillgänglighetsuppsättning. När maskinvaran som är online igen, kommer du genomförs virtuella datorer för att upprätthålla hög tillgänglighet. 
 
- **Uppdateringsdomäner**. Uppdateringsdomäner är en annan Azure koncept som ger hög tillgänglighet i tillgänglighetsuppsättningar. En uppdateringsdomän är en logisk grupp av underliggande maskinvara som kan underhållas på samma gång. Virtuella datorer finns i samma uppdateringsdomän startas tillsammans under planerat underhåll. När klienter skapar virtuella datorer i en tillgänglighetsuppsättning, distribuerar Azure-plattformen automatiskt virtuella datorer mellan dessa uppdateringsdomäner. I Azure Stack, virtuella datorer är live migreras på andra online värdar i klustret innan deras underliggande värden har uppdaterats. Eftersom det inte finns inget klient-avbrott under en värduppdatering av, finns bara funktionen domän i Azure Stack mall kompatibilitet med Azure. 

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Du kan använda rollbaserad åtkomstkontroll (RBAC) för att ge systemåtkomst till behöriga användare, grupper och tjänster genom att tilldela dem roller på en prenumeration, resursgrupp eller enskild resurs. Varje roll definierar den åtkomstnivå som en användare, grupp eller tjänst har över Microsoft Azure Stack-resurser.

RBAC i Azure Stack har tre grundläggande roller som gäller för alla typer av resurser: Ägare, deltagare och läsare. Ägare har fullständig åtkomst till alla resurser inklusive rätten att delegera åtkomst till andra. Deltagare kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra. Läsare kan bara visa befintliga resurser. Resten av RBAC-roller kan hanteringen av specifika Azure-resurser. Exempelvis kan rollen virtuell Datordeltagare kan skapandet och hanteringen av virtuella datorer, men tillåter inte hantering av det virtuella nätverket eller undernätet som den virtuella datorn ansluter till.

> Se [Manage Role-Based Access Control](azure-stack-manage-permissions.md) för mer information. 

## <a name="reporting-usage-data"></a>Rapportering av användningsdata
Microsoft Azure Stack samlar in och sammanställer användningsdata över alla resursproviders och skickar den till Azure för bearbetning av Azure commerce. Användningsdata som samlas in på Azure Stack kan ses via ett REST-API. Det finns en Azure-konsekventa klient-API som Provider och delegerad Provider API: er att hämta användningsdata för alla klient-prenumerationer. Dessa data kan användas för att integrera med ett externt verktyg eller en tjänst för fakturerings- eller återbetalning. När användning har behandlats av Azure commerce, kan det visas i på Azures faktureringsportal.

> Läs mer om [rapportdata på Azure Stack-användning till Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Nästa steg

[Jämför Azure Stack och globala Azure](compare-azure-azure-stack.md)

[Grundläggande om administration](azure-stack-manage-basics.md)

[Snabbstart: Använd Azure Stack-administrationsportalen](azure-stack-manage-portals.md)