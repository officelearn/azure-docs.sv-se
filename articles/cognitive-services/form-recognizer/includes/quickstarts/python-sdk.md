---
title: 'Snabb start: formulär tolkens klient bibliotek för python'
description: I den här snabb starten kommer du igång med formulär tolkens klient bibliotek för python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: 3300bd53be36e34ff32267605352d6f80923724e
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277956"
---
[Referens dokumentation](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Paket (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* En Azure Storage-blob som innehåller en uppsättning tränings data. Se [skapa en tränings data uppsättning för en anpassad modell](../../build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här snabb starten kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451).
* [Python 2,7, eller 3,5 eller senare](https://www.python.org/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-form-recognizer-azure-resource"></a>Skapa en Azure-resurs för formulär igenkänning

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Skapa miljövariabler

[!INCLUDE [environment-variables](../environment-variables.md)]


### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa ett nytt python-program i önskat redigerings program eller IDE. Importera sedan följande bibliotek.

```python
import os
import azure.ai.formrecognizer
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import ResourceNotFoundError
```

Skapa variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln när du har startat programmet måste du stänga och öppna redigeraren, IDE eller Shell igen för att få åtkomst till variabeln.

```python
endpoint = os.environ["FORM_RECOGNIZER_ENDPOINT"]
key = os.environ["FORM_RECOGNIZER_KEY"]
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

```console
pip install azure-ai-formrecognizer
```

<!-- 
tbd object model
-->

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med formulär tolkens klient bibliotek för python:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera formulär innehåll](#recognize-form-content)
* [Identifiera kvitton](#recognize-receipts)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autentisera klienten

Här ska du autentisera två klient objekt med de prenumerationsfiler som du definierade ovan. Du använder ett **AzureKeyCredential** -objekt, så om det behövs kan du uppdatera API-nyckeln utan att skapa nya klient objekt.

```python
form_recognizer_client = FormRecognizerClient(endpoint=endpoint, credential=AzureKeyCredential(key))

form_training_client = FormTrainingClient(endpoint, AzureKeyCredential(key))
```

## <a name="define-variables"></a>Definiera variabler

> [!NOTE]
> Kodfragmenten i den här guiden använder fjärrformulär som används av URL: er. Om du vill bearbeta lokala formulär dokument i stället, se de relaterade metoderna i [referens dokumentation](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) och [exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Du måste också lägga till referenser till URL: erna för din utbildning och testa data.
* Om du vill hämta SAS-URL: en för din anpassade modell inlärnings data öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Den bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Om du vill få en URL för ett formulär att testa kan du använda ovanstående steg för att hämta SAS-URL: en för ett enskilt dokument i Blob Storage. Eller ta med URL: en för ett dokument som finns på annan plats.
* Använd metoden ovan för att hämta URL: en för en kvitto avbildning, eller Använd URL: en för den exempel bild som anges.

```python
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"
formUrl = "<SAS-URL-of-a-form-in-blob-storage>"
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"
```

## <a name="recognize-form-content"></a>Identifiera formulär innehåll

Du kan använda formulär igenkänning för att identifiera tabeller, rader och ord i dokument, utan att behöva träna en modell.

Använd metoden **begin_recognize_content** för att identifiera innehållet i en fil på en viss URL.

```Python
poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
contents = poller.result()
```

Det returnerade värdet är en samling **FormPage** -objekt: en för varje sida i det dokument som skickas. Följande kod itererar igenom dessa objekt och skriver ut de extraherade nyckel-och värdeparen och tabell data.

```python
for idx, content in enumerate(contents):
    print("----Recognizing content from page #{}----".format(idx))
    print("Has width: {} and height: {}, measured with unit: {}".format(
        content.width,
        content.height,
        content.unit
    ))
    for table_idx, table in enumerate(content.tables):
        print("Table # {} has {} rows and {} columns".format(table_idx, table.row_count, table.column_count))
        for cell in table.cells:
            print("...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                cell.row_index,
                cell.column_index,
                cell.text,
                format_bounding_box(cell.bounding_box)
            ))
    for line_idx, line in enumerate(content.lines):
        print("Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
            line_idx,
            len(line.words),
            line.text,
            format_bounding_box(line.bounding_box)
        ))
    print("----------------------------------------")
```

Koden ovan använder en hjälp funktion `format_bounding_box` för att förenkla koordinaterna i en avgränsnings ruta. Definiera den separat:

```python
def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])
```

## <a name="recognize-receipts"></a>Identifiera kvitton

Det här avsnittet visar hur du identifierar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvitto modell. Om du vill känna igen kvitton från en URL använder du metoden **begin_recognize_receipts_from_url** . 

```python
poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
receipts = poller.result()
```

Det returnerade värdet är en samling **RecognizedReceipt** -objekt: en för varje sida i det dokument som skickas. Följande kodblock skriver ut grundläggande kvitto information till-konsolen.

```python
for idx, receipt in enumerate(receipts):
    print("--------Recognizing receipt #{}--------".format(idx))
    receipt_type = receipt.fields.get("ReceiptType")
    if receipt_type:
        print("Receipt Type: {} has confidence: {}".format(receipt_type.value, receipt_type.confidence))
    merchant_name = receipt.fields.get("MerchantName")
    if merchant_name:
        print("Merchant Name: {} has confidence: {}".format(merchant_name.value, merchant_name.confidence))
    transaction_date = receipt.fields.get("TransactionDate")
    if transaction_date:
        print("Transaction Date: {} has confidence: {}".format(transaction_date.value, transaction_date.confidence))
```

Nästa kodblock upprepas genom de enskilda objekt som identifierats vid inleveransen och skriver ut information till-konsolen.


```python
    print("Receipt items:")
    for idx, item in enumerate(receipt.fields.get("Items").value):
        print("...Item #{}".format(idx))
        item_name = item.value.get("Name")
        if item_name:
            print("......Item Name: {} has confidence: {}".format(item_name.value, item_name.confidence))
        item_quantity = item.value.get("Quantity")
        if item_quantity:
            print("......Item Quantity: {} has confidence: {}".format(item_quantity.value, item_quantity.confidence))
        item_price = item.value.get("Price")
        if item_price:
            print("......Individual Item Price: {} has confidence: {}".format(item_price.value, item_price.confidence))
        item_total_price = item.value.get("TotalPrice")
        if item_total_price:
            print("......Total Item Price: {} has confidence: {}".format(item_total_price.value, item_total_price.confidence))
```

Slutligen skriver det sista blocket kod ut resten av de större kvitto uppgifterna.

```python
    subtotal = receipt.fields.get("Subtotal")
    if subtotal:
        print("Subtotal: {} has confidence: {}".format(subtotal.value, subtotal.confidence))
    tax = receipt.fields.get("Tax")
    if tax:
        print("Tax: {} has confidence: {}".format(tax.value, tax.confidence))
    tip = receipt.fields.get("Tip")
    if tip:
        print("Tip: {} has confidence: {}".format(tip.value, tip.confidence))
    total = receipt.fields.get("Total")
    if total:
        print("Total: {} has confidence: {}".format(total.value, total.confidence))
    print("--------------------------------------")
```


## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En utbildad modell kan spara strukturerade data som innehåller nyckel-/värde relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användar gränssnitt, t. ex. [formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka utbildnings dokumenten.

I följande kod används Training-klienten med funktionen **begin_training** för att träna en modell på en specifik uppsättning dokument.

```python
poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=False)
model = poller.result()
```

Det returnerade **CustomFormSubmodel** -objektet innehåller information om formulär typerna som modellen kan identifiera och vilka fält som kan extraheras från varje formulär typ. Följande kod block skriver ut den här informationen till-konsolen.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.requested_on))
print("Last modified: {}".format(model.completed_on))

print("Recognized fields:")
# Looping through the submodels, which contains the fields they were trained on
for submodel in model.submodels:
    print("...The submodel has form type '{}'".format(submodel.form_type))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have label '{}'".format(
            name, field.label
        ))
```

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

Du kan också träna anpassade modeller genom att manuellt märka utbildnings dokumenten. Utbildning med etiketter leder till bättre prestanda i vissa scenarier. 

> [!IMPORTANT]
> För att träna med etiketter måste du ha särskilda etikett informations filer (* \<filename\>.pdf.labels.jspå*) i din Blob Storage-behållare tillsammans med utbildnings dokumenten. [Formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md) innehåller ett användar gränssnitt som hjälper dig att skapa etikettfiler. När du har gjort det kan du anropa funktionen **begin_training** med parametern *use_training_labels* inställd på `true` .

```python
poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
model = poller.result()
```

Den returnerade **CustomFormSubmodel** anger de fält som modellen kan extrahera, tillsammans med dess beräknade noggrannhet i varje fält. Följande kod block skriver ut den här informationen till-konsolen.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# looping through the submodels, which contains the fields they were trained on
# The labels are based on the ones you gave the training document.
for submodel in model.submodels:
    print("...The submodel with form type {} has accuracy '{}'".format(submodel.form_type, submodel.accuracy))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have name '{}' with an accuracy of {}".format(
            name, field.name, field.accuracy
        ))
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Det här avsnittet visar hur du extraherar nyckel/värde-information och annat innehåll från dina anpassade formulär typer med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan. Se avsnittet [träna en modell](#train-a-model-without-labels) .

Du använder metoden **begin_recognize_custom_forms_from_url** . Det returnerade värdet är en samling **RecognizedForm** -objekt: en för varje sida i det dokument som skickas.

```python
# Make sure your form's type is included in the list of form types the custom model can recognize
poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model.model_id, form_url=formUrl)
forms = poller.result()
```

Följande kod skriver ut analys resultaten till-konsolen. Det skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroende poäng.

```python
for idx, form in enumerate(forms):
    print("--------Recognizing Form #{}--------".format(idx))
    print("Form {} has type {}".format(idx, form.form_type))
    for name, field in form.fields.items():
        # each field is of type FormField
        # The value of the field can also be a FormField, or a list of FormFields
        # In our sample, it is just a FormField.
        print("...Field '{}' has value '{}' with a confidence score of {}".format(
            name, field.value, field.confidence
        ))
        # label data is populated if you are using a model trained with unlabeled data, since the service needs to make predictions for
        # labels if not explicitly given to it.
        if field.label_data:
            print("...Field '{}' has label '{}' with a confidence score of {}".format(
                name,
                field.label_data.text,
                field.confidence
            ))
    print("-----------------------------------")
```

## <a name="manage-your-custom-models"></a>Hantera dina anpassade modeller

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrol lera antalet modeller i resurs kontot för FormRecognizer

Följande kod block kontrollerar hur många modeller som du har sparat i ditt formulärs igenkännings konto och jämför dem med konto gränsen.

```python
# First, we see how many custom models we have, and what our limit is
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista de modeller som för närvarande lagras i resurs kontot

Följande kodblock visar de aktuella modellerna i ditt konto och skriver ut information till-konsolen. Den sparar också en referens till den första modellen.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_custom_models()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="get-a-specific-model-using-the-models-id"></a>Hämta en speciell modell med modellens ID

Följande kod block använder det modell-ID som sparades från föregående avsnitt och använder det för att hämta information om modellen.

```python
# Now we'll get the first custom model in the paged list
custom_model = form_training_client.get_custom_model(model_id=first_model.model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Created on: {}".format(custom_model.requested_on))
print("Last modified: {}".format(custom_model.completed_on))
```

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurs kontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Den här koden tar bort modellen som används i föregående avsnitt.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

# Confirm deletion:
try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
}
```

## <a name="run-the-application"></a>Köra appen

Kör programmet med `python` kommandot på snabb starts filen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

### <a name="general"></a>Allmänt

Formulär identifierarens klient bibliotek kommer att generera undantag som definierats i [Azure Core](https://aka.ms/azsdk-python-azure-core).

## <a name="logging"></a>Loggning

I det här biblioteket används [standard loggnings biblioteket](https://docs.python.org/3/library/logging.html) för loggning. Grundläggande information om HTTP-sessioner (URL: er, rubriker och så vidare) är inloggad på informations nivå.

Detaljerad loggning av fel SÖKNINGs nivå, inklusive instans begär Anden/svar och inte redigerade huvuden, kan aktive ras på en klient med `logging_enable` nyckelords argumentet:

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

På samma sätt `logging_enable` kan utförlig loggning aktive ras för en enskild åtgärd, även om den inte är aktive rad för klienten:

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du använt formulär tolkens python-klient bibliotek för att träna modeller och analysera formulär på olika sätt. Nu ska du lära dig hur du skapar en bättre tränings data uppsättning och ger mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempel koden från den här guiden (och mer) finns på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).
