---
title: Azure Monitor Loggar för tjänsteleverantörer | Microsoft-dokument
description: 'Azure Monitor Logs kan hjälpa hanterade tjänsteleverantörer (MSP), stora företag, oberoende programvaruleverantörer (ISV: er) och värdtjänstleverantörer hantera och övervaka servrar i kundens lokala infrastruktur eller molninfrastruktur.'
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658888"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure-övervakarloggar för tjänsteleverantörer

Log Analytics-arbetsytor i Azure Monitor kan hjälpa hanterade tjänsteleverantörer (MSP), stora företag, oberoende programvaruleverantörer (ISV) och värdtjänstleverantörer hantera och övervaka servrar i kundens lokala infrastruktur eller molninfrastruktur.

Stora företag har många likheter med tjänsteleverantörer, särskilt när det finns ett centraliserat IT-team som ansvarar för att hantera IT för många olika affärsenheter. För enkelhetens skull använder det här dokumentet termen *tjänsteleverantör,* men samma funktioner är också tillgängliga för företag och andra kunder.

För partner och tjänsteleverantörer som ingår i [CSP-programmet (Cloud Solution Provider)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) är Log Analytics i Azure Monitor en av de Azure-tjänster som är tillgängliga i Azure CSP-prenumerationer.

Logganalys i Azure Monitor kan också användas av en tjänsteleverantör som hanterar kundresurser via Azure-delegerade resurshanteringskapaciteten i [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="architectures-for-service-providers"></a>Arkitekturer för tjänsteleverantörer

Log Analytics-arbetsytor ger en metod för administratören att styra flödet och isoleringen av [loggdata](data-platform-logs.md) och skapa en arkitektur som tillgodoser dess specifika affärsbehov. [I](design-logs-deployment.md) den här artikeln beskrivs design-, distributions- och migreringsövervägandena för en arbetsyta, och i artikeln [om hantering av åtkomst](manage-access.md) beskrivs hur du använder och hanterar behörigheter för att logga data. Tjänsteleverantörerna har ytterligare överväganden.

Det finns tre möjliga arkitekturer för tjänsteleverantörer när det gäller Log Analytics-arbetsytor:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuerad - Loggar lagras i arbetsytor som finns i kundens klientorganisation

I den här arkitekturen distribueras en arbetsyta i kundens klientorganisation som används för alla loggar för kunden.

Det finns två sätt att tjänsteleverantörer administratörer kan få tillgång till en Log Analytics arbetsyta i en kundklient:

- En kund kan lägga till enskilda användare från tjänsteleverantören som [Azure Active Directory-gästanvändare (B2B).](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) Tjänstprovideradministratörerna måste logga in på varje kunds katalog i Azure-portalen för att kunna komma åt dessa arbetsytor. Detta kräver också att kunderna hanterar individuell åtkomst för varje tjänsteleverantörsadministratör.
- För större skalbarhet och flexibilitet kan tjänsteleverantörer använda [Azure-delegerade resurshanteringsfunktionen](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) i [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) för att komma åt kundens klientorganisation. Med den här metoden ingår tjänstprovideradministratörerna i en Azure AD-användargrupp i tjänsteleverantörens klientorganisation och den här gruppen beviljas åtkomst under introduktionsprocessen för varje kund. Dessa administratörer kan sedan komma åt varje kunds arbetsytor inifrån sin egen tjänstleverantörsklient, i stället för att behöva logga in på varje kunds klientorganisation individuellt. Åtkomst till kundernas logganalysarbetsytor resurser på detta sätt minskar det arbete som krävs på kundsidan, och kan göra det enklare att samla in och analysera data över flera kunder som hanteras av samma tjänsteleverantör via verktyg som [Azure Monitor Arbetsböcker](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview). Mer information finns i [Övervaka kundresurser i stor skala](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

Fördelarna med den distribuerade arkitekturen är:

* Kunden kan bekräfta specifika behörighetsnivåer via [Azure-delegerad resurshantering](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)eller hantera åtkomst till loggarna med hjälp av sin egen [rollbaserade åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Loggar kan samlas in från alla typer av resurser, inte bara agentbaserade VM-data. Till exempel Azure Audit Logs.
* Varje kund kan ha olika inställningar för sin arbetsyta, till exempel lagring och datatak.
* Isolering mellan kunder för reglering och kompatibilitet.
* Avgiften för varje arbetsyta kommer att rullas in i kundens prenumeration.

Missgynnar av den utdelade arkitekturen är:

* Centralt visualisera och analysera data över kundklienter med verktyg som Azure Monitor Arbetsböcker kan resultera i långsammare upplevelser, särskilt när du analyserar data över mer än 50 + arbetsytor.
* Om kunder inte är inbyggda för Azure-delegerad resurshantering måste tjänstprovideradministratörer etableras i kundkatalogen och det är svårare för tjänsteleverantören att hantera ett stort antal kundklienter samtidigt.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Central - Loggar lagras på en arbetsyta som finns i tjänsteleverantörens klient

I den här arkitekturen lagras inte loggarna i kundens klienter utan bara på en central plats i en av tjänsteleverantörens prenumerationer. De agenter som är installerade på kundens virtuella datorer är konfigurerade för att skicka sina loggar till den här arbetsytan med hjälp av arbetsytans ID och hemlig nyckel.

Fördelarna med den centraliserade arkitekturen är:

* Det är lätt att hantera ett stort antal kunder och integrera dem i olika backend-system.
* Tjänsteleverantören har full äganderätt över loggarna och de olika artefakterna, till exempel funktioner och sparade frågor.
* Tjänsteleverantören kan utföra analyser över alla sina kunder.

Missgynnar av den centraliserade arkitekturen är:

* Den här arkitekturen gäller endast för agentbaserade VM-data, den täcker inte PaaS-, SaaS- och Azure-infrastrukturdatakällor.
* Det kan vara svårt att skilja data mellan kunderna när de slås samman till en enda arbetsyta. Den enda bra metoden för att göra det är att använda datorns fullständigt kvalificerade domännamn (FQDN) eller via Azure-prenumerations-ID. 
* Alla data från alla kunder lagras i samma region med en enda faktura och samma lagrings- och konfigurationsinställningar.
* Azure-infrastruktur och PaaS-tjänster som Azure Diagnostics och Azure Audit Logs kräver att arbetsytan finns i samma klient som resursen, vilket innebär att de inte kan skicka loggarna till den centrala arbetsytan.
* Alla VM-agenter från alla kunder autentiseras till den centrala arbetsytan med samma arbetsyte-ID och nyckel. Det finns ingen metod för att blockera loggar från en viss kund utan att avbryta andra kunder.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrid - Loggar lagras på arbetsytan som finns i kundens hyresgäst och några av dem dras till en central plats.

Den tredje arkitekturblandningen mellan de två alternativen. Den baseras på den första distribuerade arkitekturen där loggarna är lokala för varje kund men använder någon mekanism för att skapa en central lagringsplats för loggar. En del av loggarna dras till en central plats för rapportering och analys. Den här delen kan vara ett litet antal datatyper eller en sammanfattning av aktiviteten, till exempel daglig statistik.

Det finns två alternativ för att implementera loggar på en central plats:

1. Central arbetsyta: Tjänsteleverantören kan skapa en arbetsyta i sin klientorganisation och använda ett skript som använder [fråge-API:et](https://dev.loganalytics.io/) med [API:et för datainsamling](../../azure-monitor/platform/data-collector-api.md) för att föra data från de olika arbetsytorna till den här centrala platsen. Ett annat alternativ, annat än ett skript, är att använda [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI som en central plats: Power BI kan fungera som den centrala platsen när de olika arbetsytorna exporterar data till den med hjälp av integreringen mellan Log Analytics-arbetsytan och [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Nästa steg

* Automatisera skapande och konfiguration av arbetsytor med hjälp av [Resource Manager-mallar](template-workspace-configuration.md)

* Automatisera skapandet av arbetsytor med [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Använd [aviseringar](../../azure-monitor/platform/alerts-overview.md) för att integrera med befintliga system

* Generera sammanfattningsrapporter med [Power BI](../../azure-monitor/platform/powerbi.md)

* Ombord på kunder till [Azure-delegerad resurshantering](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
