---
title: Installera programpaket på datornoderna - Azure Batch | Microsoft Docs
description: Använd funktionen programmet paket i Azure Batch enkelt kan hantera flera program och versioner för installation på Batch-beräkningsnoder.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/06/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f982e859892965379b7ffb08e15dd1cf51b9801f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31515687"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Distribuera program till compute-noder med Batch-programpaket

Funktionen programmet paket i Azure Batch tillhandahåller enkel hantering av aktiviteten program och deras distribution till compute-noder i din pool. Du kan använda programpaket, för att ladda upp och hantera flera versioner av de program som kör aktiviteter, inklusive tillhörande stödfiler. Du kan sedan automatiskt distribuera en eller flera av dessa program till compute-noder i din pool.

I den här artikeln beskrivs hur du överför och hantera programpaket i Azure-portalen. Du lär dig sedan att installera dem på en pool compute-noderna med den [Batch .NET] [ api_net] bibliotek.

> [!NOTE]
> 
> Programpaket kan användas för alla Batch-pooler som skapats efter 5 juli 2017. De kan användas för Batch-pooler som skapats mellan 10 mars 2016 och 5 juli 2017, men endast om poolen skapades med en molntjänstkonfiguration. Programpaket kan inte användas för Batch-pooler som har skapats före 10 mars 2016.
>
> API: er för att skapa och hantera programpaket är en del av den [Batch Management .NET] [ api_net_mgmt] bibliotek. API: er för att installera paket med program på en beräkningsnod är en del av den [Batch .NET] [ api_net] bibliotek. Jämförbara funktioner finns i den tillgängliga Batch-API: er för andra språk. 
>
> Tillämpningsfunktion för paket som beskrivs här ersätter funktionen Batch-appar som finns i tidigare versioner av tjänsten.
> 
> 

## <a name="application-package-requirements"></a>Paketet programkrav
Om du vill använda programpaket måste du [länka ett Azure Storage-konto](#link-a-storage-account) till Batch-kontot.

## <a name="about-applications-and-application-packages"></a>Om program och programpaket
I Azure Batch en *programmet* refererar till en uppsättning med en ny version-binärfiler som kan hämtas automatiskt till compute-noder i din pool. En *programpaket* refererar till en *specifika* av dessa binärfiler och representerar en viss *version* av programmet.

![Översiktsdiagram över program och programpaket][1]

### <a name="applications"></a>Program
Ett program i batchen innehåller en eller flera program paket och anger konfigurationsalternativ för programmet. Ett program kan till exempel ange standard programpaketets version att installera på datornoder och om dess paket kan uppdateras eller tas bort.

### <a name="application-packages"></a>Programpaket
Ett programpaket är en .zip-fil som innehåller programbinärer och stödfiler som krävs för dina aktiviteter att köra programmet. Varje programpaket representerar en viss version av programmet.

Du kan ange programpaket på poolen och aktivitet. Du kan ange en eller flera av dessa paket och (valfritt) en version när du skapar en pool eller aktiviteten.

* **Poolen programpaket** har distribuerats till *varje* nod i poolen. Program distribueras när en nod ansluter till en pool och när den startas om eller avbildade.
  
    Poolen programpaket är lämplig när alla noder i en pool köra ett jobb aktiviteter. Du kan ange en eller flera programpaket när du skapar en pool och du kan lägga till eller uppdatera en befintlig adresspool paket. Om du uppdaterar en befintlig adresspool programpaket måste du starta om dess noder för att installera det nya paketet.
* **Uppgift programpaket** är bara distribueras till en beräkningsnod schemalagd att köra en aktivitet innan du kör kommandoraden för aktiviteten. Om det angivna programpaketet och den version redan finns på nod den omdistribueras inte och det befintliga paketet används.
  
    Uppgiften programpaket är användbara i miljöer med delade poolen, där olika jobben körs på en pool och poolen tas inte bort när ett jobb har slutförts. Om jobbet har färre aktiviteter än noder i poolen kan programpaket för aktiviteter minimera dataöverföringen eftersom ditt program bara distribueras till noderna som kör aktiviteterna.
  
    Andra scenarier kan dra nytta av aktiviteten programpaket finns jobb som kör ett stort program, men endast för några aktiviteter. Till exempel kan ett före bearbetning steg eller en merge-aktivitet, där programmet föregående bearbetning eller koppling är tunga, dra nytta av programpaket för aktiviteten.

> [!IMPORTANT]
> Det finns begränsningar för antalet program och programpaket i ett batchkonto och på den största storleken för paketet. Se [kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md) mer information om dessa begränsningar.
> 
> 

### <a name="benefits-of-application-packages"></a>Fördelarna med programpaket
Programpaket kan förenkla koden i Batch-lösningen och lägre omkostnader som krävs för att hantera program som körs i dina uppgifter.

Med programpaket behöver din pool startuppgift inte ange en lång lista med enskilda resursfiler ska installeras på noderna. Du behöver hantera flera versioner av dina filer i Azure Storage, eller på noderna manuellt. Och du behöver inte bry dig om att generera [SAS-URL: er](../storage/common/storage-dotnet-shared-access-signature-part-1.md) att ge åtkomst till filer i ditt lagringskonto. Batch fungerar i bakgrunden med Azure Storage för att lagra programpaket och distribuera dem för att compute-noder.

> [!NOTE] 
> Den totala storleken på en startaktivitet måste vara mindre än eller lika med 32768 tecken, inklusive resursfiler och miljövariabler. Om din startuppgift överskrider denna gräns, är med programpaket ett annat alternativ. Du kan också skapa ett komprimerade Arkiv som innehåller din resursfiler, ladda upp den som en blob till Azure Storage och packa upp den från kommandoraden för start-aktivitet. 
>
>

## <a name="upload-and-manage-applications"></a>Ladda upp och hantera program
Du kan använda den [Azure-portalen] [ portal] eller Batch Management API: er att hantera programpaket i Batch-kontot. I nästa avsnitt beskrivs först hur du länka ett lagringskonto och diskutera att lägga till program och paket och hantera dem med portalen.

### <a name="link-a-storage-account"></a>Länka ett lagringskonto
Om du vill använda programpaket måste du först koppla en [Azure Storage-konto](batch-api-basics.md#azure-storage-account) till Batch-kontot. Om du ännu inte har konfigurerat ett lagringskonto på Azure-portalen visar en varning som första gången du klickar på **program** i Batch-kontot.



!['Inget lagringskonto har konfigurerats, varning i Azure-portalen][9]

Batch-tjänsten använder det associera lagringskontot för att lagra dina programpaket. När du har länkat två konton kan Batch automatiskt distribuera paket som lagras i länkade Storage-konto till compute-noder. Länka ett lagringskonto till Batch-kontot, klicka på **lagringskonto** på den **varning** fönstret och klicka sedan på **Lagringskonto** igen.

![Välj lagring konto-bladet i Azure-portalen][10]

Vi rekommenderar att du skapar ett lagringskonto *specifikt* för användning med Batch-kontot och markera den här. När du har skapat ett lagringskonto kan du sedan länka den till Batch-kontot med hjälp av den **Lagringskonto** fönster.

> [!NOTE] 
> För närvarande du kan inte använda programpaket med ett Azure Storage-konto som har konfigurerats med [regler i brandväggen](../storage/common/storage-network-security.md).
> 

Batch-tjänsten använder Azure Storage för att lagra dina programpaket som blockblobar. Du är [debiteras som vanligt] [ storage_pricing] för block-blob-data. Bör du tänka på antalet och storleken på din programpaket och tar regelbundet bort inaktuella paket för att minimera kostnaderna.
> 
> 

### <a name="view-current-applications"></a>Visa aktuella program
Om du vill visa programmen i Batch-kontot, klickar du på den **program** menyalternativet i den vänstra menyn när Visa din **Batch-kontot**.

![Program sida vid sida][2]

Om du markerar alternativet öppnas den **program** fönster:

![Lista över program][3]

Det här fönstret visas ID för varje program i ditt konto och följande egenskaper:

* **Paket**: antal versioner som är associerade med det här programmet.
* **Standardversionen**: programversionen installeras om du inte anger en version när du anger programmet för en pool. Den här inställningen är valfri.
* **Tillåt uppdateringar**: det värde som anger om paketet uppdateringar, borttagningar och tillägg är tillåtna. Om detta anges till **nr**, paketet uppdateringar och borttagningar är inaktiverat för programmet. Endast nya programpaketversioner kan läggas till. Standardinställningen är **Ja**.

### <a name="view-application-details"></a>Visa programinformation
Om du vill visa detaljerad information för ett program, Välj ett program i den **program** fönster.

![Programinformation][4]

Du kan konfigurera följande inställningar för ditt program i programinformation.

* **Tillåt uppdateringar**: Ange om dess programpaket kan uppdateras eller tas bort. Se ”uppdatera eller ta bort ett programpaket” senare i den här artikeln.
* **Standardversionen**: Ange ett programpaket som ska användas som standard ska distribuerar compute-noder.
* **Visningsnamn**: Ange ett eget namn som din Batch-lösning kan använda när den visas information om programmet, till exempel i Användargränssnittet för en tjänst som du tillhandahålla till dina kunder via Batch.

### <a name="add-a-new-application"></a>Lägg till ett nytt program
Skapa ett nytt program, lägga till ett programpaket och ange ett nytt, unikt-ID. Det första programpaketet som du lägger till med den nya program-ID skapas också det nya programmet.

Klicka på **Program** > **Lägg till**.

![Nya program bladet i Azure-portalen][5]

Den **nytt program** fönstret innehåller följande fält för att ange inställningarna för ditt nya program och programpaket.

**Program-id**

Det här fältet anger ID för det nya programmet som standard valideringsregler för Azure Batch-ID. Regler för att tillhandahålla ett program-ID är följande:

* För Windows-noder, kan ID: T innehålla vilken kombination av alfanumeriska tecken, bindestreck och understreck. På Linux-noder tillåts endast alfanumeriska tecken och understreck.
* Får inte innehålla fler än 64 tecken.
* Måste vara unika inom Batch-kontot.
* Är bevara och skiftlägesoberoende.

**Version**

Det här fältet anger vilken version av programpaketet som du överför. Version strängar regleras följande valideringsregler:

* Versionsträngen kan innehålla en kombination av alfanumeriska tecken, bindestreck, understreck och punkter på Windows-noder. För Linux-noder, kan Versionsträngen innehålla endast alfanumeriska tecken och understreck.
* Får inte innehålla fler än 64 tecken.
* Måste vara unikt i programmet.
* Är bevara och skiftlägesoberoende.

**Programpaket**

Det här fältet anger ZIP-fil som innehåller programbinärer och stödfiler som krävs för att köra programmet. Klicka på den **Välj en fil** rutan eller mappikonen för att bläddra till och markera en .zip-fil som innehåller filer som ditt program.

När du har valt en fil, klickar du på **OK** att börja överföra till Azure Storage. När överföringen är klar, visas ett meddelande i portalen. Den här åtgärden kan ta lite tid beroende på storleken på den fil som du överför och hastighet för nätverksanslutningen.

> [!WARNING]
> Stäng inte den **nytt program** fönstret innan överföringen är klar. Detta stoppar överföringen.
> 
> 

### <a name="add-a-new-application-package"></a>Lägg till ett nytt programpaket
Om du vill lägga till ett program Paketversion för ett befintligt program, Välj ett program i den **program** windows och klickar på **paket** > **Lägg till**.

![Lägg till program paketet bladet i Azure-portalen][8]

Som du kan se fälten stämmer överens med den **nytt program** fönster, men **program-id** kryssrutan är inaktiverad. Som du gjorde för det nya programmet ange den **Version** för din nya paket, bläddra till din **programpaket** ZIP-filen och klicka sedan på **OK** att ladda upp paketet.

### <a name="update-or-delete-an-application-package"></a>Uppdatera eller ta bort ett programpaket
Om du vill uppdatera eller ta bort ett befintligt programpaket, öppna information för programmet, klickar du på **paket**, klickar du på den **tre punkter** i raden i det programpaket som du vill ändra och välj den åtgärd som du vill utföra.

![Uppdatera eller ta bort paketet i Azure-portalen][7]

**Uppdatering**

När du klickar på **uppdatering**, **uppdateringspaketet** windows visas. Det här fönstret liknar den **nytt programpaket** fönster, men endast fältet paketet markeringen är aktiverad, så att du kan ange en ny ZIP-fil och överför.

![Uppdatera paketet bladet i Azure-portalen][11]

**Ta bort**

När du klickar på **ta bort**, du uppmanas att bekräfta borttagningen av paketversionen och Batch tar bort paketet från Azure Storage. Om du tar bort standardversionen av ett program i **standardversionen** inställningen tas bort för programmet.

![Ta bort programmet ][12]

## <a name="install-applications-on-compute-nodes"></a>Installera program på datornoderna
Nu när du har lärt dig hur du hanterar programpaket med Azure-portalen, diskuterar vi hur du distribuerar dem datornoder och köra dem med Batch-uppgifter.

### <a name="install-pool-application-packages"></a>Installera poolen programpaket
Ange en eller flera programpaket för att installera ett programpaket på alla beräkningsnoder i poolen, *referenser* för poolen. Programpaket som du anger för en pool är installerade på varje beräkningsnod när noden ansluts till poolen och när noden startas om eller avbildas på nytt.

Ange en eller flera i Batch .NET [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] när du skapar en ny pool eller för en befintlig adresspool. Den [ApplicationPackageReference] [ net_pkgref] klassen anger en program-ID och version för att installera på en pool compute-noder.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

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
> Om en distribution av paketet misslyckas av någon anledning, Batch-tjänsten markerar noden [oanvändbar][net_nodestate], och inga aktiviteter som är schemalagda för körning på noden. I det här fallet bör du **starta om** noden för att återuppta paketdistributionen. Starta om noden kan också schemalägga aktivitet igen på noden.
> 
> 

### <a name="install-task-application-packages"></a>Installera aktivitet programpaket
Precis som en pool kan du ange programpaket *referenser* för en aktivitet. När en aktivitet är schemalagd att köras på en nod, paketet hämtas och extraheras precis innan aktivitetens kommandoraden körs. Om ett angivet paket och version är redan installerad på noden, paketet laddas inte ned, och det befintliga paketet används.

Om du vill installera ett programpaket för aktiviteten konfigurera aktivitetens [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] egenskapen:

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

## <a name="execute-the-installed-applications"></a>Köra installerade program
De paket som du har angett för en pool eller aktivitet hämtat och extraherat till en namngiven katalog i den `AZ_BATCH_ROOT_DIR` för noden. Batch skapar även en miljövariabel som innehåller sökvägen till katalogen. Din aktivitet kommandorader använda den här miljövariabeln referera till programmet på noden. 

På Windows-noder är variabeln i följande format:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Formatet är något annorlunda på Linux-noder. Punkter (.), bindestreck (-) och nummertecken (#) förenklas till understreck i miljövariabeln. Observera också att i fallet med det program-ID bevaras. Exempel:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` och `version` är värden som motsvarar den program- och versionen som du har angett för distribution. Om du har angett att version 2.7 av programmet till exempel *blandaren* ska installeras på Windows-noder din aktivitet kommandorader använder den här miljövariabeln komma åt sina filer:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Ange miljövariabeln på Linux-noder i det här formatet. Förenkla punkter (.), bindestreck (-) och nummertecken (#) till understreck och bevara skiftläget för det program-ID:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

När du överför ett programpaket kan du ange en standardversion att distribuera till dina compute-noder. Om du har angett en standardversion för ett program, kan du utelämna suffixet version när du refererar till programmet. Du kan ange standard programversionen i Azure-portalen i den **program** fönster som visas i [ladda upp och hantera program](#upload-and-manage-applications).

Till exempel om du anger ”2.7” som standardversion för programmet *blandaren*, och dina aktiviteter referera miljövariabeln följande och sedan Windows-noder som ska köra version 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Följande kodavsnitt visar ett exempel uppgiften-kommandoraden som startar standardversionen av den *blandaren* program:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Se [miljöinställningar för aktiviteter](batch-api-basics.md#environment-settings-for-tasks) i den [Batch funktionsöversikt](batch-api-basics.md) mer information om beräkning nod miljöinställningar.
> 
> 

## <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool
Om en befintlig pool har redan konfigurerats med ett programpaket, kan du ange ett nytt paket för poolen. Om du anger en ny paketet referens för en pool följande gäller:

* Batch-tjänsten installeras nyss angivna paketet på alla nya noder som ansluta till poolen och alla befintliga nod som startats om eller avbildade.
* Compute-noder som redan finns i poolen när du uppdaterar paketet refererar till inte installerar automatiskt nya programpaketet. Dessa compute-noder måste startas om eller avbildade för att ta emot det nya paketet.
* När ett nytt paket distribueras återspegla skapade miljövariablerna de nya programmet paketet refererar till.

I det här exemplet har befintliga poolen version 2.7 av den *blandaren* program som har konfigurerats som en av dess [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. Ange en ny för att uppdatera poolens noder med version 2.76b [ApplicationPackageReference] [ net_pkgref] med den nya versionen och spara ändringen.

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

Nu när den nya versionen har konfigurerats, Batch-tjänsten installeras versionen 2.76b till någon *nya* nod som ansluter till poolen. Installera 2.76b på de noder som är *redan* i poolen, starta om eller återavbilda dem. Observera att omstartad noder behålla filerna från den tidigare paketet distributioner.

## <a name="list-the-applications-in-a-batch-account"></a>Visa en lista över program i ett Batch-konto
Du kan visa program och deras paket i ett Batch-konto med hjälp av den [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] metod.

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

## <a name="wrap-up"></a>Sammanfattning
Med programpaket kan hjälpa du kunderna Välj program för sitt arbete och ange den exakta versionen ska användas vid bearbetning av jobb med din tjänst med Batch-aktiverade. Du kan också tillhandahålla möjligheten för kunderna att ladda upp och spåra sina egna program i din tjänst.

## <a name="next-steps"></a>Nästa steg
* Den [Batch REST API] [ api_rest] innehåller också stöd för att arbeta med programpaket. Se exempelvis den [applicationPackageReferences] [ rest_add_pool_with_packages] element i [lägga till en pool med ett konto] [ rest_add_pool] information om hur du anger paket som ska installeras med hjälp av REST API. Se [program] [ rest_applications] mer information om hur du hämtar information om programmet med hjälp av Batch REST API.
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

[1]: ./media/batch-application-packages/app_pkg_01.png "Översiktsdiagram för programmets paket"
[2]: ./media/batch-application-packages/app_pkg_02.png "Program-panelen i Azure-portalen"
[3]: ./media/batch-application-packages/app_pkg_03.png "Program-bladet i Azure-portalen"
[4]: ./media/batch-application-packages/app_pkg_04.png "Programmet informationsbladet i Azure-portalen"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nya program bladet i Azure-portalen"
[7]: ./media/batch-application-packages/app_pkg_07.png "Uppdatera eller ta bort paket listrutan i Azure-portalen"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nya bladet med paketet i Azure-portalen"
[9]: ./media/batch-application-packages/app_pkg_09.png "Ingen varning har länkade Storage-konto"
[10]: ./media/batch-application-packages/app_pkg_10.png "Välj lagring konto-bladet i Azure-portalen"
[11]: ./media/batch-application-packages/app_pkg_11.png "Uppdatera paketet bladet i Azure-portalen"
[12]: ./media/batch-application-packages/app_pkg_12.png "Ta bort paketet bekräftelsedialogruta i Azure-portalen"
