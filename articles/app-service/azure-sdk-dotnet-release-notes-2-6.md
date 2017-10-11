---
title: "Azure SDK för .NET 2.6 viktig information"
description: "Azure SDK för .NET 2.6 viktig information"
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 21817b09440fc98a54dc45c9129d104b01fa387d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Azure SDK för .NET 2.6 viktig information
Det här dokumentet innehåller viktig information för Azure SDK för .NET 2.6-versionen. 

Du kan utveckla tjänsten molnprogram (PaaS) på .NET 4.5.2 eller .NET 4.6 under förutsättning att du manuellt installera Målversionen av .NET Framework på rolltjänsten moln med Azure SDK 2.6. Se [installera .NET på en rolltjänst för molnet](http://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Service Bus-uppdateringar
* Händelsehubbar: 
  
  * Kan nu riktade åtkomstkontroll när du skickar händelser genom att exponera ytterligare publisher slutpunkt för Händelsehubbar.
  * Ytterligare stabilitet och förbättringar som lagts till i Händelsehubbar-funktionen.
  * Lägger till stöd för Amqp protokoll över WebSocket för meddelanden och Händelsehubbar.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>HDInsight Tools för Visual Studio-uppdateringar
* **IntelliSense förbättring**: fjärrmetadata förslag
  
    HDInsight Tools för Visual Studio stöder nu hämtning fjärrmetadata när du redigerar ditt Hive-skript. Skriv till exempel **Välj * FROM** och alla tabellnamn kommer att visas. Kolumnnamnen visas när du har angett en tabell.
* **Stöd för HDInsight-emulator**
  
    HDInsight Tools för Visual Studio stöder nu ansluter till HDInsight-emulator, så du kan utveckla dina Hive-skript lokalt utan att några kostnader, kör dessa skript mot dina HDInsight-kluster. 
  
    Mer information finns i [handboken](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **HDInsight Tools för Visual Studio-stöd för Allmänt Hadoop-kluster** (förhandsgranskning)
  
    HDInsight Tools för Visual Studio stöder nu allmänt Hadoop-kluster, så du kan använda HDInsight Tools för Visual Studio för att göra följande:
  
  * ansluta till ditt kluster 
  * skriva Hive-fråga med förbättrad IntelliSense-autokomplettering stöd 
  * Visa alla jobb i klustret med ett intuitivt användargränssnitt. 
    
    Mer information finns i [handboken](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Uppdateringar för cachelagring i Rollinstanser
* **Cachelagring i Rollinstanser** har uppdaterats för att använda **Microsoft Azure Storage SDK: N** version 4.3. Fram till nu den **cachelagring i Rollinstanser** med Azure Storage SDK version 1.7.
  
    Kunder med hjälp av Azure SDK 2.5 eller nedan bör uppdatera till Azure SDK 2.6 och flytta till den nya versionen av Azure Storage SDK: N. 
  
    Just nu kommer Azure Storage version 2011-08-18 att tas bort 1 augusti 2016. Alla migreringar av cachelagring i Rollinstanser från Azure SDK 2.5 eller nedan måste 2.6 vara fullständig vid den tidpunkten. Mer information om tillbakadragningen av Azure Storage version 2011-08-18 finns [Microsoft Azure Storage Service borttagning versionsuppdatering: tillägget till 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Presenterar vi 30 November 2016 pensionering för Azure Managed Cache Service och cachelagring i Rollinstanser för Azure. Vi rekommenderar att du migrerar till Azure Redis-Cache som förberedelse för den här pensionering. Mer information om datum och vägledning för migrering finns [som Azure Cache erbjudande är rätt för mig?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Azure Apptjänst-verktyg
Följande objekt har uppdaterats i Azure SDK 2.6-versionen.

* Azure-publicering förbättrad om du vill inkludera Azure API Apps som mål för en distribution.
* API Apps etablering funktioner så att användarna med API-App funktioner och att skapa etablering.
* Server Explorer ändrats för att återspegla den nya App Service noden med webb-, mobil- och API apps grupperade efter resursgrupp.
* Lägg till Azure API-App-klient gest lagts till i de flesta C#-projekt som ska aktivera automatisk generering av Swagger-aktiverade API Apps körs i Azure-prenumeration för en användare.
* Verktygsuppsättning för API Apps och Apptjänst noder i Server Explorer är tillgängliga i Visual Studio 2013 endast. 

## <a name="azure-resource-manager-tools-updates"></a>Uppdaterar Azure Resource Manager-verktyg
Verktyg för Azure resource manager har uppdaterats med mallar för virtuella datorer, nätverk och lagring. JSON redigeringsmiljön har uppdaterats med en ny disposition vy för mallar och möjligheten att redigera mallar med hjälp av JSON kodavsnitt. Mallar som distribuerats från Visual Studio använder ett PowerShell-skript som ingår i projektet, så att ändringar som görs i skriptet kommer att användas av Visual Studio.

## <a name="diagnostics-improvements-for-cloud-services"></a>Förbättringar av diagnostik för molntjänster
Azure SDK 2.6 ger tillbaka stöd för att samla in diagnostik loggar i Azure-beräkningsemulatorn och överför dem till utveckling lagring. Alla diagnostik loggar (inklusive application trace händelsespårning för Windows (ETW)-loggar, prestandaräknare, infrastruktur händelseloggarna loggar och windows-loggar) genereras när programmet körs i emulatorn kan överföras till utveckling lagring och kontrollera att din diagnostikloggning fungerar på din lokala dator. 

Lagringskontot för Gatewaydiagnostik kan nu anges i filen service configuration (.cscfg) vilket gör det lättare att använda olika diagnostik lagringskonton för olika miljöer. Det finns några viktiga skillnader mellan hur anslutningssträngen fungerade i Azure SDK 2.4 och Azure SDK 2.6. Läs mer om hur du använder anslutningssträngen diagnostik lagring och hur den påverkar dina projekt [konfigurera diagnostik för Azure Cloud Services](http://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Gör ändringar
### <a name="azure-resource-manager-tools"></a>Azure Resource Manager-verktyg
* Den **projekt för distribution av molnet** projekttypen som är tillgängliga i Azure SDK 2.5 har bytt namn till **Azure-resursgrupp**.
* **Molnet projekt för distribution av** typ av projekt som har skapats i Azure SDK 2.5 kan användas i 2.6 men distribuerar mallen från Visual Studio kommer att misslyckas. Dock distribuerar med PowerShell-skriptet kommer att fungera som det gjorde tidigare.  Mer information om hur du använder **projekt för distribution av molnet** i 2.6 läsa det här [efter](http://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Kända problem
* Insamling av loggar för diagnostik i emulatorn kräver ett 64-bitars operativsystem. Diagnostik loggar samlas inte när du kör ett 32-bitars operativsystem. Detta påverkar inte andra funktioner i emulatorn. 
* Azure SDK 2.6 släpptes 4/29/2015 hade två problem: 
  
  * Universell App kunde inte läsas i Visual Studio 2015 när Azure SDK 2.6 installerades på datorn.
  * Misslyckas felsökning Cloud Service-projekt i Visual Studio 2013 och Visual Studio 2015 där Visual Studio svarar inte och kraschar när visas en dialogruta med meddelandet ”konfigurera diagnostik för emulatorn”.
    
    En uppdatering av Azure SDK 2.6 gavs ut 5/18/2015. Den uppdaterade versionen är 2.6.30508.1601; den innehåller korrigeringar för två problem som beskrivs ovan. Du kan identifiera genereringen av SDK från Kontrollpanelen -> program och funktioner -> Microsoft Azure-verktyg för Microsoft Visual Studio 2013 – v 2.6. Kolumnen Version visas build-nummer.
    
    Om du fortfarande inför dessa frågor, installera den senaste versionen av Azure 2.6 SDK för [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) eller [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Se även
[Support och tillbakadragning Information för Azure SDK för .NET och API: er](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

