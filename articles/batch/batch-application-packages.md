---
title: Installera programpaket på compute-noder – Azure Batch | Microsoft Docs
description: Använd funktionen programmet paket i Azure Batch för att enkelt hantera flera program och versioner för installation på Batch compute-noder.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/15/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fd3eccf3de5d46520dc5a50cab66667c875799e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454617"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Distribuera program till beräkningsnoder med Batch-programpaket

Funktionen programmet paket i Azure Batch tillhandahåller enkelt hantera uppgiftsprogram och deras distribution till beräkningsnoderna i poolen. Med programpaket kan du ladda upp och hantera flera versioner av program som dina aktiviteter ska köra, inklusive tillhörande stödfiler. Du kan sedan automatiskt distribuera en eller flera av dessa program till beräkningsnoderna i poolen.

I den här artikeln får lära du att ladda upp och hantera programpaket i Azure-portalen. Du lär dig sedan att installera dem på en poolens beräkningsnoder med den [Batch .NET] [ api_net] biblioteket.

> [!NOTE]
> 
> Programpaket kan användas för alla Batch-pooler som skapats efter 5 juli 2017. De kan användas för Batch-pooler som skapats mellan 10 mars 2016 och 5 juli 2017, men endast om poolen skapades med en molntjänstkonfiguration. Programpaket kan inte användas för Batch-pooler som har skapats före 10 mars 2016.
>
> API: er för att skapa och hantera programpaket är en del av den [Batch Management .NET] [ api_net_mgmt] biblioteket. API: er för att installera programpaket på en beräkningsnod är en del av den [Batch .NET] [ api_net] biblioteket. Jämförbara funktioner finns i de tillgängliga Batch API: er för andra språk. 
>
> Funktionen programmet paket som beskrivs här ersätter funktionen Batch Apps finns i tidigare versioner av tjänsten.
> 
> 

## <a name="application-package-requirements"></a>Paketet programkrav
Om du vill använda programpaket måste du [länkar ett Azure Storage-konto](#link-a-storage-account) till Batch-kontot.

## <a name="about-applications-and-application-packages"></a>Om program och programpaket
I Azure Batch, en *program* refererar till en uppsättning version binärfiler som kan hämtas automatiskt till beräkningsnoderna i poolen. En *programpaket* refererar till en *kravspecifikationer* dessa binärfiler och representerar en viss *version* av programmet.

![Översiktsdiagram över program och programpaket][1]

### <a name="applications"></a>Appar
Ett program i Batch innehåller en eller flera program paket och anger alternativ för programmet. Ett program kan till exempel ange standardversion för program som paketet ska installeras på compute-noder och om dess paket kan uppdateras eller tas bort.

### <a name="application-packages"></a>Programpaket
Programpaket är en .zip-fil som innehåller programmets binärfiler och stödfiler som krävs för dina aktiviteter att köra programmet. Varje programpaket representerar en specifik version av programmet.

Du kan ange programpaket på pool- och programnivå. Du kan ange en eller flera av dessa paket och (frivilligt) en version när du skapar en pool eller uppgift.

* **Lagringspoolen programpaket** distribueras till *varje* nod i poolen. Program distribueras när en nod ansluter till en pool och när den startas om eller avbildning återställs.
  
    Programpaket för pooler är lämplig när alla noder i poolen kör aktiviteterna i ett jobb. Du kan ange en eller flera programpaket när du skapar en pool, och du kan lägga till eller uppdatera en befintlig pool paket. Om du uppdaterar en befintlig pool-programpaket måste du starta om dess noder om du vill installera det nya paketet.
* **Uppgift programpaket** distribueras enbart till en beräkningsnod som schemalagts att köra en uppgift, precis innan du kör kommandoraden för aktiviteten. Om det angivna programpaketet och den version redan på noden kan den på att omdistribueras inte och det befintliga paketet används.
  
    Programpaket är användbara i miljöer med delade pooler, där olika jobb körs i en pool och poolen tas inte bort när jobbet har slutförts. Om jobbet har färre aktiviteter än noder i poolen kan programpaket för aktiviteter minimera dataöverföringen eftersom ditt program bara distribueras till noderna som kör aktiviteterna.
  
    Andra scenarier som kan dra nytta av programpaket finns jobb som kör ett stort program, men endast för några aktiviteter. Till exempel ett förväg bearbetnings steg eller en merge-uppgift, bearbeta data i förväg eller merge-programmet är där tunga, kan ha nytta av programpaket.

> [!IMPORTANT]
> Det finns begränsningar för hur många program och programpaket i Batch-konto och på den största storleken för paketet. Se [kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md) mer information om dessa begränsningar.
> 
> 

### <a name="benefits-of-application-packages"></a>Fördelarna med programpaket
Programpaket kan förenkla koden i din Batch-lösning och sänka den overhead som krävs för att hantera de program som dina aktiviteter kör.

Med programpaket kan behöver din poolens startuppgift inte ange en lång lista med filer som enskild resurs ska installeras på noderna. Du behöver att manuellt hantera flera versioner av dina filer i Azure Storage eller på noderna. Och du behöver inte bekymra dig om hur du skapar [SAS URL: er](../storage/common/storage-dotnet-shared-access-signature-part-1.md) att ge åtkomst till filer i ditt Storage-konto. Batch fungerar i bakgrunden med Azure Storage för att lagra programpaket och distribuera dem till beräkningsnoder.

> [!NOTE] 
> Den totala storleken på en startaktivitet måste vara mindre än eller lika med 32768 tecken, inklusive resursfiler och miljövariabler. Om startaktiviteten överskrider den här gränsen kan är sedan använda programpaket ett annat alternativ. Du kan också skapa ett Zippat Arkiv som innehåller dina resursfiler, ladda upp dem som en blob till Azure Storage och packa upp den från kommandoraden för startaktiviteten. 
>
>

## <a name="upload-and-manage-applications"></a>Ladda upp och hantera program
Du kan använda den [Azure-portalen] [ portal] eller Batch Management API: erna att hantera programpaket i Batch-kontot. I nästa avsnitt visar vi först hur du länkar ett lagringskonto och diskutera att lägga till program och paket och hantera dem med portalen.

### <a name="link-a-storage-account"></a>Länka ett Storage-konto
Om du vill använda programpaket måste du först länka en [Azure Storage-konto](batch-api-basics.md#azure-storage-account) till Batch-kontot. Om du ännu inte har konfigurerat en Storage-konto, Azure-portalen visar en varning som första gången du klickar på **program** i Batch-kontot.



![”Inget lagringskonto har konfigurerats, varning i Azure-portalen][9]

Batch-tjänsten använder det associera lagringskontot för att lagra dina programpaket. När du har länkat de två kontona kan de paket som lagras i länkade Storage-kontot till compute-noder att distribuera automatiskt i Batch. För att länka ett lagringskonto till Batch-kontot, klickar du på **lagringskonto** på den **varning** , och klicka på **Lagringskonto** igen.

![Välj bladet lagringskonto i Azure-portalen][10]

Vi rekommenderar att du skapar ett lagringskonto *specifikt* för användning med ditt Batch-konto och väljer det här. När du har skapat ett lagringskonto, sedan kan du länka den till ditt Batch-konto med hjälp av den **Lagringskonto** fönster.

> [!NOTE] 
> För närvarande du inte använda programpaket med ett Azure Storage-konto som har konfigurerats med [brandväggsregler](../storage/common/storage-network-security.md).
> 

Batch-tjänsten använder Azure Storage för att lagra dina programpaket som blockblobar. Du är [debiteras som vanligt] [ storage_pricing] för blockblob data och storleken på varje paket får inte överskrida den [maximala blob blockstorlek](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets). Glöm inte att beakta storlek och antalet dina programpaket och regelbundet ta bort föråldrad paket för att minimera kostnaderna.
> 
> 

### <a name="view-current-applications"></a>Visa aktuella program
Om du vill visa program i Batch-kontot, klickar du på den **program** menyalternativet i den vänstra menyn vid visning av din **Batch-konto**.

![Program sida vid sida][2]

Om du väljer alternativet öppnas den **program** fönster:

![Visa lista med program][3]

Det här fönstret visar ID för varje program i ditt konto och följande egenskaper:

* **Paket**: Antal versioner som är associerade med det här programmet.
* **Standardversion**: Programmets version installeras om du inte anger en version när du anger att programmet för en pool. Den här inställningen är valfri.
* **Tillåt uppdateringar**: Det värde som anger om paketet uppdateringar, borttagningar och tillägg är tillåtna. Om detta är inställt på **nr**, paketuppdateringar och borttagningar är inaktiverat för programmet. Endast nya programpaketversioner kan läggas till. Standardinställningen är **Ja**.

### <a name="view-application-details"></a>Visa programinformation
Om du vill se information om ett program, väljer du programmet i den **program** fönster.

![Programinformation][4]

Du kan konfigurera följande inställningar för ditt program i informationen om programmet.

* **Tillåt uppdateringar**: Ange om dess programpaket kan uppdateras eller tas bort. Se ”uppdatera eller ta bort ett programpaket” senare i den här artikeln.
* **Standardversion**: Ange ett standard-programpaket för att distribuera till beräkningsnoder.
* **Visningsnamn**: Ange ett eget namn som Batch-lösningen kan använda när den visas information om programmet, till exempel i Användargränssnittet för en tjänst som du ge dina kunder via Batch.

### <a name="add-a-new-application"></a>Lägg till ett nytt program
Om du vill skapa ett nytt program, lägga till ett programpaket och ange ett nytt, unikt program-ID. Det första programpaket som du lägger till med den nya program-ID skapar även det nya programmet.

Klicka på **Program** > **Lägg till**.

![Ny programblad i Azure-portalen][5]

Den **nytt program** fönstret visar följande fält om du vill ange inställningarna för ditt nya program och programpaket.

**Program-id**

Det här fältet anger ID för det nya programmet som omfattas av standardregler för Azure Batch-ID-verifiering. Regler för att tillhandahålla ett program-ID är följande:

* På Windows-noder, kan ID: T innehålla en kombination av alfanumeriska tecken, bindestreck och understreck. På Linux-noder tillåts endast alfanumeriska tecken och understreck.
* Får inte innehålla fler än 64 tecken.
* Måste vara unikt i Batch-konto.
* Är bevara och skiftlägeskänsliga.

**Version**

Det här fältet anger vilken version av programpaketet som du överför. Version strängar omfattas valideringsreglerna för följande:

* Versionsträngen kan innehålla en kombination av alfanumeriska tecken, bindestreck, understreck och punkter på Windows-noder. På Linux-noder innehåller Versionsträngen endast alfanumeriska tecken och understreck.
* Får inte innehålla fler än 64 tecken.
* Måste vara unikt inom programmet.
* Är bevara och skiftlägeskänsliga.

**Programpaket**

Det här fältet anger .zip-fil som innehåller programmets binärfiler och stödfiler som krävs för att köra programmet. Klicka på den **Välj en fil** box eller mappikonen för att bläddra till och markera en .zip-fil som innehåller dina programfiler.

När du har valt en fil, klickar du på **OK** att börja ladda upp till Azure Storage. När överföringen är klar visar ett meddelande i portalen. Den här åtgärden kan ta lite tid beroende på storleken på den fil som du laddar upp och hastighet för nätverksanslutningen.

> [!WARNING]
> Stäng inte det **nytt program** fönstret innan överföringen är klar. Detta stoppar överföringen.
> 
> 

### <a name="add-a-new-application-package"></a>Lägg till ett nytt programpaket
Om du vill lägga till en version av programpaketet för ett befintligt program, Välj ett program i den **program** windows och klicka på **paket** > **Lägg till**.

![Lägg till application package-bladet i Azure-portalen][8]

Som du kan se fälten stämmer överens med den **nytt program** fönstret men **program-id** rutan är inaktiverad. Som du gjorde för det nya programmet ange den **Version** för ditt nya paket, bläddra till din **programpaket** .zip filen och klicka sedan på **OK** att ladda upp paketet.

### <a name="update-or-delete-an-application-package"></a>Uppdatera eller ta bort ett programpaket
Om du vill uppdatera eller ta bort ett befintligt programpaket, öppnar du informationen för programmet, klickar du på **paket**, klickar du på den **ellipsen** i raden i det programpaket som du vill ändra och välj den åtgärd som du vill utföra.

![Uppdatera eller ta bort paketet i Azure-portalen][7]

**Uppdatering**

När du klickar på **uppdatering**, **uppdateringspaketet** visas. Det här fönstret liknar den **nytt programpaket** fönstret, men endast fältet för val av paketet är aktiverat, så att du kan ange en ny zipfil att ladda upp.

![Uppdatera paketet bladet i Azure-portalen][11]

**Ta bort**

När du klickar på **ta bort**du uppmanas att bekräfta borttagningen av paketversionen och Batch tar bort paketet från Azure Storage. Om du tar bort standardversionen av ett program, den **standardversion** inställningen tas bort för programmet.

![Ta bort program ][12]

## <a name="install-applications-on-compute-nodes"></a>Installera program på beräkningsnoder
Nu när du har lärt dig hur du hanterar programpaket med Azure portal, diskuterar vi hur du distribuerar dem för att beräkna noder och kör dem med Batch-aktiviteterna.

### <a name="install-pool-application-packages"></a>Installera programpaket för pooler
Om du vill installera ett programpaket på alla beräkningsnoder i poolen, ange en eller flera programpaket *referenser* för poolen. Programpaket som du anger för en pool installeras på varje compute-nod när noden läggs till i poolen och när noden startas om eller avbildning återställs.

Ange en eller flera i Batch .NET [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] när du skapar en ny pool eller för en befintlig pool. Den [ApplicationPackageReference] [ net_pkgref] klass anger en program-ID och version för att installera på en pool compute-noder.

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
> Om en programdistribution för paketet misslyckas av någon anledning, Batch-tjänsten markerar noden [oanvändbara][net_nodestate], och inga aktiviteterna schemaläggs för körning på noden. I det här fallet bör du **starta om** noden för att återuppta paketdistributionen. En omstart av noden kan också schemalägga aktivitet igen på noden.
> 
> 

### <a name="install-task-application-packages"></a>Installera programpaket
Precis som en pool kan du ange programpaket *referenser* för en aktivitet. När en uppgift har schemalagts att köras på en nod är paketet hämtade och extraherade precis innan aktivitetens kommandorad körs. Om ett angivet paket och version är redan installerad på noden, paketet laddas inte ned och det befintliga paketet används.

Om du vill installera ett programpaket i uppgift att konfigurera aktivitetens [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] egenskapen:

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

## <a name="execute-the-installed-applications"></a>Kör de installerade programmen
De paket som du har angett för en pool eller uppgift laddas ned och extraheras till en namngiven katalog i den `AZ_BATCH_ROOT_DIR` för noden. Batch skapar även en miljövariabel som innehåller sökvägen till katalogen. Din kommandorader använda den här miljövariabeln när du refererar till programmet på noden. 

På Windows-noder är variabeln i följande format:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Formatet är något annorlunda på Linux-noder. Punkter (.), bindestreck (-) och nummertecken (#) förenklas till understreck i miljövariabeln. Observera också att i fallet med program-ID bevaras. Exempel:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` och `version` är värden som motsvarar den program- och versionen som du har angett för distribution. Exempel: Om du har angett den versionen 2.7 av programmet *blender* ska installeras på Windows-noder, din kommandorader använder den här miljövariabeln komma åt dess filer:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Ange miljövariabeln på Linux-noder i det här formatet. Förenkla punkter (.), bindestreck (-) och nummertecken (#) till understreck och bevara skiftläget för program-ID:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

När du överför ett programpaket kan du ange en standardversion för att distribuera till dina beräkningsnoder. Om du har angett en standardversion för ett program, kan du utelämna suffixet version när du referera till programmet. Du kan ange standardversion för program i Azure-portalen i den **program** som det visas i [ladda upp och hantera program](#upload-and-manage-applications).

Exempel: Om du anger ”2.7” som standardversion för programmet *blender*, och dina aktiviteter referera till följande miljövariabler och sedan dina Windows-noder kör version 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Följande kodavsnitt visar ett exempel aktivitetens kommandorad som startar standardversionen av den *blender* program:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Se [miljöinställningar för aktiviteter](batch-api-basics.md#environment-settings-for-tasks) i den [Batch-funktionsöversikten](batch-api-basics.md) för mer information om miljöinställningar för compute-nod.
> 
> 

## <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool
Om en befintlig pool har redan konfigurerats med ett programpaket, kan du ange ett nytt paket för poolen. Om du anger ett nya paketreferens för en pool, följande gäller:

* Batch-tjänsten installerar det nya angivna paketet på alla nya noder som ansluter till poolen och på en befintlig nod som startas om eller avbildning återställs.
* Compute-noder som redan finns i poolen när du uppdaterar paketet refererar till inte installerar automatiskt nya programpaket. Dessa compute-noder måste startas om eller avbildning för att ta emot det nya paketet.
* När ett nytt paket distribueras återspeglar miljövariablerna som skapade de nya referenserna för programpaket.

I det här exemplet befintlig pool har version 2.7 av den *blender* program som konfigurerats som en av dess [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. Ange en ny för att uppdatera poolens noder med version 2.76b [ApplicationPackageReference] [ net_pkgref] med den nya versionen och spara ändringen.

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

Nu när den nya versionen har konfigurerats, Batch-tjänsten installerar version 2.76b till någon *nya* nod som ansluter till poolen. Installera 2.76b på de noder som är *redan* i poolen, starta om eller återavbilda dem. Observera att omstartad noder behålla filerna från den tidigare paketet distributioner.

## <a name="list-the-applications-in-a-batch-account"></a>En lista med program i Batch-konto
Du kan lista program och deras paket i ett Batch-konto med hjälp av den [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] metod.

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
Med programpaket kan hjälpa du dina kunder Välj program för sitt arbete och ange den exakta versionen ska användas för att bearbeta jobb med Batch-aktiverat tjänsten. Du kan också ange möjligheten för kunderna att ladda upp och spåra sina egna program i din tjänst.

## <a name="next-steps"></a>Nästa steg
* Den [Batch REST API] [ api_rest] innehåller också stöd för att arbeta med programpaket. Se exempelvis den [applicationPackageReferences] [ rest_add_pool_with_packages] element i [lägga till en pool till ett konto] [ rest_add_pool] information om hur du anger paket att installera med hjälp av REST-API. Se [program] [ rest_applications] mer information om hur du skaffar information om program med hjälp av Batch REST-API.
* Lär dig hur du programmässigt [hantera Azure Batch-konton och kvoter med Batch Management .NET](batch-management-dotnet.md). Den [Batch Management .NET] [ api_net_mgmt] biblioteket kan aktivera konto skapas eller tas bort för Batch-programmet eller tjänsten.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Översiktsdiagram för program-paket"
[2]: ./media/batch-application-packages/app_pkg_02.png "Program sida vid sida i Azure-portalen"
[3]: ./media/batch-application-packages/app_pkg_03.png "Program-bladet i Azure-portalen"
[4]: ./media/batch-application-packages/app_pkg_04.png "Information om programblad i Azure-portalen"
[5]: ./media/batch-application-packages/app_pkg_05.png "Ny programblad i Azure-portalen"
[7]: ./media/batch-application-packages/app_pkg_07.png "Uppdatera eller ta bort paket listrutan i Azure-portalen"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nya paket programblad i Azure-portalen"
[9]: ./media/batch-application-packages/app_pkg_09.png "Inga länkade Storage-konto avisering"
[10]: ./media/batch-application-packages/app_pkg_10.png "Välj bladet lagringskonto i Azure-portalen"
[11]: ./media/batch-application-packages/app_pkg_11.png "Uppdatera paketet bladet i Azure-portalen"
[12]: ./media/batch-application-packages/app_pkg_12.png "Ta bort paketet bekräftelsedialogrutan i Azure-portalen"
