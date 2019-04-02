---
title: 'Självstudier: Skapa ett program med hög tillgänglighet med Blob-lagring – Azure Storage'
description: Använd RA-GRS för att ge programdata hög tillgänglighet
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: c4e81d9be09855cde986bfd21f8f688fa7d1341e
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793727"
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

* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
  - **Azure Development**

  ![Azure-utveckling (under Web & Cloud (Webb och moln))](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* Installera [Python](https://www.python.org/downloads/)
* Ladda ned och installera [Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python)

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

* Installera och konfigurera [Maven](https://maven.apache.org/download.cgi) så att det fungerar från kommandoraden
* Installera och konfigurera [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

* Installera och konfigurera [Maven](http://maven.apache.org/download.cgi) så att det fungerar från kommandoraden
* Installera och konfigurera [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

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
   | **Replikering**| Geo-redundant lagring med läsbehörighet (RA-GRS) | Detta krävs för att exemplet ska fungera. |
   |**Prenumeration** | din prenumeration |Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Plats** | Östra USA | Välj en plats. |

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

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

[Hämta exempelprojektet](https://github.com/Azure-Samples/storage-java-ha-ra-grs) och extrahera filen storage-java-ragrs.zip. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett grundläggande Java-program.

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

[Hämta exempelprojektet](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs) och extrahera filen storage-java-ragrs.zip. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett grundläggande Java-program.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

---

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Det rekommenderas att du lagrar den här anslutningssträngen i en miljövariabel på den lokala datorn där programmet körs. Följ något av exemplen nedan beroende på operativsystemet för att skapa miljövariabeln.

Navigera till ditt lagringskonto i Azure Portal. Välj **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Kopiera **anslutningssträngen** från den primära eller sekundära nyckeln. Ersätt \<yourconnectionstring\> med den faktiska anslutningssträngen genom att köra ett av följande kommandon beroende på vilket operativsystem du har. Kommandot sparar en miljövariabel till den lokala datorn. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** eller gränssnittet på nytt. Ersätt **\<storageConnectionString\>** i följande exempel:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Det rekommenderas att du lagrar den här anslutningssträngen i en miljövariabel på den lokala datorn där programmet körs. Följ något av exemplen nedan beroende på operativsystemet för att skapa miljövariabeln.

Navigera till ditt lagringskonto i Azure Portal. Välj **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Kopiera **anslutningssträngen** från den primära eller sekundära nyckeln. Ersätt \<yourconnectionstring\> med den faktiska anslutningssträngen genom att köra ett av följande kommandon beroende på vilket operativsystem du har. Kommandot sparar en miljövariabel till den lokala datorn. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** eller gränssnittet på nytt. Ersätt **\<storageConnectionString\>** i följande exempel:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Det rekommenderas att du lagrar den här anslutningssträngen i en miljövariabel på den lokala datorn där programmet körs. Följ något av exemplen nedan beroende på operativsystemet för att skapa miljövariabeln.

Navigera till ditt lagringskonto i Azure Portal. Välj **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Kopiera **anslutningssträngen** från den primära eller sekundära nyckeln. Ersätt \<yourconnectionstring\> med den faktiska anslutningssträngen genom att köra ett av följande kommandon beroende på vilket operativsystem du har. Kommandot sparar en miljövariabel till den lokala datorn. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** eller gränssnittet på nytt. Ersätt **\<storageConnectionString\>** i följande exempel:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

Det här exemplet kräver att du lagrar namnet och nyckeln för ditt lagringskonto på ett säkert sätt. Lagra dem i miljövariabler som är lokala på den dator som kommer att köra exemplet. Använd antingen Linux- eller Windows-exemplet, beroende på operativsystemet, för att skapa miljövariablerna. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** eller gränssnittet på nytt.

### <a name="linux-example"></a>Linux-exemplet

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Windows-exempel

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-console-application"></a>Kör konsolprogrammet

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

I Visual Studio trycker du på **F5** eller väljer **Start** för att starta felsökning av programmet. Visual Studio återställer automatiskt NuGet-paket som saknas (om konfigurerat). Gå till avsnittet om hur du [installerar och ominstallerar paket med paketåterställning](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) om du vill veta mer.

Ett konsolfönster öppnas och programmet körs. Programmet överför bilden **HelloWorld.png** från lösningen till lagringskontot. Programmet kontrollerar att bilden har replikerats till den sekundära RA-GRS-slutpunkten. Sedan börjar programmet ladda ned bilden upp till 999 gånger. Varje läsning representeras av ett **P** eller ett **S**. **P** representerar den primära slutpunkten och **S** representerar den sekundära slutpunkten.

![Konsolprogrammet körs](media/storage-create-geo-redundant-storage/figure3.png)

I exempelkoden används aktiviteten `RunCircuitBreakerAsync` i filen `Program.cs` för att ladda ned en bild från lagringskontot med hjälp av metoden [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). Före nedladdningen definieras en [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). Åtgärdskontexten definierar de händelsehanterare som utlöses när en nedladdning slutförs eller om en nedladdning misslyckas och ett nytt försök görs.

# <a name="pythontabpython"></a>[Python](#tab/python)

Om du vill köra programmet i en terminal eller kommandotolk går du till katalogen **circuitbreaker.py** och skriver `python circuitbreaker.py`. Programmet överför bilden **HelloWorld.png** från lösningen till lagringskontot. Programmet kontrollerar att bilden har replikerats till den sekundära RA-GRS-slutpunkten. Sedan börjar programmet ladda ned bilden upp till 999 gånger. Varje läsning representeras av ett **P** eller ett **S**. **P** representerar den primära slutpunkten och **S** representerar den sekundära slutpunkten.

![Konsolprogrammet körs](media/storage-create-geo-redundant-storage/figure3.png)

I exempelkoden används metoden `run_circuit_breaker` i filen `circuitbreaker.py` för att ladda ned en bild från lagringskontot med hjälp av metoden [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html).

Återförsöksfunktionen för lagringsobjektet har angetts till en linjär återförsöksprincip. Återförsöksfunktionen bestämmer om det ska göras ett nytt försök med begäran och anger antalet sekunder som ska förflyta innan det görs ett nytt försök. Ange true för värdet **retry\_to\_secondary** om det ska göras ett nytt försök till den sekundära slutpunkten om den första begäran till den primära slutpunkten misslyckas. I exempelprogrammet definieras en anpassad återförsöksprincip i funktionen `retry_callback` för lagringsobjektet.

Före nedladdningen definieras funktionerna [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) och [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) för objektet. Funktionerna definierar de händelsehanterare som utlöses när en nedladdning slutförs eller om en nedladdning misslyckas och ett nytt försök görs.

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

Du kan köra programmet genom att öppna en terminal eller Kommandotolken begränsad till den nedladdade programmappen. Därifrån anger du `mvn compile exec:java` för att köra programmet. Programmet överför bilden **HelloWorld.png** från katalogen till ditt lagringskonto och kontrollerar att bilden har replikerats till den sekundära RA-GRS-slutpunkten. När kontrollen är slutförd börjar programmet ladda ned bilden upprepade gånger samtidigt som det rapporterar tillbaka till slutpunkten som nedladdningen görs från.

Återförsöksfunktionen för lagringsobjektet är inställd på att använda en linjär återförsöksprincip. Återförsöksfunktionen bestämmer om det ska göras ett nytt försök med begäran och anger antalet sekunder som ska förflyta mellan varje nytt försök. Egenskapen **LocationMode** för **BlobRequestOptions** har angetts till **PRIMARY\_THEN\_SECONDARY**. Det gör att programmet automatiskt växlar till den sekundära platsen om det inte lyckas nå den primära platsen för att hämta **HelloWorld.png**.

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

Du kör exemplet med hjälp av Maven på kommandoraden.

1. öppna ett gränssnitt och gå till **storage-blobs-java-v10-quickstart** i den klonade katalogen.
2. Ange `mvn compile exec:java`.

Det här exemplet skapar en testfil i din standardkatalog. För Windows-användare är det **AppData\Local\Temp**. Exemplet visar sedan följande alternativ med kommandon som du kan ange:

- Ange **P** för att köra en placeringsåtgärd (Put) för blob. Då laddas en temp-fil upp till ditt lagringskonto.
- Ange **L** för att utföra en liståtgärd för blob. Då visas en lista över de blobar som för närvarande finns i din container.
- Ange **G** för att utföra en hämtningsåtgärd (Get) för blob. Då laddas en fil ned från ditt lagringskonto till din lokala dator.
- Ange **D** för att köra en borttagningsåtgärd (Delete) för blob. tas bloben bort från lagringskontot.
- Ange **E** för att stänga exemplet. Detta gör även att alla resurser som exemplet skapade tas bort.

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

---

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Händelsehanterare för nytt försök

Händelsehanteraren `OperationContextRetrying` anropas när nedladdningen av bilden misslyckas och den är inställd på att göra nya försök. Om det maximala antalet återförsök som har definierats i programmet nås ändras [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) för begäran till `SecondaryOnly`. Den här inställningen tvingar programmet att försöka ladda ned bilden från den sekundära slutpunkten. Med den här konfigurationen tar det kortare tid att begära bilden eftersom det inte görs oändliga försök att hämta den från den primära slutpunkten.

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

Händelsehanteraren `OperationContextRequestCompleted` anropas när nedladdningen av bilden slutförs. Om programmet använder den sekundära slutpunkten fortsätter programmet att använda den här slutpunkten upp till 20 gånger. Efter 20 gånger återställer programmet [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) till `PrimaryThenSecondary` och försöker på nytt med den primära slutpunkten. Om en begäran lyckas fortsätter programmet att läsa från den primära slutpunkten.

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
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
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

# <a name="java-v7-sdktabjava-v7"></a>[Java V7 SDK](#tab/java-v7)

Med Java behöver du inte definiera återanropshanterare om egenskapen **LocationMode** för **BlobRequestOptions** är inställd på **PRIMARY\_THEN\_SECONDARY**. Det gör att programmet automatiskt växlar till den sekundära platsen om det inte lyckas nå den primära platsen för att hämta **HelloWorld.png**.

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

Med Java V10 SDK är det fortfarande onödigt att definiera motringningshanterare, och det finns nu vissa grundläggande skillnader i SDK:t jämfört med V7 SDK. I stället för LocationMode finns en sekundär **pipeline**. Du kan definiera en sekundär pipeline via **RequestRetryOptions**. Om en sådan definieras kan programmet automatiskt växla till den sekundära pipelinen om det inte når dina data genom den primära pipelinen.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```

---

## <a name="next-steps"></a>Nästa steg

I den första delen i serien lärde du dig hur du skapar ett program med hög tillgänglighet med RA-GRS-lagringskonton.

Gå vidare till den andra delen i serien och lär dig hur du simulerar ett fel och tvingar programmet att använda den sekundära RA-GRS-slutpunkten.

> [!div class="nextstepaction"]
> [Simulera ett fel i anslutningen till din primära lagringsslutpunkt](storage-simulate-failure-ragrs-account-app.md)
