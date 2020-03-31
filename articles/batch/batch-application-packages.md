---
title: Installera programpaket på beräkningsnoder - Azure Batch | Microsoft-dokument
description: Använd funktionen programpaket i Azure Batch för att enkelt hantera flera program och versioner för installation på batchberäkningsnoder.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30301832381bdc7b5f001eec2c449c571f9fd671
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086217"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Distribuera program för att beräkna noder med Batch-programpaket

Funktionen programpaket i Azure Batch ger enkel hantering av uppgiftsprogram och deras distribution till beräkningsnoderna i poolen. Med programpaket kan du ladda upp och hantera flera versioner av de program som dina uppgifter körs, inklusive deras stödfiler. Du kan sedan automatiskt distribuera ett eller flera av dessa program till beräkningsnoderna i poolen.

I den här artikeln får du lära dig hur du laddar upp och hanterar programpaket i Azure-portalen. Sedan får du lära dig hur du installerar dem på en pools beräkningsnoder med [batch .NET-biblioteket.][api_net]

> [!NOTE]
> Programpaket kan användas för alla Batch-pooler som skapats efter 5 juli 2017. De kan användas för Batch-pooler som skapats mellan 10 mars 2016 och 5 juli 2017, men endast om poolen skapades med en molntjänstkonfiguration. Programpaket kan inte användas för Batch-pooler som har skapats före 10 mars 2016.
>
> API:erna för att skapa och hantera programpaket är en del av [BATCH Management .NET-biblioteket.][api_net_mgmt] API:erna för att installera programpaket på en beräkningsnod är en del av [batch-NET-biblioteket.][api_net] Jämförbara funktioner finns i de tillgängliga batch-API:erna för andra språk. 
>
> Funktionen programpaket som beskrivs här ersätter funktionen Batch Apps som finns i tidigare versioner av tjänsten.

## <a name="application-package-requirements"></a>Krav på programpaket
Om du vill använda programpaket måste du [länka ett Azure Storage-konto](#link-a-storage-account) till ditt batchkonto.

## <a name="about-applications-and-application-packages"></a>Om program och programpaket
I Azure Batch refererar ett *program* till en uppsättning versionsbaserade binärfiler som automatiskt kan hämtas till beräkningsnoderna i poolen. Ett *programpaket* refererar till en *specifik uppsättning* av dessa binärfiler och representerar en viss *version* av programmet.

![Diagram över program och programpaket på hög nivå][1]

### <a name="applications"></a>Program
Ett program i Batch innehåller ett eller flera programpaket och anger konfigurationsalternativ för programmet. Ett program kan till exempel ange standardversionen av programpaketet som ska installeras på beräkningsnoder och om dess paket kan uppdateras eller tas bort.

### <a name="application-packages"></a>Programpaket
Ett programpaket är en ZIP-fil som innehåller program binärfiler och stödfiler som krävs för att dina uppgifter ska kunna köra programmet. Varje programpaket representerar en specifik version av programmet.

Du kan ange programpaket på pool- och aktivitetsnivåer. Du kan ange ett eller flera av dessa paket och (eventuellt) en version när du skapar en pool eller aktivitet.

* **Poolprogrampaket distribueras** till *varje* nod i poolen. Program distribueras när en nod ansluter till en pool och när den startas om eller görs om.
  
    Poolprogrampaket är lämpliga när alla noder i en pool kör ett jobbs uppgifter. Du kan ange ett eller flera programpaket när du skapar en pool och du kan lägga till eller uppdatera en befintlig pools paket. Om du uppdaterar en befintlig pools programpaket måste du starta om noderna för att installera det nya paketet.
* **Aktivitetsprogrampaket distribueras** endast till en beräkningsnod som schemalagts för att köra en aktivitet, precis innan aktivitetens kommandorad körs. Om det angivna programpaketet och versionen redan finns på noden distribueras det inte om och det befintliga paketet används.
  
    Aktivitetsprogrampaket är användbara i miljöer med delad pool, där olika jobb körs på en pool och poolen inte tas bort när ett jobb har slutförts. Om jobbet har färre aktiviteter än noder i poolen kan programpaket för aktiviteter minimera dataöverföringen eftersom ditt program bara distribueras till noderna som kör aktiviteterna.
  
    Andra scenarier som kan dra nytta av aktivitetsprogrampaket är jobb som kör ett stort program, men för endast ett fåtal aktiviteter. Till exempel kan en förbearbetningsfas eller en sammanfogningsuppgift, där förbearbetnings- eller kopplingsprogrammet är tungviktare, dra nytta av att använda uppgiftsprogrampaket.

> [!IMPORTANT]
> Det finns begränsningar för antalet program och programpaket inom ett Batch-konto och på den maximala programpaketstorleken. Mer information om dessa begränsningar finns [i Kvoter och begränsningar för Azure Batch-tjänsten.](batch-quota-limit.md)
> 
> 

### <a name="benefits-of-application-packages"></a>Fördelar med ansökningspaket
Programpaket kan förenkla koden i batch-lösningen och sänka de kostnader som krävs för att hantera de program som dina uppgifter kör.

Med programpaket behöver poolens startuppgift inte ange en lång lista med enskilda resursfiler som ska installeras på noderna. Du behöver inte manuellt hantera flera versioner av dina programfiler i Azure Storage eller på dina noder. Och du behöver inte oroa dig för att generera [SAS-url:er](../storage/common/storage-dotnet-shared-access-signature-part-1.md) för att ge åtkomst till filerna i ditt Lagringskonto. Batch fungerar i bakgrunden med Azure Storage för att lagra programpaket och distribuera dem för att beräkna noder.

> [!NOTE] 
> Den totala storleken på en startaktivitet måste vara mindre än eller lika med 32768 tecken, inklusive resursfiler och miljövariabler. Om startuppgiften överskrider den här gränsen är det ett annat alternativ att använda programpaket. Du kan också skapa ett zippat arkiv som innehåller dina resursfiler, ladda upp det som en blob till Azure Storage och sedan packa upp det från kommandoraden för din startuppgift. 
>
>

## <a name="upload-and-manage-applications"></a>Ladda upp och hantera program
Du kan använda [Azure-portalen][portal] eller API:erna för batchhantering för att hantera programpaketen i ditt batchkonto. I de närmaste avsnitten visar vi först hur du länkar ett lagringskonto, sedan diskuterar du att lägga till program och paket och hantera dem med portalen.

### <a name="link-a-storage-account"></a>Länka ett lagringskonto
Om du vill använda programpaket måste du först länka ett [Azure Storage-konto](batch-api-basics.md#azure-storage-account) till ditt batchkonto. Om du ännu inte har konfigurerat ett lagringskonto visas en varning i Azure-portalen första gången du klickar på **Program** i ditt batchkonto.



![Varning för inget lagringskonto konfigurerat i Azure-portalen][9]

Batch-tjänsten använder det associerade lagringskontot för att lagra dina programpaket. När du har länkat de två kontona kan Batch automatiskt distribuera paketen som lagras i det länkade lagringskontot till dina beräkningsnoder. Om du vill länka ett lagringskonto till ditt batchkonto klickar du på **Lagringskonto** i **fönstret Varning** och klickar sedan på **Lagringskonto** igen.

![Välj lagringskontoblad i Azure-portalen][10]

Vi rekommenderar att du skapar ett lagringskonto *specifikt* för användning med ditt Batch-konto och väljer det här. När du har skapat ett lagringskonto kan du sedan länka det till ditt batchkonto med hjälp av fönstret **Lagringskonto.**

> [!IMPORTANT] 
> - För närvarande kan du inte använda programpaket med ett Azure Storage-konto som är konfigurerat med [brandväggsregler](../storage/common/storage-network-security.md).
> - Ett Azure Storage-konto med **hierarkisk namnområde** inställt **på Aktiverat** kan inte användas för programpaket.

Batch-tjänsten använder Azure Storage för att lagra dina programpaket som blockblobar. Du [debiteras som vanligt][storage_pricing] för blockblobbdata och storleken på varje paket kan inte överskrida den maximala blockblolobstorleken. Mer information finns i [Azure Storage skalbarhet och prestandamål för lagringskonton](../storage/blobs/scalability-targets.md). Var noga med att överväga storleken och antalet av dina programpaket, och regelbundet ta bort föråldrade paket för att minimera kostnaderna.

### <a name="view-current-applications"></a>Visa aktuella program
Om du vill visa programmen i ditt Batch-konto klickar du på **menyalternativet Program** på den vänstra menyn när du visar ditt **batchkonto**.

![Panel för program][2]

Om du väljer det här menyalternativet öppnas **fönstret Program:**

![Lista program][3]

I det här fönstret visas ID:t för varje program i ditt konto och följande egenskaper:

* **Paket**: Antalet versioner som är associerade med det här programmet.
* **Standardversion**: Programversionen som är installerad om du inte anger någon version när du anger programmet för en pool. Den här inställningen är valfri.
* **Tillåt uppdateringar:** Värdet som anger om paketuppdateringar, borttagningar och tillägg tillåts. Om detta är inställt på **Nej**inaktiveras paketuppdateringar och borttagningar för programmet. Endast nya programpaketversioner kan läggas till. Standard är **Ja**.

Om du vill se filstrukturen för programpaketet på beräkningsnoden navigerar du till ditt Batch-konto i portalen. Från ditt Batch-konto navigerar du till **Pooler**. Välj den pool som innehåller den beräkningsnod som du är intresserad av.

![Noder i poolen][13]

När du har valt poolen navigerar du till den beräkningsnod som programpaketet är installerat på. Därifrån finns detaljerna i programpaketet **applications** i programmappen. Ytterligare mappar på beräkningsnoden innehåller andra filer, till exempel startuppgifter, utdatafiler, felutdata osv.

![Filer på nod][14]

### <a name="view-application-details"></a>Visa programinformation
Om du vill se information om ett program väljer du programmet i **fönstret Program.**

![Programinformation][4]

I programinformationen kan du konfigurera följande inställningar för ditt program.

* **Tillåt uppdateringar:** Ange om programpaketen kan uppdateras eller tas bort. Se "Uppdatera eller ta bort ett programpaket" senare i den här artikeln.
* **Standardversion**: Ange ett standardprogrampaket som ska distribueras för att beräkna noder.
* **Visningsnamn**: Ange ett eget namn som batch-lösningen kan använda när den visar information om programmet, till exempel i användargränssnittet för en tjänst som du tillhandahåller dina kunder via Batch.

### <a name="add-a-new-application"></a>Lägga till ett nytt program
Om du vill skapa ett nytt program lägger du till ett programpaket och anger ett nytt, unikt program-ID. Det första programpaketet som du lägger till med det nya program-ID:et skapar också det nya programmet.

Klicka på Lägg**till** **program** > .

![Nytt programblad i Azure-portalen][5]

I fönstret **Nytt program** finns följande fält för att ange inställningarna för det nya programmet och programpaketet.

**Program-ID:t**

Det här fältet anger ID för ditt nya program, som omfattas av standardverifieringsreglerna för Azure Batch ID. Reglerna för att tillhandahålla ett ansöknings-ID är följande:

* På Windows-noder kan ID innehålla valfri kombination av alfanumeriska tecken, bindestreck och understreck. På Linux-noder tillåts endast alfanumeriska tecken och understreck.
* Det går inte att innehålla fler än 64 tecken.
* Måste vara unikt i batchkontot.
* Är fall-bevara och fall-okänslig.

**Version**

Det här fältet anger vilken version av programpaketet du laddar upp. Versionssträngar omfattas av följande verifieringsregler:

* På Windows-noder kan versionssträngen innehålla valfri kombination av alfanumeriska tecken, bindestreck, understreck och punkter. På Linux-noder kan versionssträngen bara innehålla alfanumeriska tecken och understreck.
* Det går inte att innehålla fler än 64 tecken.
* Måste vara unikt i programmet.
* Är fall-bevara och fall-okänslig.

**Programpaket**

Det här fältet anger zip-filen som innehåller programbinärerna och stödfilerna som krävs för att köra programmet. Klicka på rutan **Välj en fil** eller mappikonen för att bläddra till och markera en ZIP-fil som innehåller programmets filer.

När du har valt en fil klickar du på **OK** för att påbörja överföringen till Azure Storage. När överföringen är klar visar portalen ett meddelande. Beroende på storleken på filen som du laddar upp och hastigheten på nätverksanslutningen kan den här åtgärden ta lite tid.

> [!WARNING]
> Stäng inte fönstret **Nytt program** innan uppladdningen är klar. Om du gör det stoppas uppladdningsprocessen.
> 
> 

### <a name="add-a-new-application-package"></a>Lägga till ett nytt programpaket
Om du vill lägga till en programpaketversion för ett befintligt program markerar du ett program i **programfönstren** och klickar på **Paket** > **Lägg till**.

![Lägg till programpaketblad i Azure-portalen][8]

Som du kan se matchar fälten fälten fälten i det **nya programfönstret,** men **rutan Program-ID** är inaktiverad. Precis som du gjorde för det nya programmet anger du **versionen** för det nya paketet, bläddrar till **zip-filen för programpaketet** och klickar sedan på **OK** för att ladda upp paketet.

### <a name="update-or-delete-an-application-package"></a>Uppdatera eller ta bort ett programpaket
Om du vill uppdatera eller ta bort ett befintligt programpaket öppnar du informationen för programmet, klickar på **Paket,** klickar på **ellipsen** på raden i programpaketet som du vill ändra och väljer den åtgärd som du vill utföra.

![Uppdatera eller ta bort paket i Azure Portal][7]

**Uppdatering**

När du klickar på **Uppdatera**visas **uppdateringspaketfönstren.** Det här fönstret liknar fönstret **Nytt programpaket,** men endast paketvalsfältet är aktiverat, så att du kan ange en ny ZIP-fil som ska överföras.

![Uppdatera paketblad i Azure-portalen][11]

**Ta bort**

När du klickar på **Ta bort**uppmanas du att bekräfta borttagningen av paketversionen och Batch tar bort paketet från Azure Storage. Om du tar bort standardversionen av ett program tas **standardversionsinställningen** bort för programmet.

![Ta bort program][12]

## <a name="install-applications-on-compute-nodes"></a>Installera program på beräkningsnoder
Nu när du har lärt dig hur du hanterar programpaket med Azure-portalen kan vi diskutera hur du distribuerar dem för att beräkna noder och köra dem med Batch-uppgifter.

### <a name="install-pool-application-packages"></a>Installera poolprogrampaket
Om du vill installera ett programpaket på alla beräkningsnoder i en pool anger du en eller flera *programpaketreferenser* för poolen. De programpaket som du anger för en pool installeras på varje beräkningsnod när noden ansluter till poolen och när noden startas om eller omimageras.

I Batch .NET anger du en eller flera [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] när du skapar en ny pool eller för en befintlig pool. Klassen [ApplicationPackageReference][net_pkgref] anger ett program-ID och en version som ska installeras på en pools beräkningsnoder.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Om en programpaketdistribution misslyckas av någon anledning markerar batch-tjänsten noden [oanvändbar][net_nodestate]och inga aktiviteter schemaläggs för körning på den noden. I det här fallet bör du **starta om** noden för att starta om paketdistributionen. Om du startar om noden kan du också schemalägga aktiviteten igen på noden.
> 
> 

### <a name="install-task-application-packages"></a>Installera programpaket för aktivitet
I likhet med en pool anger du *programpaketreferenser* för en aktivitet. När en aktivitet är schemalagd att köras på en nod hämtas paketet och extraheras precis innan aktivitetens kommandorad körs. Om ett angivet paket och en viss version redan är installerad på noden hämtas inte paketet och det befintliga paketet används.

Om du vill installera ett uppgiftsprogrampaket konfigurerar du aktivitetens [CloudTask][net_cloudtask]. [Egenskapen ApplicationPackageReferences:][net_cloudtask_pkgref]

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Köra de installerade programmen
De paket som du har angett för en pool eller uppgift hämtas och `AZ_BATCH_ROOT_DIR` extraheras till en namngiven katalog i noden. Batch skapar också en miljövariabel som innehåller sökvägen till den namngivna katalogen. Aktivitetskommandoraderna använder den här miljövariabeln när programmet refereras till på noden. 

På Windows-noder är variabeln i följande format:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

På Linux-noder är formatet något annorlunda. Punkter (.), bindestreck (-) och taltecken (#) förenklas till understreck i miljövariabeln. Observera också att fallet med program-ID:et bevaras. Ett exempel:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`och `version` är värden som motsvarar den program- och paketversion som du har angett för distributionen. Om du till exempel angav att version 2.7 av *programmixer* ska installeras på Windows-noder, använder aktivitetskommandoraderna den här miljövariabeln för att komma åt sina filer:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

På Linux-noder anger du miljövariabeln i det här formatet. Förenkla perioderna (.), bindestreck (-) och taltecken (#) till understreck och bevara fallet med program-ID:et:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

När du laddar upp ett programpaket kan du ange en standardversion som ska distribueras till beräkningsnoderna. Om du har angett en standardversion för ett program kan du utelämna versionssuffixet när du refererar till programmet. Du kan ange standardprogramversionen i Azure-portalen, i **fönstret Program,** som visas i [Ladda upp och hantera program](#upload-and-manage-applications).

Om du till exempel anger "2.7" som standardversion för *programmixer*och dina uppgifter refererar till följande miljövariabel, körs version 2.7 i Windows-noderna:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Följande kodavsnitt visar en exempeluppgiftskommandorad som startar standardversionen av *mixerprogrammet:*

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Mer information om inställningar för beräkningsnoder finns i [Miljöinställningar för funktionen](batch-api-basics.md#environment-settings-for-tasks) Gruppera för aktiviteter i översikten för [funktionen Gruppera.](batch-api-basics.md)
> 
> 

## <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool
Om en befintlig pool redan har konfigurerats med ett programpaket kan du ange ett nytt paket för poolen. Om du anger en ny paketreferens för en pool gäller följande:

* Batch-tjänsten installerar det nyligen angivna paketet på alla nya noder som ansluter till poolen och på alla befintliga noder som startas om eller återskapas.
* Beräkningsnoder som redan finns i poolen när du uppdaterar paketreferenserna installerar inte automatiskt det nya programpaketet. Dessa beräkningsnoder måste startas om eller göras om för att ta emot det nya paketet.
* När ett nytt paket distribueras återspeglar de skapade miljövariablerna de nya programpaketreferenserna.

I det här exemplet har den befintliga poolen version 2.7 av *mixerprogrammet* konfigurerat som en av dess [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. Om du vill uppdatera poolens noder med version 2.76b anger du en ny [ApplicationPackageReference][net_pkgref] med den nya versionen och genomför ändringen.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Nu när den nya versionen har konfigurerats installerar batch-tjänsten version 2.76b till en *ny* nod som ansluter till poolen. Om du vill installera 2,76b på noderna som *redan* finns i poolen startar du om eller vill ta igen dem. Observera att omstartade noder behåller filerna från tidigare paketdistributioner.

## <a name="list-the-applications-in-a-batch-account"></a>Lista programmen i ett batchkonto
Du kan lista programmen och deras paket i ett Batch-konto med hjälp av [ApplicationOperations][net_appops]. [Metoden ListApplicationSummaries.][net_appops_listappsummaries]

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Avsluta
Med programpaket kan du hjälpa dina kunder att välja program för sina jobb och ange den exakta versionen som ska användas när du bearbetar jobb med din Batch-aktiverade tjänst. Du kan också ge dina kunder möjlighet att ladda upp och spåra sina egna program i din tjänst.

## <a name="next-steps"></a>Nästa steg
* [Batch REST API][api_rest] ger också stöd för att arbeta med programpaket. Se till exempel [elementet applicationPackageReferences][rest_add_pool_with_packages] i [Lägg till en pool i ett konto][rest_add_pool] för information om hur du anger paket som ska installeras med hjälp av REST API. Mer [Applications][rest_applications] information om hur du hämtar programinformation finns i API:et för batch-REST.
* Lär dig hur du programmässigt [hanterar Azure Batch-konton och kvoter med Batch Management .NET](batch-management-dotnet.md). [Batchhantering .NET-biblioteket][api_net_mgmt] kan aktivera kontoskapande och borttagningsfunktioner för batchprogrammet eller -tjänsten.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Programpaket på hög nivå diagram"
[2]: ./media/batch-application-packages/app_pkg_02.png "Programpanelen i Azure-portalen"
[3]: ./media/batch-application-packages/app_pkg_03.png "Programblad i Azure-portalen"
[4]: ./media/batch-application-packages/app_pkg_04.png "Bladet programinformation i Azure-portalen"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nytt programblad i Azure-portalen"
[7]: ./media/batch-application-packages/app_pkg_07.png "Listrutan Uppdatera eller ta bort paket i Azure Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nytt programpaketblad i Azure-portalen"
[9]: ./media/batch-application-packages/app_pkg_09.png "Ingen länkad lagringskontoavisering"
[10]: ./media/batch-application-packages/app_pkg_10.png "Välj lagringskontoblad i Azure-portalen"
[11]: ./media/batch-application-packages/app_pkg_11.png "Uppdatera paketblad i Azure-portalen"
[12]: ./media/batch-application-packages/app_pkg_12.png "Dialogrutan Ta bort paketbekräftelse i Azure Portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Beräkningsinformation för nod i Azure-portalen"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Filer på beräkningsnoden som visas i Azure-portalen"
