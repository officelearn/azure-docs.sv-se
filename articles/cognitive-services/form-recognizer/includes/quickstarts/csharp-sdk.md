---
title: 'Snabb start: formulär tolkens klient bibliotek för .NET'
description: I den här snabb starten kommer du igång med formulär tolkens klient bibliotek för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: a1015c1a2d0722d2525d2184790ee9391de25613
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85805750"
---
[Referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/).
* En Azure Storage-blob som innehåller en uppsättning tränings data. Se [skapa en tränings data uppsättning för en anpassad modell](../../build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här snabb starten kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451).
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-form-recognizer-azure-resource"></a>Skapa en Azure-resurs för formulär igenkänning

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Skapa miljövariabler

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `formrecognizer-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: _program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Bygg sedan programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Från projekt katalogen öppnar du *program.cs* -filen i önskat redigerings program eller IDE. Lägg till följande `using` direktiv:

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.IO;
using System.Threading.Tasks;
```

Lägg sedan till följande kod i programmets **main** -metod. Du ska definiera den här asynkrona uppgiften senare. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du formulär tolkens klient bibliotek för .NET med följande kommando:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med formulär tolkens klient bibliotek för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera formulär innehåll](#recognize-form-content)
* [Identifiera kvitton](#recognize-receipts)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autentisera klienten

Under `Main` metoden definierar du uppgiften som refereras till i `Main` . Här ska du autentisera två klient objekt med de prenumerationsfiler som du definierade ovan. Du använder ett **AzureKeyCredential** -objekt, så om det behövs kan du uppdatera API-nyckeln utan att skapa nya klient objekt.

```csharp
static async Task RunFormRecognizerClient()
{ 
    string endpoint = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_ENDPOINT");
    string apiKey = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_KEY");
    var credential = new AzureKeyCredential(apiKey);
    
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    var recognizerClient = new FormRecognizerClient(new Uri(endpoint), credential);
```

### <a name="call-client-specific-methods"></a>Anropa klient-/regionsspecifika metoder

Nästa kodblock använder klient objekt för att anropa metoder för var och en av de viktigaste uppgifterna i formulär igenkännings-SDK: n. Du ska definiera dessa metoder senare.

Du måste också lägga till referenser till URL: erna för din utbildning och testa data. 
* Om du vill hämta SAS-URL: en för din anpassade modell inlärnings data öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Den bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Om du vill få en URL för ett formulär att testa kan du använda ovanstående steg för att hämta SAS-URL: en för ett enskilt dokument i Blob Storage. Eller ta med URL: en för ett dokument som finns på annan plats.
* Använd metoden ovan för att hämta URL: en för en kvitto avbildning, eller Använd URL: en för den exempel bild som anges.

> [!NOTE]
> Kodfragmenten i den här guiden använder fjärrformulär som används av URL: er. Om du vill bearbeta lokala formulär dokument i stället, se de relaterade metoderna i [referens dokumentationen](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer).

```csharp
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    Console.WriteLine("Get form content...");
    await GetContent(recognizerClient, formUrl);

    Console.WriteLine("Analyze receipt...");
    await AnalyzeReceipt(recognizerClient, receiptUrl);

    Console.WriteLine("Train Model with training data...");
    Guid modelId = await TrainModel(trainingClient, trainingDataUrl);

    Console.WriteLine("Analyze PDF form...");
    await AnalyzePdfForm(recognizerClient, modelId, formUrl);

    Console.WriteLine("Manage models...");
    await ManageModels(trainingClient, trainingDataUrl) ;
}
```

## <a name="recognize-form-content"></a>Identifiera formulär innehåll

Du kan använda formulär igenkänning för att identifiera tabeller, rader och ord i dokument, utan att behöva träna en modell.

Om du vill identifiera innehållet i en fil vid en specifik URI använder du metoden **StartRecognizeContentFromUri** .

```csharp
private static async Task<Guid> GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<IReadOnlyList<FormPage>> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

Det returnerade värdet är en samling **FormPage** -objekt: en för varje sida i det dokument som skickas. Följande kod itererar igenom dessa objekt och skriver ut de extraherade nyckel-och värdeparen och tabell data.

```csharp
    foreach (FormPage page in formPages.Value)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count}" + 
            $" lines.");
    
        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count}" + 
                $" word{(line.Words.Count > 1 ? "s" : "")}," +
                $" and text: '{line.Text}'.");
        }
    
        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and" +
                $" {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})"
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Identifiera kvitton

Det här avsnittet visar hur du identifierar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvitto modell.

Om du vill känna igen kvitton från en URI använder du metoden **StartRecognizeReceiptsFromUri** . Det returnerade värdet är en samling **RecognizedReceipt** -objekt: en för varje sida i det dokument som skickas. Följande kod bearbetar ett kvitto vid den aktuella URI: n och skriver ut de viktigaste fälten och värdena till-konsolen.

```csharp
private static async Task<Guid> AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    RecognizedReceiptCollection receipts = await recognizerClient.StartRecognizeReceiptsFromUri(new Uri(receiptUri))
    .WaitForCompletionAsync();

    foreach (RecognizedReceipt receipt in receipts)
    {
    FormField merchantNameField;
    if (receipt.RecognizedForm.Fields.TryGetValue("MerchantName", out merchantNameField))
    {
        if (merchantNameField.Value.Type == FieldValueType.String)
        {
            string merchantName = merchantNameField.Value.AsString();

            Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
        }
    }

    FormField transactionDateField;
    if (receipt.RecognizedForm.Fields.TryGetValue("TransactionDate", out transactionDateField))
    {
        if (transactionDateField.Value.Type == FieldValueType.Date)
        {
            DateTime transactionDate = transactionDateField.Value.AsDate();

            Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
        }
    }
```

Nästa kodblock upprepas genom de enskilda objekt som identifierats vid inleveransen och skriver ut information till-konsolen.

```csharp
    FormField itemsField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Items", out itemsField))
    {
        if (itemsField.Value.Type == FieldValueType.List)
        {
            foreach (FormField itemField in itemsField.Value.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.Value.Type == FieldValueType.Dictionary)
                {
                    IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                    FormField itemNameField;
                    if (itemFields.TryGetValue("Name", out itemNameField))
                    {
                        if (itemNameField.Value.Type == FieldValueType.String)
                        {
                            string itemName = itemNameField.Value.AsString();

                            Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                        }
                    }

                    FormField itemTotalPriceField;
                    if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                    {
                        if (itemTotalPriceField.Value.Type == FieldValueType.Float)
                        {
                            float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                            Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                        }
                    }
                }
            }
        }
    }
```

Slutligen skriver det sista blocket kod ut det totala värdet för inleveransen.

```csharp
    FormField totalField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Total", out totalField))
    {
        if (totalField.Value.Type == FieldValueType.Float)
        {
            float total = totalField.Value.AsFloat();

            Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
        }
    }
}
```

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En utbildad modell kan spara strukturerade data som innehåller nyckel-/värde relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användar gränssnitt, t. ex. [formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka utbildnings dokumenten.

Följande metod tränar en modell på en specifik uppsättning dokument och skriver ut modellens status till-konsolen. 

```csharp
private static async Task<Guid> TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: false).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

Det returnerade **CustomFormModel** -objektet innehåller information om formulär typerna som modellen kan identifiera och vilka fält som kan extraheras från varje formulär typ. Följande kod block skriver ut den här informationen till-konsolen.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

Slutligen returnerar den här metoden det unika ID: t för modellen.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

Du kan också träna anpassade modeller genom att manuellt märka utbildnings dokumenten. Utbildning med etiketter leder till bättre prestanda i vissa scenarier. För att träna med etiketter måste du ha särskilda etikett informations filer (* \<filename\>.pdf.labels.jspå*) i din Blob Storage-behållare tillsammans med utbildnings dokumenten. [Formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md) innehåller ett användar gränssnitt som hjälper dig att skapa etikettfiler. När du har gjort det kan du anropa metoden **StartTrainingAsync** med parametern *uselabels* inställd på `true` .

```csharp
private static async Task<Guid> TrainModelWithLabelsAsync(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
    .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: true).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

Den returnerade **CustomFormModel** anger de fält som modellen kan extrahera, tillsammans med dess beräknade noggrannhet i varje fält. Följande kod block skriver ut den här informationen till-konsolen.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Accuracy != null)
            {
                Console.Write($", Accuracy: {field.Accuracy}");
            }
            Console.WriteLine("");
        }
    }
    return model.ModelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Det här avsnittet visar hur du extraherar nyckel/värde-information och annat innehåll från dina anpassade formulär typer med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan. Se avsnittet [träna en modell](#train-a-model-without-labels) .

Du använder metoden **StartRecognizeCustomFormsFromUri** . Det returnerade värdet är en samling **RecognizedForm** -objekt: en för varje sida i det dokument som skickas.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient formClient, Guid modelId, string pdfFormFile)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId, new Uri(formUri))
        .WaitForCompletionAsync();
```

Följande kod skriver ut analys resultaten till-konsolen. Det skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroende poäng.

```csharp
    foreach (RecognizedForm form in forms.Value)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");
    
            if (field.LabelText != null)
            {
                Console.WriteLine($"    Label: '{field.LabelText.Text}");
            }
    
            Console.WriteLine($"    Value: '{field.ValueText.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

## <a name="manage-your-custom-models"></a>Hantera dina anpassade modeller

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto. Följande kod utför alla modell hanterings aktiviteter i en enda metod, som exempel. Börja med att kopiera metod signaturen nedan:

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrol lera antalet modeller i resurs kontot för FormRecognizer

Följande kod block kontrollerar hur många modeller som du har sparat i ditt formulärs igenkännings konto och jämför dem med konto gränsen.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista de modeller som för närvarande lagras i resurs kontot

Följande kodblock visar de aktuella modellerna i ditt konto och skriver ut information till-konsolen.

```csharp
    // List the first ten or fewer models currently stored in the account.
    Pageable<CustomFormModelInfo> models = trainingClient.GetModelInfos();
    
    foreach (CustomFormModelInfo modelInfo in models.Take(10))
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Created On: {modelInfo.CreatedOn}");
        Console.WriteLine($"    Last Modified: {modelInfo.LastModified}");
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Hämta en speciell modell med modellens ID

Följande kod blockerar tågen en ny modell (precis som i avsnittet [träna en modell](#train-a-model-without-labels) ) och hämtar sedan en andra referens till den med hjälp av dess ID.

```csharp
    // Create a new model to store in the account
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingFileUrl)).WaitForCompletionAsync();
    
    // Get the model that was just created
    CustomFormModel modelCopy = trainingClient.GetCustomModel(model.ModelId);
    
    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following" +
        " form types:");
    
    foreach (CustomFormSubModel subModel in modelCopy.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurs kontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>Kör programmet

Kör programmet från program katalogen med `dotnet run` kommandot.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

När du interagerar med Cognitive Services formulär tolkens klient bibliotek med hjälp av .NET SDK, resulterar felen som returneras av tjänsten i en `RequestFailedException` . De innehåller samma HTTP-statuskod som skulle ha returnerats av en REST API begäran.

Om du till exempel skickar en kvitto avbildning med en ogiltig URI `400` returneras ett fel som anger "felaktig begäran".

```csharp Snippet:FormRecognizerBadRequest
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();

}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Du ser att ytterligare information, t. ex. klient begär ande-ID för åtgärden, loggas.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken .NET-klient bibliotek för att träna modeller och analysera formulär på olika sätt. Nu ska du lära dig hur du skapar en bättre tränings data uppsättning och ger mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempel koden från den här guiden (och mer) finns på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
