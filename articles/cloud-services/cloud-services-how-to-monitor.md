---
title: Övervaka en Azure Cloud-tjänst | Microsoft-dokument
description: Beskriver vad övervakning av en Azure Cloud-tjänst innebär och vilka alternativ du har.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 01/29/2018
ms.author: tagore
ms.openlocfilehash: 61c794ba03934ae1828ba310f3f776bfb61b652b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273103"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Introduktion till övervakning av molntjänster

Du kan övervaka nyckelprestandamått för alla molntjänster. Varje molntjänstroll samlar in minimala data: CPU-användning, nätverksanvändning och diskanvändning. Om molntjänsten har `Microsoft.Azure.Diagnostics` tillägget tillämpas på en roll, kan den rollen samla in ytterligare datapunkter. Den här artikeln innehåller en introduktion till Azure Diagnostics for Cloud Services.

Med grundläggande övervakning samplas prestandaräknaredata från rollinstanser och samlas in med 3-minutersintervall. Dessa grundläggande övervakningsdata lagras inte i ditt lagringskonto och har ingen extra kostnad kopplad till den.

Med avancerad övervakning samplas och samlas ytterligare mått in med intervall på 5 minuter, 1 timme och 12 timmar. Aggregerade data lagras i ett lagringskonto, i tabeller och rensas efter 10 dagar. Det lagringskonto som används konfigureras av rollen. Du kan använda olika lagringskonton för olika roller. Detta konfigureras med en anslutningssträng i [CSDEF-](cloud-services-model-and-package.md#servicedefinitioncsdef) och [CSCFG-filerna.](cloud-services-model-and-package.md#serviceconfigurationcscfg)


## <a name="basic-monitoring"></a>Grundläggande övervakning

Som anges i inledningen samlar en molntjänst automatiskt in grundläggande övervakningsdata från den virtuella värddatorn. Dessa data inkluderar CPU-procent, nätverk in/ut och diskläsning/skrivning. De insamlade övervakningsdata visas automatiskt på översikts- och måttsidorna för molntjänsten i Azure-portalen. 

Grundläggande övervakning kräver inget lagringskonto. 

![grundläggande molntjänstövervakningspaneler](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Avancerad övervakning

Avancerad övervakning innebär att använda **Azure Diagnostics-tillägget** (och eventuellt Application Insights SDK) på den roll du vill övervaka. Diagnostiktillägget använder en config-fil (per roll) med namnet **diagnostics.wadcfgx** för att konfigurera diagnostikmåtten som övervakas. Azure Diagnostic-tillägget samlar in och lagrar data i ett Azure Storage-konto. Dessa inställningar konfigureras i **filerna .wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)och [.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg) Detta innebär att det finns en extra kostnad i samband med avancerad övervakning.

När varje roll skapas lägger Visual Studio till Azure Diagnostics-tillägget i det. Det här diagnostiktillägget kan samla in följande typer av information:

* Anpassade prestandaräknare
* Programloggar
* Windows-händelseloggar
* .NET-händelsekälla
* IIS-loggar
* Manifestbaserat ETW
* Kraschdumpar
* Kundfelloggar

> [!IMPORTANT]
> Alla dessa data sammanställs i lagringskontot, men portalen tillhandahåller **inte** ett eget sätt att kartlägga data. Vi rekommenderar starkt att du integrerar en annan tjänst, till exempel Application Insights, i ditt program.

## <a name="setup-diagnostics-extension"></a>Tillägg för installation av diagnostik

Om du inte har ett **klassiskt** lagringskonto [skapar du först ett](../storage/common/storage-account-create.md). Kontrollera att lagringskontot skapas med den angivna **klassiska distributionsmodellen.**

Navigera sedan till resursen **Lagringskonto (klassiskt).** Välj **Inställningar** > **Access-nycklar** och kopiera värdet för den **primära anslutningssträngen.** Du behöver det här värdet för molntjänsten. 

Det finns två config filer som du måste ändra för avancerad diagnostik som ska aktiveras, **ServiceDefinition.csdef** och **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

Lägg till en ny inställning som heter `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` för varje roll som använder avancerad diagnostik i filen **ServiceDefinition.csdef.** Visual Studio tillför det här värdet i filen när du skapar ett nytt projekt. Om det saknas kan du lägga till det nu. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Detta definierar en ny inställning som måste läggas till i varje **ServiceConfiguration.cscfg-fil.** 

Troligtvis har du två **CSCFG-filer,** en med namnet **ServiceConfiguration.cloud.cscfg** för distribution till Azure och en med namnet **ServiceConfiguration.local.cscfg** som används för lokala distributioner i den emulerade miljön. Öppna och ändra varje **CSCFG-fil.** Lägg till `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`en inställning med namnet . Ange värdet till den **primära anslutningssträngen** för det klassiska lagringskontot. Om du vill använda det lokala lagringsutrymmet `UseDevelopmentStorage=true`på utvecklingsmaskinen använder du .

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Använda programinsikter

När du publicerar molntjänsten från Visual Studio får du möjlighet att skicka diagnostikdata till Application Insights. Du kan skapa Azure-programmet Application Insights vid den tidpunkten eller skicka data till en befintlig Azure-resurs. Din molntjänst kan övervakas av Application Insights för tillgänglighet, prestanda, fel och användning. Anpassade diagram kan läggas till i Application Insights så att du kan se de data som är viktigast. Rollinstansdata kan samlas in med hjälp av Application Insights SDK i ditt molntjänstprojekt. Mer information om hur du integrerar Application Insights finns i [Application Insights med Cloud Services](../azure-monitor/app/cloudservices.md).

Observera att även om du kan använda Application Insights för att visa prestandaräknare (och de andra inställningar) som du har angett via tillägget Windows Azure Diagnostics, får du bara en rikare upplevelse genom att integrera SDK för Programinsikter i ditt arbetar- och webbroller.


## <a name="next-steps"></a>Nästa steg

- [Läs mer om programinsikter med Molntjänster](../azure-monitor/app/cloudservices.md)
- [Ställ in prestandaräknare](diagnostics-performance-counters.md)




