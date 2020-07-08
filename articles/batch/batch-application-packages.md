---
title: Distribuera programpaket till Compute-noder
description: Använd funktionen programpaket i Azure Batch för att enkelt hantera flera program och versioner för installation på batch Compute-noder.
ms.topic: how-to
ms.date: 04/26/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cebb7bf001d16e1024ed466268758f0b1bc92c6c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955038"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Distribuera program till Compute-noder med batch-programpaket

Programpaket-funktionen i Azure Batch ger enkel hantering av uppgifts program och deras distribution till datornoderna i poolen. Med programpaket kan du ladda upp och hantera flera versioner av programmen som dina aktiviteter kör, inklusive deras stödfiler. Du kan sedan automatiskt distribuera ett eller flera av dessa program till Compute-noderna i poolen.

I den här artikeln får du lära dig hur du överför och hanterar programpaket i Azure Portal. Du lär dig sedan att installera dem i en Pools datornoder med [batch .net][api_net] -biblioteket.

> [!NOTE]
> Programpaket kan användas för alla Batch-pooler som skapats efter 5 juli 2017. De kan användas för Batch-pooler som skapats mellan 10 mars 2016 och 5 juli 2017, men endast om poolen skapades med en molntjänstkonfiguration. Programpaket kan inte användas för Batch-pooler som har skapats före 10 mars 2016.
>
> API: erna för att skapa och hantera programpaket ingår i [batch Management .net][api_net_mgmt] -biblioteket. API: erna för att installera programpaket på en Compute-nod är en del av [batch .net][api_net] -biblioteket. Jämförbara funktioner finns i de tillgängliga batch-API: erna för andra språk. 
>
> Funktionen programpaket som beskrivs här ersätter funktionen batch apps som finns i tidigare versioner av tjänsten.

## <a name="application-package-requirements"></a>Krav för applikations paket
Om du vill använda programpaket måste du [Länka ett Azure Storage-konto](#link-a-storage-account) till ditt batch-konto.

## <a name="about-applications-and-application-packages"></a>Om program och programpaket
I Azure Batch refererar ett *program* till en uppsättning versioner av binärfiler som kan hämtas automatiskt till Compute-noderna i poolen. Ett *programpaket* refererar till en *specifik uppsättning* av dessa binärfiler och representerar en angiven *version* av programmet.

![Diagram över program och programpaket på hög nivå][1]

### <a name="applications"></a>Program
Ett program i batch innehåller ett eller flera programpaket och anger konfigurations alternativ för programmet. Ett program kan till exempel ange standard versionen för programpaket som ska installeras på Compute-noder och om dess paket kan uppdateras eller tas bort.

### <a name="application-packages"></a>Programpaket
Ett programpaket är en. zip-fil som innehåller programmets binärfiler och stödfiler som krävs för att dina aktiviteter ska köra programmet. Varje programpaket representerar en specifik version av programmet.

Du kan ange programpaket på pool-och aktivitets nivåer. Du kan ange ett eller flera av dessa paket och (valfritt) en version när du skapar en pool eller uppgift.

* **Programpaket för pooler** distribueras till *varje* nod i poolen. Program distribueras när en nod ansluter till en pool och när den startas om eller avbildningas om.
  
    Programpaket för poolen är lämpliga när alla noder i en pool kör jobbets aktiviteter. Du kan ange ett eller flera programpaket när du skapar en pool och du kan lägga till eller uppdatera en befintlig Pools paket. Om du uppdaterar en befintlig Pools programpaket måste du starta om noderna för att installera det nya paketet.
* **Programpaket för uppgifter** distribueras endast till en Compute-nod som har schemalagts för att köra en uppgift, precis innan aktivitetens kommando rad körs. Om det angivna programpaketet och versionen redan finns på noden omdistribueras det inte och det befintliga paketet används.
  
    Programpaket för uppgifter är användbara i miljöer med delade pooler, där olika jobb körs på en pool, och poolen tas inte bort när ett jobb har slutförts. Om jobbet har färre aktiviteter än noder i poolen kan programpaket för aktiviteter minimera dataöverföringen eftersom ditt program bara distribueras till noderna som kör aktiviteterna.
  
    Andra scenarier som kan dra nytta av uppgifts programmets paket är jobb som kör ett stort program, men endast för några få uppgifter. Till exempel ett för bearbetnings steg eller en sammanfognings uppgift, där för bearbetnings-eller kopplings programmet är Heavyweight, kan dra nytta av att använda uppgifts programpaket.

> [!IMPORTANT]
> Det finns begränsningar för antalet program och programpaket i ett batch-konto och den maximala storleken för programpaket. Mer information om dessa begränsningar finns i [kvoter och begränsningar för Azure Batch tjänsten](batch-quota-limit.md) .
> 
> 

### <a name="benefits-of-application-packages"></a>Fördelar med programpaket
Programpaket kan förenkla koden i din batch-lösning och sänka den omkostnader som krävs för att hantera de program som dina aktiviteter kör.

Med programpaket behöver inte poolens start uppgift ange en lång lista med enskilda resursfiler som ska installeras på noderna. Du behöver inte manuellt hantera flera versioner av dina programfiler i Azure Storage eller på noderna. Och du behöver inte oroa dig för att skapa [SAS-URL: er](../storage/common/storage-dotnet-shared-access-signature-part-1.md) för att ge åtkomst till filerna i ditt lagrings konto. Batch fungerar i bakgrunden med Azure Storage för att lagra programpaket och distribuera dem till Compute-noder.

> [!NOTE] 
> Den totala storleken på en startaktivitet måste vara mindre än eller lika med 32768 tecken, inklusive resursfiler och miljövariabler. Om start aktiviteten överskrider den här gränsen är det ett annat alternativ att använda programpaket. Du kan också skapa ett zippat arkiv som innehåller dina resursfiler, överföra det som en blob till Azure Storage och sedan zippa upp det från kommando raden i Start aktiviteten. 
>
>

## <a name="upload-and-manage-applications"></a>Ladda upp och hantera program
Du kan använda [Azure Portal][portal] eller API: er för batch Management för att hantera programpaketen i batch-kontot. I kommande avsnitt visar vi först hur man länkar ett lagrings konto och diskuterar sedan att lägga till program och paket och hantera dem med portalen.

### <a name="link-a-storage-account"></a>Länka ett lagrings konto
Om du vill använda programpaket måste du först länka ett [Azure Storage-konto](accounts.md#azure-storage-accounts) till batch-kontot. Om du ännu inte har konfigurerat ett lagrings konto visas en varning i Azure Portal första gången du klickar på **program** i batch-kontot.



![Varningen "inget lagrings konto har kon figurer ATS" i Azure Portal][9]

Batch-tjänsten använder det associerade lagrings kontot för att lagra dina programpaket. När du har länkat de två kontona kan batch automatiskt distribuera paketen som lagras i det länkade lagrings kontot till dina Compute-noder. Om du vill länka ett lagrings konto till ditt batch-konto klickar du på **lagrings konto** i **varnings** fönstret och klickar sedan på **lagrings konto** igen.

![Välj bladet lagrings konto i Azure Portal][10]

Vi rekommenderar att du skapar ett lagrings konto som är *specifikt* för användning med ditt batch-konto och väljer det här. När du har skapat ett lagrings konto kan du sedan länka det till ditt batch-konto med hjälp av fönstret **lagrings konto** .

> [!IMPORTANT] 
> - För närvarande kan du inte använda programpaket med ett Azure Storage-konto som är konfigurerat med [brand Väggs regler](../storage/common/storage-network-security.md).
> - Ett Azure Storage konto med **hierarkiskt namn område** inställt på **aktive rad** kan inte användas för programpaket.

Batch-tjänsten använder Azure Storage för att lagra dina programpaket som block-blobbar. Du [debiteras som normal][storage_pricing] för block-BLOB-data och storleken på varje paket får inte överskrida den största blockets BLOB-storlek. Mer information finns i [Azure Storage skalbarhets-och prestanda mål för lagrings konton](../storage/blobs/scalability-targets.md). Se till att du tar hänsyn till storlek och antal för dina programpaket och ta regelbundet bort inaktuella paket för att minimera kostnaderna.

### <a name="view-current-applications"></a>Visa aktuella program
Om du vill visa programmen i batch-kontot klickar du på meny alternativet **program** på den vänstra menyn när du visar ditt **Batch-konto**.

![Panelen program][2]

När du väljer det här meny alternativet öppnas fönstret **program** :

![Lista program][3]

I det här fönstret visas ID: t för varje program i ditt konto och följande egenskaper:

* **Paket**: antalet versioner som är associerade med det här programmet.
* **Standard version**: program versionen som installeras om du inte anger en version när du anger programmet för en pool. Den här inställningen är valfri.
* **Tillåt uppdateringar**: det värde som anger om paket uppdateringar, borttagningar och tillägg är tillåtna. Om detta är inställt på **Nej**, inaktive ras paket uppdateringar och borttagningar för programmet. Det går bara att lägga till nya programpaket versioner. Standardvärdet är **Ja**.

Om du vill se fil strukturen för programpaketet på din Compute-nod går du till ditt batch-konto i portalen. Gå till **pooler**på ditt batch-konto. Välj den pool som innehåller de Compute-noder som du är intresse rad av.

![Noder i poolen][13]

När du har valt din pool navigerar du till Compute-noden som programpaketet är installerat på. Därifrån finns information om programpaketet i mappen **program** . Ytterligare mappar på Compute-noden innehåller andra filer, t. ex. start aktiviteter, utdatafiler, fel utdata osv.

![Filer på noden][14]

### <a name="view-application-details"></a>Visa programinformation
Om du vill se information om ett program väljer du programmet i fönstret **program** .

![Programinformation][4]

I programinformationen kan du konfigurera följande inställningar för ditt program.

* **Tillåt uppdateringar**: Ange om program paketen kan uppdateras eller tas bort. Se "uppdatera eller ta bort ett programpaket" senare i den här artikeln.
* **Standard version**: Ange ett standard-programpaket som ska distribueras till Compute-noder.
* **Visnings namn**: Ange ett eget namn som din batch-lösning kan använda när den visar information om programmet, till exempel i användar gränssnittet för en tjänst som du tillhandahåller till dina kunder via batch.

### <a name="add-a-new-application"></a>Lägg till ett nytt program
Om du vill skapa ett nytt program lägger du till ett programpaket och anger ett nytt, unikt program-ID. Det första applikations paketet som du lägger till med det nya program-ID: t skapar också det nya programmet.

Klicka på **program**  >  **Lägg till**.

![Bladet ny program i Azure Portal][5]

I fönstret **ny app** finns följande fält för att ange inställningar för det nya programmet och programpaketet.

**Program-ID**

Det här fältet innehåller ID: t för ditt nya program, vilket omfattas av validerings reglerna för standard Azure Batch-ID. Reglerna för att tillhandahålla ett program-ID är följande:

* På Windows-noder kan ID: t innehålla en kombination av alfanumeriska tecken, bindestreck och under streck. På Linux-noder är endast alfanumeriska tecken och under streck tillåtna.
* Får innehålla högst 64 tecken.
* Måste vara unikt inom batch-kontot.
* Är Skift läges känsligt och Skift läges okänsligt.

**Version**

Det här fältet innehåller versionen för det programpaket som du överför. Versions strängar är underkastade följande validerings regler:

* På Windows-noder kan versions strängen innehålla valfri kombination av alfanumeriska tecken, bindestreck, under streck och punkter. På Linux-noder får versions strängen endast innehålla alfanumeriska tecken och under streck.
* Får innehålla högst 64 tecken.
* Måste vara unikt inom programmet.
* Är Skift läges bevarad och Skift läges okänslig.

**Programpaket**

I det här fältet anges. zip-filen som innehåller binärfilerna för programmet och de stödfiler som krävs för att köra programmet. Klicka på rutan **Välj en fil** eller mappikonen för att bläddra till och välj en. zip-fil som innehåller programmets filer.

När du har valt en fil, klickar du på **OK** för att starta överföringen till Azure Storage. När överföringen är klar visas ett meddelande i portalen. Den här åtgärden kan ta lite tid beroende på storleken på den fil som du överför och nätverks anslutningens hastighet.

> [!WARNING]
> Stäng inte det **nya** programfönstret innan överförings åtgärden är klar. Om du gör det stoppas överförings processen.
> 
> 

### <a name="add-a-new-application-package"></a>Lägg till ett nytt programpaket
Om du vill lägga till en programpaket version för ett befintligt program väljer du ett program i fönstren **program** och klickar på **paket**  >  **Lägg till**.

![Bladet Lägg till programpaket i Azure Portal][8]

Som du kan se matchar fälten de i fönstret **nytt program** , men rutan **program-ID** är inaktive rad. När du har gjort det nya programmet anger du **versionen** för ditt nya paket, bläddrar till din **programpaket** . zip-fil och klickar sedan på **OK** för att ladda upp paketet.

### <a name="update-or-delete-an-application-package"></a>Uppdatera eller ta bort ett programpaket
Om du vill uppdatera eller ta bort ett befintligt programpaket öppnar du informationen för programmet, klickar på **paket**, klickar på **ellipsen** i raden i det programpaket som du vill ändra och väljer den åtgärd som du vill utföra.

![Uppdatera eller ta bort paket i Azure Portal][7]

**Uppdatera**

När du klickar på **Uppdatera**visas **uppdaterings paketets** fönster. Det här fönstret liknar det **nya fönstret programpaket** , men endast fältet för val av paket är aktiverat, så att du kan ange en ny zip-fil att ladda upp.

![Bladet uppdatera paket i Azure Portal][11]

**Ta bort**

När du klickar på **ta bort**uppmanas du att bekräfta borttagningen av paket versionen och batch tar bort paketet från Azure Storage. Om du tar bort standard versionen av ett program tas **standard versions** inställningen bort för programmet.

![Ta bort program][12]

## <a name="install-applications-on-compute-nodes"></a>Installera program på Compute-noder
Nu när du har lärt dig hur du hanterar programpaket med Azure Portal kan vi diskutera hur du distribuerar dem för att beräkna noder och köra dem med batch-uppgifter.

### <a name="install-pool-application-packages"></a>Installera poolens programpaket
Om du vill installera ett programpaket på alla Compute-noder i en pool anger du ett eller flera Programpakets *referenser* för poolen. De programpaket som du anger för en pool installeras på varje Compute-nod när noden ansluter till poolen, och när noden startas om eller avbildningas om.

I batch .NET anger du en eller flera [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] när du skapar en ny pool eller för en befintlig pool. [ApplicationPackageReference][net_pkgref] -klassen anger ett program-ID och en version som ska installeras i en Pools datornoder.

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
> Om distributionen av ett programpaket Miss lyckas, markerar batch-tjänsten noden som [oanvändbar][net_nodestate]och inga aktiviteter är schemalagda för körning på den noden. I så fall bör du **starta om** noden för att initiera paket distributionen igen. När du startar om noden aktive ras även schemaläggning igen på noden.
> 
> 

### <a name="install-task-application-packages"></a>Installera programpaket för uppgift
På samma sätt som en pool anger du programpaket- *referenser* för en aktivitet. När en aktivitet har schemalagts för körning på en nod laddas paketet ned och extraheras precis innan aktivitetens kommando rad körs. Om ett angivet paket och version redan är installerat på noden laddas inte paketet ned och det befintliga paketet används.

Konfigurera aktivitetens [CloudTask][net_cloudtask]om du vill installera ett uppgifts program paket. [ApplicationPackageReferences][net_cloudtask_pkgref] -egenskap:

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
De paket som du har angett för en pool eller aktivitet laddas ned och extraheras till en namngiven katalog i `AZ_BATCH_ROOT_DIR` noden. Batch skapar också en miljö variabel som innehåller sökvägen till den namngivna katalogen. Dina aktivitets kommando rader använder den här miljövariabeln för att referera till programmet på noden. 

På Windows-noder har variabeln följande format:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

På Linux-noder är formatet något annorlunda. Punkter (.), bindestreck (-) och nummer tecken (#) förenklas till under streck i miljö variabeln. Observera också att fallet med program-ID: t bevaras. Ett exempel:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`och `version` är värden som motsvarar den program-och paket version som du har angett för distribution. Om du till exempel har angett att version 2,7 av program *blandning* ska installeras på Windows-noder, använder dina aktivitets kommando rader denna miljö variabel för att komma åt dess filer:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

På Linux-noder anger du miljövariabeln i det här formatet. Förenkla perioderna (.), bindestreck (-) och nummer tecken (#) till under streck och behåll fallet med program-ID: t:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

När du laddar upp ett programpaket kan du ange en standard version som ska distribueras till dina Compute-noder. Om du har angett en standard version för ett program kan du utelämna versionens suffix när du refererar till programmet. Du kan ange standard program versionen i Azure Portal i fönstret **program** , som visas i [överför och hantera program](#upload-and-manage-applications).

Om du till exempel ställer in "2,7" som standard version för program *blandning*, och dina uppgifter refererar till följande miljö variabel, körs Windows-noderna version 2,7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Följande kodfragment visar ett exempel på en uppgift kommando rad som startar standard versionen av *över gångs* programmet:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Mer information om inställningar för Compute Node-miljön finns i [miljö inställningar för aktiviteter](jobs-and-tasks.md#environment-settings-for-tasks). 

## <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool
Om en befintlig pool redan har kon figurer ATS med ett programpaket kan du ange ett nytt paket för poolen. Om du anger en ny paket referens för en pool gäller följande:

* Batch-tjänsten installerar det nyligen angivna paketet på alla nya noder som ansluter till poolen och på alla befintliga noder som startas om eller avbildningar.
* Compute-noder som redan finns i poolen när du uppdaterar paket referenserna installerar inte automatiskt det nya programpaketet. De här Compute-noderna måste startas om eller avbildningas om för att det nya paketet ska kunna tas emot.
* När ett nytt paket distribueras återspeglar de skapade miljövariablerna de nya programpaket referenserna.

I det här exemplet har den befintliga poolen version 2,7 av *över gångs* programmet som kon figurer ATS som en av dess [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. Om du vill uppdatera poolens noder med version 2.76 b anger du en ny [ApplicationPackageReference][net_pkgref] med den nya versionen och genomför ändringen.

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

Nu när den nya versionen har kon figurer ATS installerar batch-tjänsten version 2.76 b till en *ny* nod som ansluter till poolen. Om du vill installera 2.76 b på noderna som *redan* finns i poolen startar du om eller återavbildningar dem. Observera att omstartade noder behåller filerna från tidigare paket distributioner.

## <a name="list-the-applications-in-a-batch-account"></a>Visa en lista över programmen i ett batch-konto
Du kan visa en lista över program och deras paket i ett batch-konto med hjälp av [ApplicationOperations][net_appops]. [ListApplicationSummaries][net_appops_listappsummaries] -metod.

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

## <a name="wrap-up"></a>Bryt upp
Med programpaket kan du hjälpa dina kunder att välja program för sina jobb och ange den exakta versionen som ska användas för bearbetning av jobb med din batch-aktiverade tjänst. Du kan också ge dina kunder möjlighet att ladda upp och spåra sina egna program i din tjänst.

## <a name="next-steps"></a>Nästa steg
* [Batch-REST API][api_rest] tillhandahåller också stöd för att arbeta med programpaket. Se till exempel elementet [applicationPackageReferences][rest_add_pool_with_packages] i [lägga till en pool till ett konto][rest_add_pool] för information om hur du anger paket som ska installeras med hjälp av REST API. Se [program][rest_applications] för information om hur du hämtar programinformation med Batch-REST API.
* Lär dig hur du program mässigt [hanterar Azure Batch konton och kvoter med batch Management .net](batch-management-dotnet.md). Med [batch Management .net][api_net_mgmt] -biblioteket kan du aktivera funktioner för att skapa och ta bort konton för ditt batch-program eller-tjänst.

[api_net]: /dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: /rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: /dotnet/api/microsoft.azure.batch.applicationoperations
[net_appops_listappsummaries]: /dotnet/api/microsoft.azure.batch.applicationoperations
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_cloudpool_pkgref]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_cloudtask_pkgref]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_nodestate]: /dotnet/api/microsoft.azure.batch.computenode
[net_pkgref]: /dotnet/api/microsoft.azure.batch.applicationpackagereference
[portal]: https://portal.azure.com
[rest_applications]: /rest/api/batchservice/application
[rest_add_pool]: /rest/api/batchservice/pool/add
[rest_add_pool_with_packages]: /rest/api/batchservice/pool/add#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagram över programpaket på hög nivå"
[2]: ./media/batch-application-packages/app_pkg_02.png "Panelen program i Azure Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Bladet program i Azure Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Bladet program detaljer i Azure Portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Bladet ny program i Azure Portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "List rutan uppdatera eller ta bort paket i Azure Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Bladet nytt programpaket i Azure Portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Ingen avisering om länkat lagrings konto"
[10]: ./media/batch-application-packages/app_pkg_10.png "Välj bladet lagrings konto i Azure Portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Bladet uppdatera paket i Azure Portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Bekräftelse dialog rutan ta bort paket i Azure Portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Beräkna Node-information i Azure Portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Filer på Compute-noden som visas i Azure Portal"
