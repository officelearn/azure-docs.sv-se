---
title: "Övervaka en Azure-molntjänst | Microsoft Docs"
description: "Beskriver vad övervakning av en Azure-molntjänst innebär och vilken del av alternativen är."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: adegeo
ms.openlocfilehash: f3a3a1beb8540ee8ab0502379396c06ea505fb44
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="introduction-to-cloud-service-monitoring"></a>Introduktion till Molntjänsten övervakning

Du kan övervaka prestandarelaterade nyckeltal för en tjänst i molnet. Varje rolltjänst för molnet samlar in minimal data: CPU-användning, nätverksanvändning och diskanvändning. Om Molntjänsten har den `Microsoft.Azure.Diagnostics` tillägg som tillämpas på en roll, rollen kan samla in ytterligare datapunkterna. Den här artikeln innehåller en introduktion till Azure-diagnostik för molntjänster.

Med grundläggande övervakning prestandaräknardata från rollinstanser provtagning och samlas in på 3-minutersintervall. Den här grundläggande övervakningsdata lagras inte i ditt lagringskonto och har utan extra kostnader som är kopplade till den.

Med avancerad övervakning är ytterligare mått provtagning och samlas in regelbundet på 5 minuter, 1 timme och 12 timmar. Sammanställda data lagras i ett lagringskonto i tabeller och tas bort efter 10 dagar. Storage-konto som används har konfigurerats med rollen. Du kan använda olika lagringskonton för olika roller. Detta är konfigurerat med en anslutningssträng i den [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) och [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) filer.


## <a name="basic-monitoring"></a>Grundläggande övervakning

Som anges i inledningen, en tjänst i molnet samlar automatiskt in grundläggande övervakning från den virtuella datorn på värden. Dessa data inkluderar CPU-procent, nätverk in/ut och läsning/skrivning till disk. Insamlade övervakningsdata visas automatiskt på sidorna Översikt och mått för Molntjänsten i Azure-portalen. 

Grundläggande övervakning kräver inte ett lagringskonto. 

![grundläggande Molntjänsten övervakning paneler](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Avancerad övervakning

Avancerad övervakning är med hjälp av den **Azure Diagnostics** tillägget (och eventuellt Application Insights SDK) på vilken roll som du vill övervaka. Tillägget diagnostik använder en konfigurationsfil (per roll) med namnet **diagnostics.wadcfgx** att konfigurera diagnostik-mätvärden som övervakas. Azure-diagnostik tillägget samlar in och lagrar data i Azure Storage-konto. Dessa inställningar konfigureras på den **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), och [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) filer. Detta innebär att det finns en extra kostnader som är associerade med avancerad övervakning.

När varje roll skapas lägger Visual Studio till Azure-diagnostik tillägget till den. Det här tillägget diagnostics kan samla in följande typer av information:

* Anpassade prestandaräknare
* Programloggar
* Windows-händelseloggar
* Händelsekällan för .NET
* IIS-loggar
* Manifestet baserat ETW
* Krasch minnesdumpar
* Kunden felloggar

> [!IMPORTANT]
> När dessa data slås samman till lagringskontot på portalen har **inte** ett enhetligt sätt att diagrammets data. Vi rekommenderar starkt att du integrerar en annan tjänst som Application Insights i ditt program.

## <a name="setup-diagnostics-extension"></a>Konfigurera diagnostik-tillägg

Första, om du inte har en **klassiska** lagringskonto [skapar du en](../storage/common/storage-create-storage-account.md#create-a-storage-account). Kontrollera att lagringskontot har skapats med den **klassiska distributionsmodellen** angivna.

Gå sedan till den **Storage-konto (klassisk)** resurs. Välj **inställningar** > **åtkomstnycklar** och kopiera den **primära anslutningssträngen** värde. Du behöver det här värdet för Molntjänsten. 

Det finns två konfigurationsfiler för avancerad diagnostik som ska aktiveras måste du ändra **ServiceDefinition.csdef** och **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

I den **ServiceDefinition.csdef** lägger du till en ny inställning med namnet `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` för varje roll som använder avancerad diagnostik. Visual Studio lägger till det här värdet i filen när du skapar ett nytt projekt. Om den saknas, kan du lägga till den nu. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Detta definierar en ny inställning måste läggas till varje **ServiceConfiguration.cscfg** fil. 

Troligen har två **.cscfg** filer, en med namnet **ServiceConfiguration.cloud.cscfg** för att distribuera till Azure och som heter **ServiceConfiguration.local.cscfg** som används för lokala distributioner i emulerade miljö. Öppna och ändra varje **.cscfg** fil. Lägg till en inställning med namnet `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Ange värdet till den **primära anslutningssträngen** för klassiska storage-konto. Om du vill använda lokal lagring på utvecklingsdatorn använda `UseDevelopmentStorage=true`.

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

När du publicerar Molntjänsten från Visual Studio, ges möjlighet att skicka diagnostisk data till Application Insights. Du kan skapa Application Insights Azure-resurshanteraren då eller skicka data till en befintlig Azure-resurs. Din molntjänst kan övervakas av Application Insights för tillgänglighet, prestanda, fel och användning. Anpassade diagram kan läggas till Application Insights så att du kan se data som är mest viktiga. Rollen instans kan samlas in med hjälp av Application Insights SDK i ditt molntjänstprojekt. Mer information om hur du integrerar Application Insights finns [Application Insights med molntjänster](../application-insights/app-insights-cloudservices.md).

Observera att du kan använda Application Insights för att visa prestandaräknare (och andra inställningar) du har angett via tillägg för Windows Azure-diagnostik du endast får en bättre upplevelse genom att integrera Application Insights SDK i din Worker och webbtjänst roller.


## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om Application Insights med molntjänster](../application-insights/app-insights-cloudservices.md)
- [Ställ in prestandaräknare](diagnostics-performance-counters.md)

