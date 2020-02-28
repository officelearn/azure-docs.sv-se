---
title: Azure Monitor loggar för tjänst leverantörer | Microsoft Docs
description: Azure Monitor loggar kan hjälpa Managed Service Providers (MSP), stora företag, oberoende program varu leverantörer (ISV) och värd leverantörer att hantera och övervaka servrar i kundens lokala eller molnbaserade infrastruktur.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658888"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure Monitor loggar för tjänst leverantörer

Log Analytics arbets ytor i Azure Monitor kan hjälpa Managed Service Providers (MSP), stora företag, oberoende program varu leverantörer (ISV) och värd tjänster att hantera och övervaka servrar i kundens lokala eller molnbaserade infrastruktur.

Stora företag dela många likheter med leverantörer, särskilt när det finns en central IT-teamet som ansvarar för att hantera IT för många olika affärsenheter. För enkelhetens skull använder det här dokumentet termen *Service Provider* men samma funktioner är också tillgängliga för företag och andra kunder.

För partner och tjänst leverantörer som ingår i ett [CSP-program (Cloud Solution Provider)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) Log Analytics i Azure Monitor en av de Azure-tjänster som är tillgängliga i Azure CSP-prenumerationer.

Log Analytics i Azure Monitor kan också användas av en tjänst leverantör som hanterar kund resurser via Azures delegerade resurs hanterings funktion i [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="architectures-for-service-providers"></a>Arkitekturer för tjänsteleverantörer

Log Analytics-arbetsytor är en metod som administratören kan använda för att styra flödet och isoleringen av [loggdata](data-platform-logs.md) och skapa en arkitektur som tillgodoser specifika affärs behov. [Den här artikeln](design-logs-deployment.md) beskriver design-, distributions-och migrerings överväganden för en arbets yta och i artikeln [Hantera åtkomst](manage-access.md) beskrivs hur du tillämpar och hanterar behörigheter för att logga data. Leverantörer av tjänster har ytterligare överväganden.

Det finns tre möjliga arkitekturer för tjänsteleverantörer om Log Analytics-arbetsytor:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. distribuerade loggar lagras i arbets ytor som finns i kundens klient organisation

I den här arkitekturen med en arbetsyta i kundens klient som används för alla loggar kundens.

Det finns två sätt som administratörer kan använda för att få åtkomst till en Log Analytics-arbetsyta i en kund klient organisation:

- En kund kan lägga till enskilda användare från tjänst leverantören som [Azure Active Directory gäst användare (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Administratörer för tjänst leverantörer måste logga in på varje kunds katalog i Azure Portal för att kunna komma åt dessa arbets ytor. Detta kräver också att kunderna hanterar enskild åtkomst för varje tjänst leverantörs administratör.
- För bättre skalbarhet och flexibilitet kan tjänst leverantörer använda [Azures delegerade resurs hanterings](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) funktion i [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) för att få åtkomst till kundens klient. Med den här metoden inkluderas tjänst leverantörs administratörer i en Azure AD-användargrupp i tjänst leverantörens klient organisation och gruppen beviljas åtkomst under onboarding-processen för varje kund. Administratörerna kan sedan komma åt varje kunds arbets ytor från sin egen tjänst leverantörs klient, i stället för att behöva logga in på varje kunds klient organisation individuellt. Genom att komma åt dina kunders Log Analytics arbets ytans resurser på det här sättet minskar du det arbete som krävs på kund sidan och kan göra det enklare att samla in och analysera data över flera kunder som hanteras av samma tjänst leverantör via verktyg som [Azure Monitor arbets böcker](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview). Mer information finns i [övervaka kund resurser i stor skala](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

Fördelarna med den distribuerade arkitekturen är:

* Kunden kan bekräfta specifika behörighets nivåer via [Azure-delegerad resurs hantering](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management), eller så kan du hantera åtkomst till loggarna med hjälp av sin egen [rollbaserade åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Loggar kan samlas in från alla typer av resurser, inte bara agentbaserade VM-data. Till exempel Azure-granskningsloggarna.
* Varje kund kan ha olika inställningar för arbetsytan, till exempel kvarhållning och data tak som skall.
* Isolering mellan kunder, föreskrifter och efterlevnad.
* Kostnad för varje arbetsyta kommer att finnas i kundens prenumeration.

Nack delarna med den distribuerade arkitekturen är:

* Att visualisera och analysera data centralt mellan kund klienter med verktyg som Azure Monitor arbets böcker kan resultera i långsammare upplevelser, särskilt när du analyserar data över 50 + arbets ytor.
* Om kunderna inte har publicerats för Azure-delegerad resurs hantering måste tjänst leverantörs administratörerna vara etablerade i kund katalogen och det är svårare för tjänste leverantören att hantera ett stort antal kund klienter samtidigt.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrala loggar lagras på en arbets yta som finns i tjänste leverantörens klient organisation

Loggfilerna lagras inte i den här arkitekturen i kundens klienter, men endast i en central plats i en av tjänstleverantörens prenumerationer. Agenter som installerats på kundens virtuella datorer är konfigurerade för att skicka loggar till den här arbetsytan med hjälp av arbetsytans ID och hemlig nyckel.

Fördelarna med den centraliserade arkitekturen är:

* Det är enkelt att hantera ett stort antal kunder och integrera dem till olika serverdelssystem.
* Tjänstleverantören har fullständigt ägarskap över loggar och olika artefakter som funktioner och sparade frågor.
* Tjänstleverantören kan utföra analyser för alla sina kunder.

Nack delarna med den centraliserade arkitekturen är:

* Den här arkitekturen används endast för agentbaserad VM-data, täcker inte PaaS, SaaS och Azure fabric-datakällor.
* Det kan vara svårt att dela data mellan kunder när de infogas i en enda arbetsyta. Metoden endast bra att göra detta är att använda datorns fullständigt kvalificerade domännamnet (FQDN) eller via Azure-prenumeration-ID. 
* Alla data från alla kunder kommer att lagras i samma region med en enda faktura och samma inställningar för kvarhållning och konfiguration.
* Azure-infrastrukturen och PaaS-tjänster som Azure Diagnostics och Azure-granskningsloggarna måste arbetsytan för att vara i samma klient som resursen, så de inte kan skicka loggarna till arbetsytan central.
* Alla VM-agenter från alla kunder kommer att autentiseras till den centrala arbets ytan med samma arbetsyte-ID och nyckel. Det finns ingen metod för att blockera loggar från en viss kund utan att störa andra kunder.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrid loggar lagras i arbets ytan som finns i kundens klient organisation och vissa av dem hämtas till en central plats.

Den tredje arkitekturen blanda mellan de två alternativen. Den är baserad på den första distribuerade arkitektur där loggarna är lokala för varje kund men med någon mekanism för att skapa en central databas av loggar. En del av loggarna hämtas till en central plats för rapportering och analys. Den här delen kan vara litet antal datatyper eller en sammanfattning av aktiviteter, till exempel dagliga statistik.

Det finns två alternativ för att implementera loggar på en central plats:

1. Central arbets yta: tjänste leverantören kan skapa en arbets yta i sin klient och använda ett skript som använder [fråge-API](https://dev.loganalytics.io/) [: t med data insamlings-API: et](../../azure-monitor/platform/data-collector-api.md) för att hämta data från de olika arbets ytorna till den här centrala platsen. Ett annat alternativ än ett skript är att använda [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI som en central plats: Power BI kan fungera som den centrala platsen när de olika arbets ytorna exporterar data till den med hjälp av integreringen mellan arbets ytan Log Analytics och [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Nästa steg

* Automatisera skapandet och konfigurationen av arbets ytor med [Resource Manager-mallar](template-workspace-configuration.md)

* Automatisera skapandet av arbets ytor med [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Använd [aviseringar](../../azure-monitor/platform/alerts-overview.md) för att integrera med befintliga system

* Generera sammanfattnings rapporter med [Power BI](../../azure-monitor/platform/powerbi.md)

* Publicera kunder till [Azure delegerad resurs hantering](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
