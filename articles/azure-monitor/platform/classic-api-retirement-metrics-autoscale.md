---
title: Azure Monitor pensionering av klassiska API:er för distributionsmodell för mått och automatisk skalning
description: Mått och automatisk skalning av klassiska API:er, även kallade ASM -distributionsmodell (Azure Service Management) eller RDFE-distributionsmodell som dras tillbaka
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 9dfa6b278587f4ed79b1c3cd9eff1defd09ec0bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294663"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor pensionering av klassiska API:er för distributionsmodell för mått och automatisk skalning

Azure Monitor (tidigare Azure Insights när den först släpptes) erbjuder för närvarande möjligheten att skapa och hantera inställningar för automatisk skalning för och använda mått från klassiska virtuella datorer och klassiska Molntjänster. Den ursprungliga uppsättningen klassiska distributionsmodellbaserade API:er kommer att **dras tillbaka efter den 30 juni 2019** i alla offentliga och privata Azure-moln i alla regioner.   

Samma åtgärder har stötts genom en uppsättning Azure Resource Manager-baserade API:er i över ett år. Azure-portalen använder de nya REST-API:erna för både automatisk skalning och mått. En ny SDK, PowerShell och CLI baserat på dessa Resource Manager API:er är också tillgängliga. Våra partnertjänster för övervakning förbrukar de nya Resource Manager-baserade REST-API:erna i Azure Monitor.  

## <a name="who-is-not-affected"></a>Vem påverkas inte

Om du hanterar automatisk skalning via Azure-portalen, de [nya Azure Monitor SDK-,](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)PowerShell-, CLI- eller Resource Manager-mallarna, krävs ingen åtgärd.  

Om du förbrukar mått via Azure-portalen eller via olika [övervakningspartnertjänster](../../azure-monitor/platform/partners.md)krävs ingen åtgärd. Microsoft arbetar med övervakningspartner för att migrera till de nya API:erna.

## <a name="who-is-affected"></a>Vem påverkas

Den här artikeln gäller dig om du använder följande komponenter:

- **Klassiska Azure Insights SDK** – Om du använder den [klassiska Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)växlar du till att använda nya Azure Monitor SDK för [.NET](https://github.com/azure/azure-libraries-for-net#download) eller [Java](https://github.com/azure/azure-libraries-for-java#download). Ladda ned [Azure Monitor SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klassisk automatisk skalning** – Om du anropar [api:erna](https://msdn.microsoft.com/library/azure/mt348562.aspx) för klassiska inställningar för automatisk skalning från dina specialbyggda verktyg eller använder det [klassiska Azure Insights SDK-](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)-bör du växla till att använda [RESOURCE Manager Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Klassiska mått** – Om du konsumerar mått med de [klassiska REST-API:erna](https://msdn.microsoft.com/library/azure/dn510374.aspx) eller [klassiska Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) från specialbyggda verktyg bör du växla till att använda [RESOURCE Manager Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Om du är osäker på om koden eller de anpassade verktygen anropar de klassiska API:erna tittar du på följande:

- Granska URI:n som visas i koden eller verktyget. De klassiska API:erna https://management.core.windows.netanvänder URI. Du bör använda den nyare URI för resource manager-baserade API:er som börjar med `https://management.azure.com/`.

- Jämför monteringsnamnet på din maskin. Den äldre klassiska https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/monteringen är på .

- Om du använder certifikatautentisering för att komma åt mått eller API:er för automatisk skalning använder du en klassisk slutpunkt och ett bibliotek. De nyare Resource Manager-API:erna kräver Azure Active Directory-autentisering via ett tjänsthuvudnamn eller användarobjekt.

- Om du använder samtal som refereras i dokumentationen vid någon av följande länkar använder du de äldre klassiska API:erna.

  - [Windows.Azure.Management.Övervaka klassbibliotek](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Övervakning (klassisk) .NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations-gränssnitt](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Varför du ska byta

Alla befintliga funktioner för automatisk skalning och mått fortsätter att fungera via de nya API:erna.  

Migrera över till nyare API:er levereras med Resource Manager-baserade funktioner, till exempel stöd för konsekvent rollbaserad åtkomstkontroll (RBAC) i alla dina övervakningstjänster. Du får också ytterligare funktioner för mått: 

- stöd för dimensioner
- konsekvent 1-minuters metrisk granularitet för alla tjänster 
- bättre fråga
- högre datalagring (93 dagar med mått jämfört med 30 dagar) 

Sammantaget, liksom alla andra tjänster i Azure, resource manager baserade Azure Monitor API: er levereras med bättre prestanda, skalbarhet och tillförlitlighet. 

## <a name="what-happens-if-you-do-not-migrate"></a>Vad händer om du inte migrerar

### <a name="before-retirement"></a>Före pensioneringen

Det kommer inte att finnas någon direkt inverkan på dina Azure-tjänster eller deras arbetsbelastningar.  

### <a name="after-retirement"></a>Efter pensioneringen

Alla anrop till de klassiska API:er som tidigare angetts misslyckas och returnerar felmeddelanden liknande följande:

För automatisk skalning: *Det här API:et har inaktuellt. Använd mallarna Azure Portal, Azure Monitor SDK, PowerShell, CLI eller Resource Manager för att hantera inställningar för automatisk skalning*.  

För mått: *Det här API:et har inaktuellt. Använd Azure-portalen, Azure Monitor SDK, PowerShell, CLI för att fråga efter mått*.

## <a name="email-notifications"></a>E-postmeddelanden

Ett meddelande om pensionering skickades till e-postadresser för följande kontoroller: 

- Konto- och tjänstadministratörer
- Coadministrators (mässor)  

Om du har några frågor, kontakta oss på MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Referenser

- [Nyare REST-API:er för Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Nyare Azure-övervakarE SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
