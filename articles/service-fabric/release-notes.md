---
title: Azure Service Fabric-versioner
description: Viktig information för Azure Service Fabric. Innehåller information om de senaste funktionerna och förbättringarna i Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064249"
---
# <a name="service-fabric-releases"></a>Service Fabric-utgåvor

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Felsökningsguider</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Problemspårning</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Supportalternativ</a> 
| stöds<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">versioner</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Kodexempel</a>

Den här artikeln innehåller mer information om de senaste versionerna och uppdateringarna av Service Fabric-körningen och SDK:er.

## <a name="whats-new-in-service-fabric"></a>Nyheter i Service Fabric

### <a name="service-fabric-70"></a>Service Tyg 7,0

Azure Service Fabric 7.0 är nu tillgängligt! Du kan uppdatera till 7.0 via Azure-portalen eller via en Azure Resource Manager-distribution. På grund av feedback från kunder på utgåvor runt semesterperioden kommer vi inte att börja automatiskt uppdatera kluster som ska ta emot automatiska uppgraderingar förrän i januari.
I januari kommer vi att återuppta standardutrullningsproceduren och kluster med automatiska uppgraderingar aktiverade börjar ta emot 7.0-uppdateringen automatiskt. Vi kommer att ge ett nytt tillkännagivande innan utbyggnaden börjar.
Vi kommer också att uppdatera våra planerade utgivningsdatum för att indikera att vi tar hänsyn till denna policy. Titta här för uppdateringar om våra framtida [release scheman.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)
 
Detta är den senaste versionen av Service Fabric och är laddad med viktiga funktioner och förbättringar.

### <a name="key-announcements"></a>Viktiga meddelanden
 - [**KeyVaultReference-stöd för programhemligheter (förhandsversion):**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)Service Fabric-program som har aktiverat [hanterade identiteter](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) kan nu direkt referera till en hemlig URL för Key Vault som en miljövariabel, programparameter eller autentiseringsuppgifter för behållardatabasen. Service Fabric löser automatiskt hemligheten med hjälp av programmets hanterade identitet. 
     
- **Förbättrad uppgraderingssäkerhet för tillståndslösa tjänster:** För att garantera tillgänglighet under en programuppgradering har vi infört nya konfigurationer för att definiera [det minsta antalet instanser för tillståndslösa tjänster](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) som ska anses vara tillgängliga. Tidigare var detta värde 1 för alla tjänster och kunde inte ändras. Med den här nya säkerhetskontrollen per tjänst kan du se till att dina tjänster behåller ett minsta antal upp-instanser under programuppgraderingar, klusteruppgraderingar och annat underhåll som är beroende av Service Fabric:s hälso- och säkerhetskontroller.
  
- [**Resursgränser för användartjänster:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)Användare kan ställa in resursgränser för användartjänster på en nod för att förhindra scenarier som resursutmattning av Service Fabric-systemtjänsterna. 
  
- [**Mycket hög tjänst flytta kostnad**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) för en repliktyp. Repliker med mycket hög flyttkostnad flyttas endast om det finns ett begränsningsfel i klustret som inte kan åtgärdas på något annat sätt. Se dokumenten för ytterligare information om när användningen av en "Mycket hög" flyttkostnad är rimlig och för ytterligare överväganden.
  
-  **Ytterligare kluster säkerhetskontroller:** I den här versionen introducerade vi en konfigurerbar frö nod kvorum säkerhetskontroll. På så sätt kan du anpassa hur många seed-noder som måste vara tillgängliga under klustrets livscykel- och hanteringsscenarier. Åtgärder som skulle ta klustret under det konfigurerade värdet blockeras. Idag är standardvärdet alltid kvorum för frönoderna, till exempel om du har 7 frönoder, skulle en åtgärd som tar dig under 5 frönoder blockeras som standard. Med den här ändringen kan du göra det lägsta säkra värdet 6, vilket skulle tillåta endast en frönod att vara nere i taget.
   
- Lade till stöd för [**hantering av säkerhetskopiering och återställning i Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Detta gör följande aktiviteter möjliga direkt inifrån SFX: upptäcka säkerhetskopiering och återställning tjänst, skapa säkerhetskopiering politik, aktivera automatiska säkerhetskopior, ta adhoc säkerhetskopior, utlöser återställningsåtgärder och bläddra befintliga säkerhetskopior.

- Tillkännage tillgängligheten för [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Det här verktyget hjälper till att validera att typer som används i tillförlitliga samlingar är framåt och bakåtkompatibla under en rullande programuppgradering. Detta förhindrar uppgraderingsfel eller dataförlust och dataskador på grund av saknade eller inkompatibla typer.

- [**Aktivera stabila läsningar på sekundära repliker**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1):Stabila läsningar begränsar sekundära repliker till returnerande värden som har kvorum-acked.

Dessutom innehåller den här versionen andra nya funktioner, buggfixar och supportability, tillförlitlighet och prestandaförbättringar. För den fullständiga listan över ändringar, se [viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0-utgåvor

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 18 november 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| den 30 januari 2020 | [Uppdateringsversionen av Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| den 6 februari 2020 | [Uppdateringsversionen av Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Tyg 6,5

Den här versionen innehåller support, tillförlitlighet och prestandaförbättringar, nya funktioner, buggfixar och förbättringar för att underlätta hantering av kluster- och programlivscykel.

> [!IMPORTANT]
> Service Fabric 6.5 är den slutliga versionen med Service Fabric-verktyg i Visual Studio 2015. Kunderna rekommenderas att flytta till [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) framöver.

Här är nyheter i Service Fabric 6.5:

- Service Fabric Explorer innehåller en [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) för att granska program som du har laddat upp till bildarkivet.

- [PA-version](service-fabric-patch-orchestration-application.md) [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) innehåller många självdiagnostiska förbättringar. Kunder till POA rekommenderas att flytta till den här versionen.

- [EventStore-tjänsten är aktiverad som standard](service-fabric-visualizing-your-cluster.md#event-store) för Service Fabric 6.5-kluster om du inte har valt bort.

- Lade till [repliklivscykelhändelser](service-fabric-diagnostics-event-generation-operational.md#replica-events) för tillståndskänsliga tjänster.

- [Bättre synlighet för seed-nodstatus,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)inklusive varningar på klusternivå om en frönod är felaktig (*Ned,* *Borttagen* eller *Okänd*).

- [Service Fabric Application Disaster Recovery Tool](https://github.com/Microsoft/Service-Fabric-AppDRTool) gör att service fabric-tillståndskänsliga tjänster kan återställas snabbt när det primära klustret stöter på en katastrof. Data från det primära klustret synkroniseras kontinuerligt i det sekundära vänteprogrammet med periodisk säkerhetskopiering och återställning.

- Visual Studio-stöd för [publicering av .NET Core-appar till Linux-baserade kluster](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installeras automatiskt för Service Fabric 6.5 (och senare versioner) när du uppgraderar eller skapar ett nytt Linux-kluster på Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installeras som standard på MacOS/Linux OneBox-kluster.

Mer information finns i [Service Fabric 6.5 Release Notes](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5-utgåvor

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| den 11 juni 2019 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| den 2 juli 2019 | [Uppdateringsversionen av Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Uppdateringsversionen av Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| den 23 augusti 2019 | [Uppdateringsversionen av Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| den 14 oktober 2019 | [Uppdateringsversionen av Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Tidigare versioner

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4-utgåvor

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| den 30 november 2018 | [Azure-tjänstinfrastruktur 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| den 12 december 2018 | [Uppdateringsversionen av Azure Service Fabric 6.4 för Windows-kluster](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| den 4 februari 2019 | [Uppdateringsversionen av Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| den 4 mars 2019 | [Uppdateringsversionen av Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| den 8 april 2019 | [Uppdateringsversionen av Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| den 2 maj 2019 | [Uppdateringsversionen av Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| den 28 maj 2019 | [Uppdateringsversionen av Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
