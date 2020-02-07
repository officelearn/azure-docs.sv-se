---
title: Azure Service Fabric-versioner
description: Viktig information för Azure Service Fabric. Innehåller information om de senaste funktionerna och förbättringarna i Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064249"
---
# <a name="service-fabric-releases"></a>Service Fabric versioner

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">fel söknings guider</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">ärende spårning</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Support alternativ</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">versioner som stöds</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">kod exempel</a>

Den här artikeln innehåller mer information om de senaste versionerna och uppdateringarna av Service Fabric Runtime och SDK: er.

## <a name="whats-new-in-service-fabric"></a>Vad är nytt i Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 är nu tillgängligt! Du kommer att kunna uppdatera till 7,0 via Azure Portal eller via en Azure Resource Manager distribution. På grund av kundernas feedback om versioner under den helgdags perioden börjar vi inte att automatiskt uppdatera kluster som är inställda för att ta emot automatiska uppgraderingar förrän januari.
I januari kommer vi att återuppta standard proceduren för distributionen och kluster med aktiverade automatiska uppgraderingar börjar ta emot 7,0-uppdateringen automatiskt. Vi kommer att tillhandahålla ett annat meddelande innan distributionen påbörjas.
Vi uppdaterar även våra planerade versions datum för att ange att den här principen ska beaktas. Här hittar du uppdateringar för våra framtida [versions scheman](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Det här är den senaste versionen av Service Fabric och har lästs in med viktiga funktioner och förbättringar.

### <a name="key-announcements"></a>Viktiga meddelanden
 - [**KeyVaultReference-stöd för program hemligheter (för hands version)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric program som har aktiverat [hanterade identiteter](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) kan nu direkt referera till en Key Vault hemlig URL som en miljö variabel, program parameter eller autentiseringsuppgifter för containerns lagrings plats. Service Fabric kommer automatiskt att lösa hemligheten med hjälp av programmets hanterade identitet. 
     
- **Förbättrad uppgraderings säkerhet för tillstånds lösa tjänster**: för att garantera tillgängligheten under en program uppgradering har vi introducerat nya konfigurationer för att definiera det [minsta antalet instanser för tillstånds lösa tjänster](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) som ska anses tillgängliga. Tidigare var det här värdet 1 för alla tjänster och kunde inte ändras. Med den nya säkerhets kontrollen per tjänst kan du se till att tjänsterna behåller ett minsta antal instanser under program uppgraderingar, kluster uppgraderingar och annat underhåll som förlitar sig på Service Fabric hälso-och säkerhets kontroller.
  
- [**Resurs gränser för användar tjänster**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): användare kan konfigurera resurs gränser för användar tjänsterna på en nod för att förhindra scenarier som resurs utbelastning av Service Fabric system tjänster. 
  
- [**Mycket hög service flytt kostnad**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) för en replik typ. Repliker med mycket hög flytt kostnad kommer bara att flyttas om det finns en begränsnings överträdelse i klustret som inte kan åtgärdas på något annat sätt. I dokumentationen finns mer information om när användningen av en "mycket hög" flytt kostnad är rimlig och ytterligare överväganden.
  
-  **Ytterligare säkerhets kontroller för klustret**: i den här versionen introducerade vi en konfigurerbar säkerhets kontroll för Dirigerings-nods kvorum. På så sätt kan du anpassa hur många startnoder som måste vara tillgängliga under kluster livs cykel-och hanterings scenarier. Åtgärder som tar klustret lägre än det konfigurerade värdet blockeras. I dag är standardvärdet alltid ett kvorum för startnoderna, till exempel om du har sju startnoder, kommer en åtgärd som tar dig under 5 startnoder att blockeras som standard. Med den här ändringen kan du göra det lägsta säkra värdet 6, vilket innebär att endast en Seed-nod kan vara nere i taget.
   
- Stöd har lagts till för [**att hantera säkerhets kopierings-och återställnings tjänsten i Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Detta gör följande aktiviteter möjligt direkt från SFX: identifiera säkerhets kopierings-och återställnings tjänsten, skapa en säkerhets kopierings princip, aktivera automatisk säkerhets kopiering, använda adhoc-säkerhetskopiering, utlösa återställnings åtgärder och söka efter befintliga säkerhets kopior.

- Vi presenterar tillgängligheten för [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): med det här verktyget kan du validera att de typer som används i pålitliga samlingar är framåtriktade och bakåtkompatibla under en rullande program uppgradering. Detta bidrar till att förhindra uppgraderings fel eller data förlust och skadade data på grund av saknade eller inkompatibla typer.

- [**Aktivera stabil läsning på sekundära repliker**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): stabila läsningar begränsar sekundära repliker till att returnera värden som har varit bekräftat.

Dessutom innehåller den här versionen andra nya funktioner, fel korrigeringar och förbättringar av support, tillförlitlighet och prestanda. En fullständig lista över ändringar finns i [viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Service Fabric 7,0-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 18 november 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 januari 2020 | [Uppdaterings version för Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 februari 2020 | [Uppdaterings version för Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6,5

Den här versionen innehåller förbättringar av support, tillförlitlighet och prestanda, nya funktioner, fel korrigeringar och förbättringar för att förenkla hanteringen av kluster och program livs cykeln.

> [!IMPORTANT]
> Service Fabric 6,5 är den slutliga versionen med stöd för Service Fabric-verktyg i Visual Studio 2015. Kunderna uppmanas att gå vidare till [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) .

Här är what's New i Service Fabric 6,5:

- Service Fabric Explorer innehåller ett [avbildningsarkiv visnings](service-fabric-visualizing-your-cluster.md#image-store-viewer) program för att inspektera program som du har överfört till avbildnings lagret.

- [POA-1.4.0 (patch Orchestration Application)](service-fabric-patch-orchestration-application.md) innehåller många själv diagnostiska förbättringar. [](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) Kunder med POA rekommenderas att flytta till den här versionen.

- [EventStore-tjänsten är aktive rad som standard](service-fabric-visualizing-your-cluster.md#event-store) i Service Fabric 6,5-kluster om du inte har valt ut.

- Har lagt till [replik livs cykel händelser](service-fabric-diagnostics-event-generation-operational.md#replica-events) för tillstånds känsliga tjänster.

- [Bättre insyn i status för Dirigerings nod](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inklusive varningar på kluster nivå om en*Seed-nod är ohälsosam (av*, *borttagen* eller *okänd*).

- Med [Service Fabric-verktyget för haveri beredskap för program](https://github.com/Microsoft/Service-Fabric-AppDRTool) kan Service Fabric tillstånds känsliga tjänster återställas snabbt när det primära klustret påträffar en katastrof. Data från det primära klustret synkroniseras kontinuerligt i det sekundära standby-programmet med hjälp av periodisk säkerhets kopiering och återställning.

- Visual Studio-stöd för [att publicera .net Core-appar till Linux-baserade kluster](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installeras automatiskt för Service Fabric 6,5 (och senare versioner) när du uppgraderar eller skapar ett nytt Linux-kluster i Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installeras som standard på MacOS/Linux Onebox behållaravbildningen-kluster.

Mer information finns i [versions anmärkningar för Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6,5-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 11 juni 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juli 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 oktober 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Tidigare versioner

### <a name="service-fabric-64-releases"></a>Service Fabric 6,4-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 30 november 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6,4 uppdaterings version för Windows-kluster](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 mars 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 april 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maj 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maj, 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
