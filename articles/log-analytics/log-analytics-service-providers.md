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
ms.author: richrund
ms.openlocfilehash: 6934e92df562099122eaede39fd26cf51cf1ee44
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593057"
---
# <a name="log-analytics-features-for-service-providers"></a>Log Analytics funktioner för leverantörer
Logganalys hjälper hanterade tjänsteleverantörer (MSPs), stora företag, oberoende programvaruleverantörer (ISV) och värdleverantörer hantera och övervaka servrar i kundens lokala eller molninfrastruktur. 

Stora företag dela många likheter med leverantörer, särskilt när det finns en central IT-teamet som ansvarar för att hantera IT-avdelningen för många olika affärsenheter. För enkelhetens skull använder det här dokumentet termen *tjänstleverantör* men samma funktion är också tillgängligt för företag och andra kunder.

## <a name="cloud-solution-provider"></a>Cloud Solution Provider
Partners och leverantörer som är en del av den [Cloud Solution Providers (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, Log Analytics är en av Azure-tjänster finns i [Azure CSP prenumeration](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

För Log Analytics följande funktioner är aktiverade i *leverantör* prenumerationer.

Som en *leverantör* kan du:

* Skapa logganalys arbetsytor i prenumeration för en klient (kundens).
* Åtkomst arbetsytor som skapats av klienter. 
* Lägg till och ta bort användaråtkomst till arbetsytan med Azure användarhantering. När en klient arbetsytan i OMS-portalen på Användarhantering sidan inställningar är inte tillgängligt
  * Log Analytics har inte stöd för rollbaserad åtkomst ännu -ge en användare `reader` i Azure-portalen tillåter dem att göra konfigurationsändringar i OMS-portalen

Du måste ange klient-ID för att logga in till en klient prenumeration. Klient-ID är ofta den sista delen av e-postadressen används för att logga in.

* Lägg till i OMS-portalen `?tenant=contoso.com` i URL: en för portalen. Till exempel, `mms.microsoft.com/?tenant=contoso.com`
* I PowerShell använder den `-Tenant contoso.com` parameter när du använder `Connect-AzureRmAccount` cmdlet
* Klient-ID läggs till automatiskt när du använder den `OMS portal` länk från Azure portal för att öppna och logga in på OMS-portalen för den valda arbetsytan

Som en *kunden* av en leverantör kan du:

* Skapa log analytics arbetsytor i en CSP-prenumeration
* Åtkomst arbetsytor som skapats av Kryptografiprovidern
  * Använd den `OMS portal` länk från Azure portal för att öppna och logga in på OMS-portalen för den valda arbetsytan
* Visa och använda sidan för användarhantering under inställningar i OMS-portalen

> [!NOTE]
> Med säkerhetskopiering och återställning av lösningar för Log Analytics kan inte ansluta till Recovery Services-valvet och inte kan konfigureras i en CSP-prenumeration. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Hantera flera kunder som använder logganalys
Vi rekommenderar att du skapar en logganalys-arbetsytan för varje kund som du hanterar. Logganalys-arbetsytan innehåller:

* En geografisk plats för data som ska lagras. 
* Precision för fakturering 
* Dataisolering 
* Unik konfiguration

Genom att skapa en arbetsyta per kund kan kan du behålla data för varje kund separat och även spåra användningen av varje kund.

Mer information om när och varför ska du skapa flera arbetsytor beskrivs i [hantera åtkomst för att logga analytics](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Skapa och konfiguration av customer arbetsytor kan automatiseras med hjälp av [PowerShell](log-analytics-powershell-workspace-configuration.md), [Resource Manager-mallar](log-analytics-template-workspace-configuration.md), eller med hjälp av den [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

Användningen av Resource Manager-mallar för arbetsytan konfiguration kan du ha en master-konfiguration som kan användas för att skapa och konfigurera arbetsytor. Du kan vara säker på att som arbetsytor skapas för kunder de konfigureras automatiskt enligt dina behov. När du uppdaterar dina krav mallen uppdateras och sedan på nytt befintliga arbetsytor. Den här processen säkerställer att även befintliga arbetsytor uppfyller dina nya.    

När du hanterar flera logganalys arbetsytor, rekommenderar vi integrerar varje arbetsyta med din befintliga biljettsystem / operations-konsolen använder den [aviseringar](log-analytics-alerts.md) funktioner. Genom att integrera med befintliga system kan supportpersonal fortsätta att följa sina bekant processer. Logganalys regelbundet kontrollerar varje arbetsyta mot aviseringsvillkoren som du anger och genererar en avisering när åtgärd krävs.

Anpassade vyer av data använder den [instrumentpanelen](../azure-portal/azure-portal-dashboards.md) kapaciteten i Azure-portalen.  

För verkställande nivån rapporter som summerar data över arbetsytor kan du använda integrationen mellan logganalys och [PowerBI](log-analytics-powerbi.md). Om du behöver integrera med en annan rapporteringssystem du kan använda Sök-API (via PowerShell eller [REST](log-analytics-log-search-api.md)) att köra frågor och exportera sökresultaten.

## <a name="next-steps"></a>Nästa steg
* Automatisera skapandet och konfiguration av arbetsytor med [Resource Manager-mallar](log-analytics-template-workspace-configuration.md)
* Automatisera genereringen av arbetsytor med [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Använd [aviseringar](log-analytics-alerts.md) att integrera med befintliga system
* Generera sammanfattningsrapporter med [PowerBI](log-analytics-powerbi.md)

