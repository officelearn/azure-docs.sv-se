---
title: 'Självstudier: Skapa ett program med hög tillgänglighet med Blob-lagring – Azure Storage'
description: Använd RA-GRS för att ge programdata hög tillgänglighet
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 0ccd57e1614f23d775df2fe8e963d2cc7f9a4358
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360750"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Självstudier: Skapa ett program med hög tillgänglighet med Blob-lagring

Den här självstudien ingår i en serie. I den lär du dig att ge programdata hög tillgänglighet i Azure.

När du har slutfört den här självstudien har du ett konsolprogram som laddar upp och hämtar en blob från ett [Read-Access Geo Redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage)-lagringskonto (RA-GRS).

Med RA-GRS replikeras transaktioner från en primär region till en sekundär region. Replikeringsprocessen garanterar att data i den sekundära regionen blir konsekventa. Programmet använder mönstret [Circuit Breaker](/azure/architecture/patterns/circuit-breaker) (Kretsbrytare) för att avgöra vilken slutpunkt som ska anslutas till. Det växlar automatiskt mellan slutpunkter när fel och återställningar simuleras.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Ange anslutningssträngen
> * Kör konsolprogrammet

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

* Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med följande arbets belastningar:
  - **Azure Development**

  ![Azure-utveckling (under Web & Cloud (Webb och moln))](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* Installera [Python](https://www.python.org/downloads/)
* Ladda ned och installera [Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python)

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

* Installera och konfigurera [Maven](https://maven.apache.org/download.cgi) så att det fungerar från kommandoraden
* Installera och konfigurera [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

* Installera [Node. js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett lagringskonto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure-lagringdataobjekt.

Följ dessa steg om du vill skapa ett RA-GRS-lagringskonto:

1. Välj knappen **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Välj **Lagring** på sidan **Ny**.
3. Välj **Lagringskonto – blob, fil, tabell, kö** i **Aktuellt**.
4. Fyll i formuläret för lagringskontot med följande information (se bilden nedan) och välj **Skapa**:

   | Inställning       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Namn** | mystorageaccount | Ett unikt värde för lagringskontot |
   | **Distributionsmodell** | Resource Manager  | Resource Manager innehåller de senaste funktionerna.|
   | **Typ av konto** | StorageV2 | Mer information om kontotyper finns i [typer av lagringskonton](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Prestanda** | Standard | Standard är tillräckligt för exempelscenariot. |
   | **Replikering**| Read-Access Geo-Redundant Storage (RA-GRS) | Detta krävs för att exemplet ska fungera. |
   |**Prenumeration** | din prenumeration |Mer information om dina prenumerationer finns i [Prenumerationer](https://account.azure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Location** | East US | Välj en plats. |

![skapa lagringskonto](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Hämta exemplet

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

[Ladda ned exempelprojektet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) och extrahera (packa upp) filen storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett konsolprogram.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[Ladda ned exempelprojektet](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) och extrahera (packa upp) filen storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett grundläggande Python-program.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

[Hämta exempelprojektet](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs) och extrahera filen storage-java-ragrs.zip. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett grundläggande Java-program.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

[Hämta exempelprojektet](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) och packa upp filen. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett Basic Node. js-program.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Konfigurera exemplet

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Du kan lagra den här anslutningssträngen inom en miljövariabel på den lokala dator där programmet körs. Följ något av exemplen nedan beroende på operativsystemet för att skapa miljövariabeln.

Navigera till ditt lagringskonto i Azure Portal. Välj **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Kopiera **anslutningssträngen** från den primära eller sekundära nyckeln. Kör något av följande kommandon baserade på operativ systemet och Ersätt \<yourconnectionstring\> med den faktiska anslutnings strängen. Kommandot sparar en miljövariabel till den lokala datorn. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** eller gränssnittet på nytt.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I programmet måste du ange autentiseringsuppgifterna för ditt lagrings konto. Du kan lagra informationen i miljövariabler på den lokala datorn som kör programmet. Följ något av exemplen nedan, beroende på vilket operativ system som används för att skapa miljövariablerna.

Navigera till ditt lagringskonto i Azure Portal. Välj **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Klistra in **lagrings kontots namn** och **nyckel** värden i följande \<kommandon och ersätt plats hållarna\> youraccountname\> och \<youraccountkey. Det här kommandot sparar miljövariablerna på den lokala datorn. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** eller gränssnittet på nytt.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

Det här exemplet kräver att du lagrar namnet och nyckeln för ditt lagringskonto på ett säkert sätt. Lagra dem i miljövariabler som är lokala på den dator som kommer att köra exemplet. Använd antingen Linux- eller Windows-exemplet, beroende på operativsystemet, för att skapa miljövariablerna. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommando tolken** eller gränssnittet som du använder igen.

### <a name="linux-example"></a>Linux-exemplet

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Windows-exempel

```powershell
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

Om du vill köra det här exemplet måste du lägga till dina autentiseringsuppgifter för `.env.example` ditt lagrings konto i filen `.env`och sedan byta namn på den till.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Du kan hitta den här informationen i Azure Portal genom att gå till ditt lagrings konto och välja **åtkomst nycklar** i avsnittet **Inställningar** .

Installera de nödvändiga beroendena. Det gör du genom att öppna en kommando tolk, navigera till mappen exempel och sedan ange `npm install`.

---

## <a name="run-the-console-application"></a>Kör konsolprogrammet

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

I Visual Studio trycker du på **F5** eller väljer **Start** för att starta felsökning av programmet. Visual Studio återställer automatiskt NuGet-paket som saknas (om konfigurerat). Gå till avsnittet om hur du [installerar och ominstallerar paket med paketåterställning](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) om du vill veta mer.

Ett konsolfönster öppnas och programmet körs. Programmet överför bilden **HelloWorld.png** från lösningen till lagringskontot. Programmet kontrollerar att bilden har replikerats till den sekundära RA-GRS-slutpunkten. Sedan börjar programmet ladda ned bilden upp till 999 gånger. Varje läsning representeras av ett **P** eller ett **S**. **P** representerar den primära slutpunkten och **S** representerar den sekundära slutpunkten.

![Konsolprogrammet körs](media/storage-create-geo-redundant-storage/figure3.png)

I exempelkoden används aktiviteten `RunCircuitBreakerAsync` i filen `Program.cs` för att ladda ned en bild från lagringskontot med hjälp av metoden [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Före nedladdningen definieras en [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext). Åtgärdskontexten definierar de händelsehanterare som utlöses när en nedladdning slutförs eller om en nedladdning misslyckas och ett nytt försök görs.

# <a name="pythontabpython"></a>[Python](#tab/python)

Om du vill köra programmet i en terminal eller kommandotolk går du till katalogen **circuitbreaker.py** och skriver `python circuitbreaker.py`. Programmet överför bilden **HelloWorld.png** från lösningen till lagringskontot. Programmet kontrollerar att bilden har replikerats till den sekundära RA-GRS-slutpunkten. Sedan börjar programmet ladda ned bilden upp till 999 gånger. Varje läsning representeras av ett **P** eller ett **S**. **P** representerar den primära slutpunkten och **S** representerar den sekundära slutpunkten.

![Konsolprogrammet körs](media/storage-create-geo-redundant-storage/figure3.png)

I exempelkoden används metoden `run_circuit_breaker` i filen `circuitbreaker.py` för att ladda ned en bild från lagringskontot med hjälp av metoden [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html).

Återförsöksfunktionen för lagringsobjektet har angetts till en linjär återförsöksprincip. Återförsöksfunktionen bestämmer om det ska göras ett nytt försök med begäran och anger antalet sekunder som ska förflyta innan det görs ett nytt försök. Ange true för värdet **retry\_to\_secondary** om det ska göras ett nytt försök till den sekundära slutpunkten om den första begäran till den primära slutpunkten misslyckas. I exempelprogrammet definieras en anpassad återförsöksprincip i funktionen `retry_callback` för lagringsobjektet.

Innan nedladdningen definieras funktionen [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) och [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) för tjänst objekt. Funktionerna definierar de händelsehanterare som utlöses när en nedladdning slutförs eller om en nedladdning misslyckas och ett nytt försök görs.

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

Du kör exemplet med hjälp av Maven på kommandoraden.

1. öppna ett gränssnitt och gå till **storage-blobs-java-v10-quickstart** i den klonade katalogen.
2. Ange `mvn compile exec:java`.

Det här exemplet skapar en test fil i din standard katalog. För Windows-användare är katalogen **AppData\Local\Temp**. Exemplet visar sedan följande alternativ med kommandon som du kan ange:

- Ange **P** om du vill köra en skicka BLOB-åtgärd överförs en tillfällig fil till ditt lagrings konto via det här kommandot.
- Ange **L** för att utföra en List-BLOB-åtgärd, visar det här kommandot de blobar som för närvarande finns i din behållare.
- Ange **G** om du vill utföra en get-BLOB-åtgärd laddar detta kommando ned en fil från ditt lagrings konto till din lokala dator.
- Om **du vill köra** en Delete BLOB-åtgärd, tar det här kommandot bort blobben från ditt lagrings konto.
- Om **du vill stänga** exemplet tar du även bort alla resurser som exemplet skapade.

Det här exemplet visar dina utdata om du kör programmet på Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Du kontrollerar exemplet, så ange kommandon så att den kör koden. Indata är skiftlägeskänsliga.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

Kör exemplet genom att öppna en kommando tolk, navigera till mappen exempel och sedan ange `node index.js`.

Exemplet skapar en behållare i ditt Blob Storage-konto, överför **HelloWorld. png** till behållaren och kontrollerar sedan upprepade gånger om behållaren och avbildningen har repliker ATS till den sekundära regionen. Efter replikeringen tillfrågas du om du vill ange **D** eller **Q** (följt av retur) för att ladda ned eller avsluta. Dina utdata bör se ut ungefär som i följande exempel:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Händelsehanterare för nytt försök

Händelsehanteraren `OperationContextRetrying` anropas när nedladdningen av bilden misslyckas och den är inställd på att göra nya försök. Om det maximala antalet återförsök som har definierats i programmet nås ändras [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) för begäran till `SecondaryOnly`. Den här inställningen tvingar programmet att försöka ladda ned bilden från den sekundära slutpunkten. Med den här konfigurationen tar det kortare tid att begära bilden eftersom det inte görs oändliga försök att hämta den från den primära slutpunkten.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Händelsehanterare för slutförd begäran

Händelsehanteraren `OperationContextRequestCompleted` anropas när nedladdningen av bilden slutförs. Om programmet använder den sekundära slutpunkten fortsätter programmet att använda den här slutpunkten upp till 20 gånger. Efter 20 gånger återställer programmet [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) till `PrimaryThenSecondary` och försöker på nytt med den primära slutpunkten. Om en begäran lyckas fortsätter programmet att läsa från den primära slutpunkten.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Händelsehanterare för nytt försök

Händelsehanteraren `retry_callback` anropas när nedladdningen av bilden misslyckas och den är inställd på att göra nya försök. Om det maximala antalet återförsök som har definierats i programmet nås ändras [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) för begäran till `SECONDARY`. Den här inställningen tvingar programmet att försöka ladda ned bilden från den sekundära slutpunkten. Med den här konfigurationen tar det kortare tid att begära bilden eftersom det inte görs oändliga försök att hämta den från den primära slutpunkten.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Händelsehanterare för slutförd begäran

Händelsehanteraren `response_callback` anropas när nedladdningen av bilden slutförs. Om programmet använder den sekundära slutpunkten fortsätter programmet att använda den här slutpunkten upp till 20 gånger. Efter 20 gånger återställer programmet [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) till `PRIMARY` och försöker på nytt med den primära slutpunkten. Om en begäran lyckas fortsätter programmet att läsa från den primära slutpunkten.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

Med Java v10 SDK är det onödigt att definiera återanrops hanterare och SDK nu har några grundläggande skillnader jämfört med v7 SDK. I stället för LocationMode finns en sekundär **pipeline**. Du kan definiera en sekundär pipeline via **RequestRetryOptions**. Om en sådan definieras kan programmet automatiskt växla till den sekundära pipelinen om det inte når dina data genom den primära pipelinen.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

Med Node. js v10 SDK är återanrops hanterare onödig. I stället skapar exemplet en pipeline som kon figurer ATS med alternativ för återförsök och en sekundär slut punkt. Detta gör att programmet automatiskt växlar till den sekundära pipelinen om det inte går att komma åt dina data via den primära pipelinen.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Nästa steg

I den första delen i serien lärde du dig hur du skapar ett program med hög tillgänglighet med RA-GRS-lagringskonton.

Gå vidare till den andra delen i serien och lär dig hur du simulerar ett fel och tvingar programmet att använda den sekundära RA-GRS-slutpunkten.

> [!div class="nextstepaction"]
> [Simulera ett fel i anslutningen till din primära lagringsslutpunkt](storage-simulate-failure-ragrs-account-app.md)
