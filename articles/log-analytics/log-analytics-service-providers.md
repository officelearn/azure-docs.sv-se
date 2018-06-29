---
title: Logga analysfunktioner för tjänstleverantörer | Microsoft Docs
description: Logganalys hjälper hanteras tjänsteleverantörer (MSPs), stora företag oberoende programvara leverantörer (ISV) och värdleverantörer hantera och övervaka servrar i kundens lokala eller molninfrastruktur.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: meirm
ms.openlocfilehash: 97e36c624e865010ada67f5163af6d7f03de079f
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096578"
---
# <a name="log-analytics-features-for-service-providers"></a>Log Analytics funktioner för leverantörer
Logganalys hjälper hanterade tjänsteleverantörer (MSPs), stora företag, oberoende programvaruleverantörer (ISV) och värdleverantörer hantera och övervaka servrar i kundens lokala eller molninfrastruktur. 

Stora företag dela många likheter med leverantörer, särskilt när det finns en central IT-teamet som ansvarar för att hantera IT-avdelningen för många olika affärsenheter. För enkelhetens skull använder det här dokumentet termen *tjänstleverantör* men samma funktion är också tillgängligt för företag och andra kunder.

Partners och leverantörer som är en del av den [Cloud Solution Providers (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, Log Analytics är en av Azure-tjänster finns i [Azure CSP prenumeration](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Arkitekturer för leverantörer

Log Analytics arbetsytor ger en metod för administratören att styra flödet och isolering av loggarna och skapa en logg-arkitektur som tar upp specifika affärsbehov. [Den här artikeln](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) beskriver allmänna överväganden kring arbetsytan management. Leverantörer har ytterligare överväganden.

Det finns tre möjliga arkitekturer för tjänstleverantörer om logganalys arbetsytor:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuerad - lagras loggfilerna i arbetsytor finns i kundens klient 

I den här arkitekturen distribueras arbetsytan i kundens innehavaren som ska användas för alla loggar kundens. Service provider-administratörer beviljas åtkomst till den här arbetsytan med hjälp av [gästanvändare för Azure Active Directory (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). Tjänstproviderns administratör måste ändra i Azure-portalen till sina kunder katalogen för att kunna komma åt dessa arbetsytor.

Fördelarna med den här arkitekturen är:
* Kunden kan hantera åtkomst till loggar med sina egna [rollbaserad åtkomst](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Varje kund kan ha olika inställningar för arbetsytan, till exempel kvarhållning och tak som skall data.
* Isolering mellan kunder för regelverk och kompatibilitet.
* Kostnad för varje arbetsyta återställs till kundens prenumeration.
* Loggarna samlas in från alla typer av resurser, inte bara agent-baserade. Till exempel Azure granskning.

Nackdelarna med den här arkitekturen är:
* Det är svårare för leverantören att hantera många kunden klienter på samma gång.
* Service provider administratörer har etableras på katalogen kunder.
* Tjänstprovidern kan analysera data över sina kunder.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Central - loggar lagras i arbetsytan finns i service provider-klient

Loggfilerna lagras inte i den här arkitekturen i kundens hyresgäster, men endast i en central plats i en av tjänstproviderns prenumerationer. Agenter som installerats på kundens virtuella datorer är konfigurerade för att skicka deras loggar till den här arbetsytan med arbetsyte-ID och hemlig nyckel.

Fördelarna med den här arkitekturen är:
* Det är enkelt att hantera stort antal kunder och integrera dem till olika serverdelssystem.
* Tjänstprovidern har fullständigt ägarskap över loggarna och olika artefakter, till exempel funktioner och sparade frågor.
* Leverantören kan utföra analyser för alla kunder.

Nackdelarna med den här arkitekturen är:
* Det är svårt att dela data mellan kunderna. Endast bra metod att göra detta är att använda datorns domännamn.
* Alla data från alla kunder kommer att lagras i samma region med en enda faktura och samma inställningar för kvarhållning och konfiguration.
* Azure-strukturen och PaaS-tjänster som Azure-diagnostik och Azure granskning kräver arbetsytan ska vara i samma klientorganisation som resursen därför de inte kan skicka loggar till arbetsytan central.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrid - loggar lagras i arbetsytan i kundens klient och några av dem hämtas till en central plats.

Den tredje arkitekturen blanda mellan de två alternativen. Den är baserad på den första distribuerad arkitektur där loggarna är lokala för varje kund men med någon mekanism för att skapa en central databas loggar. En del av loggarna som hämtas till en central plats för rapportering och analys. Den här delen kan vara litet antal typer av data eller en sammanfattning av aktivitet, till exempel dagliga statistik.

Det finns två alternativ för att implementera den centrala platsen i logganalys:

1. Central arbetsytan: tjänstleverantören kan skapa en arbetsyta i dess klient och använda ett skript som använder den [frågan API](https://dev.loganalytics.io/) med den [Data Collection API](log-analytics-data-collector-api.md) in data från olika arbetsytorna till den här central plats. Ett annat alternativ än skript är att använda [Azure Logikapp](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. PowerBI som en central plats: Power BI kan fungera som den centrala platsen när de olika arbetsytorna exportera data till den med hjälp av integrering mellan logganalys och [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>Nästa steg
* Automatisera skapandet och konfiguration av arbetsytor med [Resource Manager-mallar](log-analytics-template-workspace-configuration.md)
* Automatisera genereringen av arbetsytor med [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Använd [aviseringar](log-analytics-alerts.md) att integrera med befintliga system
* Generera sammanfattningsrapporter med [Power BI](log-analytics-powerbi.md)

