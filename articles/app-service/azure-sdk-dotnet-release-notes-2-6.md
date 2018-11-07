---
title: Azure SDK för .NET 2.6 viktig information
description: Azure SDK för .NET 2.6 viktig information
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 040eea1efd3abdbbfa65c24b2950ebece9d4acbc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258399"
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Azure SDK för .NET 2.6 viktig information
Det här dokumentet innehåller viktig information för Azure SDK för .NET 2.6 versionen. 

Du kan utveckla molnet tjänstprogram (PaaS) inriktning på .NET 4.5.2 eller .NET 4.6 förutsatt att du manuellt installera målet .NET Framework på rollen för molnet med Azure SDK 2.6. Se [installera .NET på en Molntjänstroll](https://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Service Bus-uppdateringar
* Event Hubs: 
  
  * Riktade åtkomstkontroll kan nu när du skickar händelser genom att exponera ytterligare publisher-slutpunkten för Event Hubs.
  * Ytterligare stabilitets- och förbättringar som lagts till i Event Hubs-funktionen.
  * Lägger till stöd för Amqp-protokollet via WebSocket för meddelanden och Event Hubs.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>HDInsight Tools för Visual Studio-uppdateringar
* **Förbättring av IntelliSense**: fjärrmetadata förslag
  
    HDInsight Tools för Visual Studio stöder nu hämtning av fjärrmetadata när du redigerar Hive-skript. Du kan till exempel skriva **Välj * från** och alla tabellnamn kommer att visas. Namn på kolumnerna kommer att visas när du har angett en tabell.
* **HDInsight emulator support**
  
    HDInsight Tools för Visual Studio stöder nu ansluta till HDInsight emulator, så att du kan utveckla dina Hive-skript lokalt utan att någon kostnad, och kör sedan dessa skript mot dina HDInsight-kluster. 
  
    Mer information finns i [handboken](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **HDInsight Tools för Visual Studio-stöd för Allmänt Hadoop-kluster** (förhandsversion)
  
    HDInsight Tools för Visual Studio stöder nu allmänt Hadoop-kluster, så du kan använda HDInsight Tools för Visual Studio för att göra följande:
  
  * ansluta till ditt kluster 
  * skriva Hive-fråga med förbättrat stöd för IntelliSense/automatisk komplettering, 
  * Visa alla jobb i ditt kluster med ett intuitivt användargränssnitt. 
    
    Mer information finns i [handboken](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Uppdateringar för cachelagring i Rollinstanser
* **Cachelagring i Rollinstanser** har uppdaterats för att använda **Microsoft Azure Storage SDK** version 4.3. Fram till nu den **Cache i roller** använde Azure Storage SDK version 1.7.
  
    Kunder med hjälp av Azure SDK 2.5 eller nedan bör uppdatera till Azure SDK 2.6 och flytta till den nya versionen av Azure Storage SDK. 
  
    Just nu kommer Azure Storage version 2011-08-18 att tas bort från den 1 augusti 2016. Alla migreringar med Cache i roller från Azure SDK 2.5 eller nedan måste 2.6 vara klar vid den tidpunkten. Läs mer på Azure Storage version 2011-08-18 tas ur bruk [Microsoft Azure Storage-tjänsten tas bort versionsuppdatering: 2016-tillägget](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Vi presenterar den 30 November 2016 indragningen för Azure Managed Cache Service och Azure cachelagring i Rollinstanser. Vi rekommenderar att du migrerar till Azure Redis Cache som förberedelse inför den här dras tillbaka. Mer information om datum och riktlinjer finns i [vilket Azure Cache-erbjudande är bäst för mig?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Azure App Service-verktyg
Följande objekt har uppdaterats i Azure SDK 2.6-versionen.

* Azure-publicering förbättrad med Azure API Apps som ett mål för distributionen.
* API Apps etablering funktioner du aktiverar användare med funktioner för skapande och etablering av API-App.
* Server Explorer ändrats för att återspegla den nya App Service noden med webb, mobil- och API apps grupperade efter resursgrupp.
* Lägg till Azure API-Appklient-gest som lagts till i de flesta C# projekt som gör att automatisk generering av Swagger-aktiverad API Apps som körs i en användares Azure-prenumeration.
* Verktyg för API Apps och App Service-noder i Server Explorer är tillgängliga i Visual Studio 2013 endast. 

## <a name="azure-resource-manager-tools-updates"></a>Azure Resource Manager Tools-uppdateringar
I Azure resource manager tools har uppdaterats med mallar för virtuella datorer, nätverk och lagring. JSON redigeringsmiljön har uppdaterats med en ny disposition-vy för mallar och möjligheten att redigera mallar med hjälp av JSON-kodfragment. Mallar som distribuerats från Visual Studio använder ett PowerShell-skript som medföljer projektet, så att ändringar som görs till skriptet som ska användas av Visual Studio.

## <a name="diagnostics-improvements-for-cloud-services"></a>Diagnostikförbättringar för Cloud Services
Azure SDK 2.6 hämtar tillbaka stöd för att samla in diagnostikloggar i Azure-beräkningsemulatorn och överföra dem till utvecklingslagring. Alla diagnostics-loggar (inklusive programspårning loggar, händelsespårning för Windows (ETW) loggar, prestandaräknare, infrastruktur-loggar och windows-händelseloggar) genereras när programmet körs i emulatorn kan överföras till utvecklingslagring verifiera att diagnostikloggning fungerar på din lokala dator. 

Lagringskonto för Startdiagnostik kan nu anges i filen service-konfiguration (.cscfg) vilket gör det enklare att använda olika diagnostiklagringskonton för olika miljöer. Det finns några viktiga skillnader mellan hur anslutningssträngen fungerade i Azure SDK 2.4 och Azure SDK 2.6. Läs mer om hur du använder anslutningssträngen för lagring för diagnostik och hur det påverkar dina projekt [konfigurera diagnostik för Azure Cloud Services](https://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
### <a name="azure-resource-manager-tools"></a>Verktyget Azure Resource Manager
* Den **projekt för distribution av molnet** projekttyp som är tillgängliga i Azure SDK 2.5 har bytt namn till **Azure-resursgrupp**.
* **Projekt för distribution i molnet** typ av projekt som skapats i Azure SDK 2.5 kan användas i 2.6 men distribuerar mallen från Visual Studio kommer att misslyckas. Distribuera med PowerShell-skriptet fortfarande fungerar dock som det gjorde tidigare.  Information om hur du använder **projekt för distribution av molnet** i 2.6 finns i det här [publicera](https://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Kända problem
* Samla in diagnostikloggar i emulatorn kräver ett 64-bitars operativsystem. Diagnostikloggar samlas inte in när du kör på ett 32-bitars operativsystem. Detta påverkar inte heller andra emulatorn. 
* Azure SDK 2.6 släpptes 4/29 2015 hade två problem: 
  
  * Universell App kunde inte läsas i Visual Studio 2015 när Azure SDK 2.6 installerades på datorn.
  * Misslyckas felsökning Cloud Service-projekt i Visual Studio 2013 och Visual Studio 2015 där Visual Studio svarar inte och kraschar medan du visar en dialogruta med meddelandet ”konfigurera diagnostik för emulatorn”.
    
    En uppdatering av Azure SDK 2.6 gavs ut 5/18/2015. Den uppdaterade versionen är 2.6.30508.1601; den innehåller korrigeringar för två problem som beskrivs ovan. Du kan identifiera versionen av SDK: N från Kontrollpanelen -> program och funktioner -> Microsoft Azure Tools för Microsoft Visual Studio 2013 – v 2.6. Versionskolumnen visar build-nummer.
    
    Om du fortfarande får dessa frågor, installera den senaste versionen av Azure 2.6 SDK för [VS 2012](https://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](https://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) eller [VS 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Se även
[Support och tillbakadragande Information om Azure SDK för .NET och API: er](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

