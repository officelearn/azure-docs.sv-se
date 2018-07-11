---
title: Log Analytics för tjänsteleverantörer | Microsoft Docs
description: Log Analytics kan hjälpa att Managed Service Providers (MSP), stora företag oberoende programvara-leverantörer (ISV) och värdleverantörer hantera och övervaka servrar i kundens on-premises eller molninfrastruktur.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: meirm
ms.component: na
ms.openlocfilehash: ad0a3b8e0ee5f1114ea1db95cfe2f4176b8e2ddb
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931998"
---
# <a name="log-analytics-for-service-providers"></a>Logganalys för tjänsteleverantörer
Log Analytics kan leverantörer av hanterade tjänster (MSP), stora företag, oberoende programvaruleverantörer (ISV) och värdleverantörer hantera och övervaka servrar i kundens on-premises eller molninfrastruktur. 

Stora företag dela många likheter med leverantörer, särskilt när det finns en central IT-teamet som ansvarar för att hantera IT för många olika affärsenheter. För enkelhetens skull det här dokumentet använder termen *tjänstleverantör* men samma funktion är också tillgängligt för företag och andra kunder.

För partner och leverantörer som är en del av den [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) programmet, Log Analytics är en av de Azure-tjänsterna finns i [Azure CSP-prenumeration](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Arkitekturer för tjänsteleverantörer

Log Analytics-arbetsytor är en metod för administratören att styra flödet och isolering av loggarna och skapa en log-arkitektur som åtgärdar specifika affärsbehov. [Den här artikeln](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) beskriver allmänna överväganden kring arbetsytehantering. Leverantörer av tjänster har ytterligare överväganden.

Det finns tre möjliga arkitekturer för tjänsteleverantörer om Log Analytics-arbetsytor:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuerade - lagras loggar i arbetsytor i kundens klient 

I den här arkitekturen distribueras arbetsytan i kundens klient som används för alla loggar kundens. Service provider-administratörer beviljas åtkomst till arbetsytan genom att använda [gästanvändare i Azure Active Directory (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). Tjänstproviderns administratör måste växla i Azure-portalen till sina kunders katalog för att kunna komma åt dessa arbetsytor.

Fördelarna med den här arkitekturen är:
* Kunden kan hantera åtkomst till loggar med sina egna [rollbaserad åtkomst](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Varje kund kan ha olika inställningar för arbetsytan, till exempel kvarhållning och data tak som skall.
* Isolering mellan kunder, föreskrifter och efterlevnad.
* Kostnad för varje arbetsyta kommer att finnas i kundens prenumeration.
* Loggar samlas in från alla typer av resurser, inte bara agentbaserad. Till exempel Azure granskning.

Nackdelarna med den här arkitekturen är:
* Det är svårare för tjänstleverantör för att hantera stora antal kunden klienter på samma gång.
* Providern administratörer behöva etableras på katalogen kunder.
* Tjänstleverantören det går inte att analysera data mellan sina kunder.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Central - loggar lagras i arbetsytan finns i service provider-klient

Loggfilerna lagras inte i den här arkitekturen i kundens klienter, men endast i en central plats i en av tjänstleverantörens prenumerationer. Agenter som installerats på kundens virtuella datorer är konfigurerade för att skicka loggar till den här arbetsytan med hjälp av arbetsytans ID och hemlig nyckel.

Fördelarna med den här arkitekturen är:
* Det är enkelt att hantera stora antal kunder och integrera dem till olika serverdelssystem.
* Tjänstleverantören har fullständigt ägarskap över loggar och olika artefakter som funktioner och sparade frågor.
* Tjänstleverantören kan utföra analyser för alla kunder.

Nackdelarna med den här arkitekturen är:
* Den här arkitekturen används endast för agentbaserad VM-data, täcker inte PaaS, SaaS och Azure fabric-datakällor.
* Det kan vara svårt att dela data mellan kunder när de infogas i en enda arbetsyta. Metoden endast bra att göra detta är att använda datorns fullständigt kvalificerade domännamnet (FQDN) eller via Azure-prenumeration-ID. 
* Alla data från alla kunder kommer att lagras i samma region med en enda faktura och samma inställningar för kvarhållning och konfiguration.
* Azure-infrastrukturen och PaaS-tjänster som Azure Diagnostics och Azure granskning kräver arbetsytan för att vara i samma klient som resursen så de inte kan skicka loggarna till arbetsytan central.
* Alla VM-agenter från alla kunder autentiseras till arbetsytan cental samma arbetsyte-ID och nyckel. Det finns ingen metod för att blockera loggar från en viss kund utan att störa andra kunder.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrid - loggar lagras i arbetsytan finns i kundens klient och vissa av dem hämtas till en central plats.

Den tredje arkitekturen blanda mellan de två alternativen. Den är baserad på den första distribuerade arkitektur där loggarna är lokala för varje kund men med någon mekanism för att skapa en central databas av loggar. En del av loggarna hämtas till en central plats för rapportering och analys. Den här delen kan vara litet antal datatyper eller en sammanfattning av aktiviteter, till exempel dagliga statistik.

Det finns två alternativ för att implementera den centrala platsen i Log Analytics:

1. Central arbetsyta: tjänstleverantören kan skapa en arbetsyta i dess klient och använda ett skript som använder den [fråge-API](https://dev.loganalytics.io/) med den [Data samling API: et](log-analytics-data-collector-api.md) att flytta data från olika arbetsytor till den här central plats. Ett annat alternativ än skriptet är att använda [Azure Logic App](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. Power BI som en central plats: Power BI kan fungera som den centrala platsen när olika arbetsytor exporterar data till den med hjälp av integrering mellan Log Analytics och [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>Nästa steg
* Automatisera skapande och konfiguration av arbetsytor med hjälp av [Resource Manager-mallar](log-analytics-template-workspace-configuration.md)
* Automatisera genereringen av arbetsytor med hjälp av [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Använd [aviseringar](log-analytics-alerts.md) att integrera med befintliga system
* Generera sammanfattningsrapporter med [Power BI](log-analytics-powerbi.md)

