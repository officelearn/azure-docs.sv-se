---
title: 'Azure Monitor tillbakadragning av klassiska distributionsmodellen API: er för mått och automatisk skalning'
description: 'Mått och automatisk skalning klassiska API: er, kallas även för Azure Service Management (ASM) eller RDFE-modellen som har återkallats'
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: 1ea8fddf2b4e75abba38f011a271aada848a8cb5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192200"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor tillbakadragning av klassiska distributionsmodellen API: er för mått och automatisk skalning

Azure Monitor (tidigare Azure Insights när introducerades) erbjuder för närvarande möjligheten att skapa och hantera inställningarna för automatisk skalning för och konsumera mått från klassiska virtuella datorer och klassiskt molntjänster. Den ursprungliga uppsättningen klassisk distribution modellbaserade API: er kommer att **dras tillbaka efter den 30 juni 2019** i alla Azure offentliga och privata moln i alla regioner.   

Samma åtgärder har kunnat användas via en uppsättning av Azure Resource Manager-baserade API: er för under ett år. Azure-portalen använder det nya REST API: er för både automatisk skalning och mått. En ny SDK, PowerShell och CLI baserat på dessa API: er för Resource Manager är också tillgängliga. Våra partnertjänster för att övervaka använda de nya Resource Manager-baserade REST API: er i Azure Monitor.  

## <a name="who-is-not-affected"></a>Vem påverkas inte

Om du hanterar automatisk skalning via Azure-portalen, den [nya Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, CLI eller Resource Manager-mallar, ingen åtgärd krävs.  

Om du förbrukar mått via Azure portal eller via olika [övervakning partnertjänster](../../monitoring-and-diagnostics/monitoring-partners.md), ingen åtgärd krävs. Microsoft arbetar med övervakning av partner för att migrera till den nya API: er.

## <a name="who-is-affected"></a>Vem påverkas

Den här artikeln gäller om du använder följande komponenter:

- **Klassiska Azure Insights SDK** – om du använder den [klassiska Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), växla till den nya Azure Monitor SDK för [.NET](https://github.com/azure/azure-libraries-for-net#download) eller [Java](https://github.com/azure/azure-libraries-for-java#download). Ladda ned den [Azure Monitor NuGet-paketet SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klassiska Autoskala** – om du anropar den [klassiska autoskalningsinställningar API: er](https://msdn.microsoft.com/library/azure/mt348562.aspx) från dina anpassade verktyg eller använda den [klassiska Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), bör du istället använda [ Övervakare för Azure Resource Manager REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Klassiska mått** – om du förbrukar mått med hjälp av den [klassiska REST API: er](https://msdn.microsoft.com/library/azure/dn510374.aspx) eller [klassiska Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) från anpassade verktyg, bör du istället använda den [ Övervakare för Azure Resource Manager REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Om du är osäker på om din kod eller anpassade verktyg anropar klassiska API: erna kan du titta på följande:

- Granska den URI som refereras till i din kod eller verktyg. De klassiska API: erna använder URI: N https://management.core.windows.net. Du bör använda nyare URI: N för Resource Manager baserat API: er som börjar med https://management.azure.com/.

- Jämför namnet på din dator. Äldre klassiska sammansättningen är på https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Om du använder autentisering med datorcertifikat för att få åtkomst till mått eller automatisk skalning API: er använder du ett bibliotek och klassiska slutpunkt. De nyare Resource Manager-API: er kräver Azure Active Directory-autentisering via en tjänstens huvudnamn eller en användares huvudnamn.

- Om du använder anrop som nämns i dokumentationen på någon av följande länkar använder du den äldre klassiska API: er.

  - [Windows.Azure.Management.Monitoring-klassbiblioteket](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Övervakning av .NET (klassisk)](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations gränssnitt](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Varför du bör växla

Alla befintliga funktioner för automatisk skalning och mått fortsätter att fungera via den nya API: er.  

Migrera över till nyare API: er som levereras med Resource Manager-baserade funktioner, t.ex stöd för konsekvent rollbaserad åtkomstkontroll (RBAC) över alla övervakningstjänster. Du får också ytterligare funktioner för mått: 

- stöd för dimensioner
- konsekvent 1 minut mått kornighet för alla tjänster 
- bättre frågor
- högre datakvarhållning (93 dagars jämfört med mått. 30 dagar) 

Övergripande, precis som alla andra tjänster i Azure Resource Manager baserat Azure Monitor API: er som levereras med bättre prestanda, skalbarhet och tillförlitlighet. 

## <a name="what-happens-if-you-do-not-migrate"></a>Vad händer om du inte migrerar

### <a name="before-retirement"></a>Innan du pensionering

Det kan inte direkt påverkar dina Azure-tjänster eller sina arbetsbelastningar.  

### <a name="after-retirement"></a>Efter pensionering

Något anrop till klassiskt API: er som angavs tidigare misslyckas och returnerar felmeddelanden liknande följande:

För autoskalning: *Detta API är inaktuell. Använda Azure-portalen, Azure Monitor SDK, PowerShell, CLI eller Resource Manager-mallar för att hantera inställningarna för automatisk skalning*.  

För mått: *Detta API är inaktuell. Använd Azure portal, Azure Monitor-SDK, PowerShell, CLI för att fråga efter mått*.

## <a name="email-notifications"></a>E-postmeddelanden

Ett meddelande om tillbakadragande har skickats till e-postadresser för rollerna för följande konto: 

- -Konto och administratörer
- Medadministratörer  

Om du har några frågor kontaktar du oss på MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Referenser

- [Nyare REST-API: er för Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Nyare Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
