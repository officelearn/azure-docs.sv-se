---
title: Övervaka en Azure-molntjänst | Microsoft Docs
description: Beskriver vad övervaka ett Azure Cloud Services omfattar och vilka några av alternativen är.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: jeconnoc
ms.openlocfilehash: 844fef9a87c1db06c6415c59d4be26caf928382b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432912"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Introduktion till Molntjänsten övervakning

Du kan övervaka nyckelprestandamått för valfri molntjänst. Varje molntjänstroll samlar in minimal data: CPU-användning, nätverksanvändning och diskanvändning. Om Molntjänsten har den `Microsoft.Azure.Diagnostics` tillägg som tillämpas på en roll, rollen kan samla in ytterligare datapunkterna. Den här artikeln innehåller en introduktion till Azure-diagnostik för molntjänster.

Med grundläggande övervakning är prestandaräknardata från rollinstanser samplas och som samlas in med 3-minuters mellanrum. Den här grundläggande övervakningsdata lagras inte i ditt storage-konto och har utan extra kostnad som är associerat med den.

Med avancerad övervakning är ytterligare mått samplas och som samlas in med intervall på 5 minuter, 1 timme och 12 timmar. Sammanställda data lagras i ett lagringskonto i tabeller, och tas bort efter 10 dagar. Lagringskontot som används har konfigurerats av roll; Du kan använda olika lagringskonton för olika roller. Detta är konfigurerat med en anslutningssträng i den [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) och [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) filer.


## <a name="basic-monitoring"></a>Grundläggande övervakning

Enligt informationen i inledningen samlar en molntjänst automatiskt in grundläggande övervakningsdata från den virtuella datorn på värden. Dessa data inkluderar CPU-procentandel, inkommande och utgående och läsning/skrivning till disk. Insamlade övervakningsdata visas automatiskt på sidorna Översikt och mått för Molntjänsten i Azure-portalen. 

Grundläggande övervakning kräver inte ett lagringskonto. 

![grundläggande molntjänst som övervakning paneler](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Avancerad övervakning

Avancerad övervakning är att använda den **Azure Diagnostics** tillägget (och eventuellt Application Insights SDK) på vilken roll som du vill övervaka. Diagnostics-tillägget använder en konfigurationsfil (per roll) med namnet **diagnostics.wadcfgx** att konfigurera diagnostik-mått som övervakas. Tillägget Azure Diagnostics samlar in och lagrar data i ett Azure Storage-konto. De här inställningarna är konfigurerade i den **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), och [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) filer. Det innebär att det inte finns någon extra kostnad som hör till avancerad övervakning.

Eftersom varje roll har skapats lägger Visual Studio till Azure Diagnostics-tillägget till den. Den här diagnostiktillägget kan samla in följande typer av information:

* Anpassade prestandaräknare
* Programloggar
* Windows-händelseloggar
* .NET-händelsekälla
* IIS-loggar
* Manifestbaserat ETW
* Kraschdumpar
* Kund-felloggar

> [!IMPORTANT]
> Även om dessa data slås samman till lagringskontot på portalen gör **inte** är ett enhetligt sätt att diagrammets data. Vi rekommenderar starkt att du integrerar en annan tjänst, till exempel Application Insights i ditt program.

## <a name="setup-diagnostics-extension"></a>Konfigurera diagnostics-tillägg

Första, om du inte har en **klassiska** storage-konto, [skapar ett](../storage/common/storage-quickstart-create-account.md). Kontrollera att lagringskontot har skapats med den **klassiska distributionsmodellen** angivna.

Gå sedan till den **lagringskonto (klassiskt)** resurs. Välj **inställningar** > **åtkomstnycklar** och kopiera den **primär anslutningssträng** värde. Du behöver det här värdet för Molntjänsten. 

Det finns två konfigurationsfiler för avancerad diagnostik är aktiverat måste du ändra **ServiceDefinition.csdef** och **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

I den **ServiceDefinition.csdef** Lägg till en ny inställning med namnet `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` för varje roll som använder avancerad diagnostik. Visual Studio lägger du till det här värdet i filen när du skapar ett nytt projekt. Om den saknas kan du lägga till den nu. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Detta definierar en ny inställning som måste läggas till varje **ServiceConfiguration.cscfg** fil. 

Troligen har två **.cscfg** filer, en med namnet **ServiceConfiguration.cloud.cscfg** för att distribuera till Azure och som heter **ServiceConfiguration.local.cscfg** som används för lokal distribution i emulerade miljön. Öppna och ändra varje **.cscfg** fil. Lägg till en inställning med namnet `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Ange värdet till den **primär anslutningssträng** av det klassiska lagringskontot. Om du vill använda den lokala lagringen på utvecklingsdatorn använda `UseDevelopmentStorage=true`.

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

## <a name="use-application-insights"></a>Använda Application Insights

När du publicerar Molntjänsten från Visual Studio har möjlighet att skicka diagnostiska data till Application Insights. Du kan skapa Application Insights Azure-resurser vid den tiden eller skicka data till en befintlig Azure-resurs. Din molntjänst kan övervakas av Application Insights för tillgänglighet, prestanda, fel och användning. Anpassade diagram kan läggas till Application Insights så att du kan se data som är mest viktigt. Rollen instansdata kan samlas in med hjälp av Application Insights SDK i ditt molntjänstprojekt. Mer information om hur du integrerar Application Insights finns i [Application Insights med Cloud Services](../azure-monitor/app/cloudservices.md).

Observera att du kan använda Application Insights för att visa prestandaräknare (och andra inställningar) du har angett via Windows Azure Diagnostics-tillägget du bara hämta en rikare upplevelse genom att integrera Application Insights SDK till din Worker och webbtjänst roller.


## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om Application Insights med Cloud Services](../azure-monitor/app/cloudservices.md)
- [Ställ in prestandaräknare](diagnostics-performance-counters.md)

