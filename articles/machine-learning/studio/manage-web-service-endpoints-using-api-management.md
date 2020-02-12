---
title: Hantera webb tjänster med API Management
titleSuffix: ML Studio (classic) - Azure
description: En guide som visar hur du hanterar AzureML-webbtjänster som använder API Management. Hantera din REST API-slutpunkter genom att definiera användaråtkomst, begränsning av nätverksbandbredd och instrumentpanelen för övervakning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 9123e1d8e63382c6b89c86f99935dd288bb1bf16
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153425"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Hantera Azure Machine Learning Studio (klassiska) webb tjänster med API Management
## <a name="overview"></a>Översikt
Den här guiden visar hur du snabbt kommer igång med API Management för att hantera dina Azure Machine Learning Studio (klassiska) webb tjänster.

## <a name="what-is-azure-api-management"></a>Vad är Azure API Management?
Azure API Management är en Azure-tjänst som låter dig hantera REST API-slutpunkter genom att definiera användaråtkomst, begränsning av nätverksbandbredd och instrumentpanelen för övervakning. Mer information finns på [webbplatsen för Azure API Management](https://azure.microsoft.com/services/api-management/) . Information om hur du kommer igång med Azure API Management finns i [import-och publicerings guiden](/azure/api-management/import-and-publish). Den här andra guiden som den här guiden är baserad på omfattar flera ämnen, t.ex. meddelande-konfigurationer, priserna för nivån, hantering av svar, autentisering av användare, skapa produkter, developer prenumerationer och användning dashboarding.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:

* Ett Azure-konto.
* Ett AzureML-konto.
* Arbetsytan, service och api_key för en AzureML-experiment som distribueras som en webbtjänst. Mer information om hur du skapar ett AzureML-experiment finns i [snabb start för Studio](create-experiment.md). Information om hur du distribuerar ett Studio-experiment (klassiskt) som en webb tjänst finns i avsnittet om hur du distribuerar ett AzureML experiment som en webb tjänst i avsnittet om [distribution av Studio-distribution](deploy-a-machine-learning-web-service.md) . Bilaga A har också instruktioner för hur du skapar och testar ett enkelt AzureML-experiment och distribuera den som en webbtjänst.

## <a name="create-an-api-management-instance"></a>Skapa en API Management-instans

Du kan hantera din Azure Machine Learning-webbtjänst med en API Management-instans.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **+ Skapa en resurs**.
3. Skriv ”API management” i sökrutan och välj ”API management”-resursen.
4. Klicka på **Skapa**.
5. **Name** -värdet kommer att användas för att skapa en unik URL (det här exemplet använder "demoazureml").
6. Välj en **prenumeration**, en **resurs grupp**och en **plats** för tjänst instansen.
7. Ange ett värde för **organisations namn** (det här exemplet använder "demoazureml").
8. Ange **administratörens e-postadress** – det här e-postmeddelandet kommer att användas för meddelanden från API Management systemet.
9. Klicka på **Skapa**.

Det kan ta upp till 30 minuter för en ny tjänst skapas.

![Skapa tjänst](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Skapa API: et
När tjänstinstansen har skapats är nästa steg att skapa API: et. Ett API består av en uppsättning åtgärder som kan anropas från ett klientprogram. API-åtgärder körs via en proxy till befintliga webbtjänster. Den här guiden skapar API: er som proxy för att de befintliga AzureML RRS och BES-webbtjänsterna.

Skapa API: et:

1. Öppna tjänst instansen som du skapade i Azure Portal.
2. Välj **API: er**i det vänstra navigerings fönstret.

   ![API-hantering-menyn](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klicka på **Lägg till API**.
2. Ange ett **webb-API-namn** (det här exemplet använder "azureml demo-API").
3. För **webb tjänstens URL**anger du`https://ussouthcentral.services.azureml.net`.
4. Ange en ** webb-API: ets webbadressuffix ”. Detta blir den sista delen av URL: en som kunder använder för att skicka förfrågningar till tjänstinstansen (det här exemplet används ”azureml-demo”).
5. För **webb-API URL-schema**väljer du **https**.
6. För **produkter**väljer du **starter**.
7. Klicka på **Save** (Spara).


## <a name="add-the-operations"></a>Lägg till åtgärder

Åtgärder läggs till och konfigurerat till ett API i publisher-portalen. Öppna utgivar portalen genom att klicka **på Publisher-Portal** i Azure Portal för din API Management-tjänst, Välj API: **er**, **åtgärder**och klicka sedan på **Lägg till åtgärd**.

![Lägg till åtgärd](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Fönstret **ny åtgärd** visas och fliken **signatur** är markerad som standard.

## <a name="add-rrs-operation"></a>Lägg till RRS-åtgärd
Skapa en åtgärd för AzureML RRS-tjänsten:

1. För **http-verbet**väljer du **post**.
2. Skriv "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`" som **URL-mall**.
3. Ange ett **visnings namn** (det här exemplet använder "resurs poster körs").

   ![Lägg till rrs-åtgärden-signaturer](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klicka på **svar** > **Lägg till** till vänster och välj **200 OK**.
5. Klicka på **Spara** för att spara åtgärden.

   ![Lägg till rrs-åtgärden-svar](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Lägga till BES-åtgärder

> [!NOTE]
> Skärmbilder ingår inte för BES-åtgärder som de är mycket likartade de för att lägga till RRS-åtgärden.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Skicka (men inte att starta) en Batch Execution-jobb

1. Klicka på **Lägg till åtgärd** för att lägga till en bes-åtgärd i API: et.
2. För **http-verbet**väljer du **post**.
3. Skriv "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`" som **URL-mall**.
4. Ange ett **visnings namn** (det här exemplet använder "bes Submit").
5. Klicka på **svar** > **Lägg till** till vänster och välj **200 OK**.
6. Klicka på **Save** (Spara).

### <a name="start-a-batch-execution-job"></a>Starta ett jobb i Batch Execution

1. Klicka på **Lägg till åtgärd** för att lägga till en bes-åtgärd i API: et.
2. För **http-verbet**väljer du **post**.
3. Skriv "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`" för **http-verbet**.
4. Ange ett **visnings namn** (det här exemplet använder "bes start").
6. Klicka på **svar** > **Lägg till** till vänster och välj **200 OK**.
7. Klicka på **Save** (Spara).

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Hämta status eller resultatet av ett jobb i Batch Execution

1. Klicka på **Lägg till åtgärd** för att lägga till en bes-åtgärd i API: et.
2. För **http-verbet**väljer du **Hämta**.
3. Skriv "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`" som **URL-mall**.
4. Ange ett **visnings namn** (det här exemplet använder "bes status").
6. Klicka på **svar** > **Lägg till** till vänster och välj **200 OK**.
7. Klicka på **Save** (Spara).

### <a name="delete-a-batch-execution-job"></a>Ta bort ett jobb i Batch Execution

1. Klicka på **Lägg till åtgärd** för att lägga till en bes-åtgärd i API: et.
2. För **http-verbet**väljer du **ta bort**.
3. Skriv "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`" som **URL-mall**.
4. Ange ett **visnings namn** (det här exemplet använder "bes Delete").
5. Klicka på **svar** > **Lägg till** till vänster och välj **200 OK**.
6. Klicka på **Save** (Spara).

## <a name="call-an-operation-from-the-developer-portal"></a>Anropa en åtgärd från utvecklarportalen

Kan anropa åtgärder direkt från utvecklarportalen, vilket är ett enkelt sätt att visa och testa åtgärderna i ett API. I det här steget ska du anropa metoden **RR-körning** som har lagts till i **AZUREML demo-API**. 

1. Klicka på **Developer Portal**.

   ![Developer-portalen](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klicka på **API: er** på den översta menyn och klicka sedan på **AZUREML demo-API** för att se vilka åtgärder som är tillgängliga.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Välj **resurs resurs körning** för åtgärden. Klicka på **prova**.

   ![Försök it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. För **Parametrar för begäran**skriver du din **arbets yta** och **tjänst**, skriver "2,0 för **API version**" och "true" för **information**. Du kan hitta **arbets ytan** och **tjänsten** i azureml-webbtjänstens instrument panel (se **testa webb tjänsten** i bilaga A).

   För **begärandehuvuden**klickar du på **Lägg till sidhuvud** och skriver "Content-Type" och "Application/JSON". Klicka på **Lägg till sidhuvud** igen och skriv "Authorization" och "bearer *\<ditt tjänst-API-nyckel\>* ". Du hittar din API-nyckel i AzureML-webbtjänstens instrument panel (se **testa webb tjänsten** i bilaga A).

   I **begär ande text**skriver du `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klicka på **Skicka**.

   ![Skicka](./media/manage-web-service-endpoints-using-api-management/send.png)

När en åtgärd har anropats visar Developer-portalen den **begärda URL:** en från backend-tjänsten, **svars status**, **svars rubriker**och allt **svars innehåll**.

![status för Webbsvar](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Bilaga A – skapa och testa en enkel AzureML webbtjänsten
### <a name="creating-the-experiment"></a>Skapa experimentet
Nedan visas stegen för att skapa ett enkelt AzureML-experiment och distribuera den som en webbtjänst. Tar för web-tjänst som indata för en kolumn med valfri text och returnerar en uppsättning funktioner som representeras som heltal. Några exempel:

| Text | Hashade Text |
| --- | --- |
| Det här är en bra dag |1 1 2 2 0 2 0 1 |

Börja med att använda en valfri webbläsare, navigera till: [https://studio.azureml.net/](https://studio.azureml.net/) och ange dina autentiseringsuppgifter för att logga in. Skapa sedan ett nytt tomt experiment.

![Sök efter-experiment-mallar](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Byt namn på den till **SimpleFeatureHashingExperiment**. Expandera **sparade data uppsättningar** och dra **bok granskningar från Amazon** till experimentet.

![enkel-funktion-hash-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expandera **data omvandling** och **manipulering** och dra **Välj kolumner i data uppsättningen** till experimentet. Anslut **bok granskningar från Amazon** för att **välja kolumner i data uppsättningen**.

![Anslut datauppsättnings modulen för bok granskningar till en modul för projekt kolumner](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klicka på **Välj kolumner i data uppsättning** och klicka sedan på **Starta kolumn väljaren** och välj **Col2**. Klicka på bockmarkeringen för att tillämpa ändringarna.

![Välj kolumner med kolumn namn](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expandera **textanalys** och dra **funktions-hashar** till experimentet. Anslut **Välj kolumner i data uppsättning** till **funktionens hashing**.

![ansluta projektkolumner](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typ **3** för **hashing-bitsize**. Detta skapar 8 (23) kolumner.

![hash-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Nu kanske du vill klicka på **Kör** för att testa experimentet.

![Kör](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Skapa en webbtjänst
Nu ska du skapa en webbtjänst. Expandera **webb tjänsten** och dra **inmatade inmatade** till experimentet. Anslut **Indatamängden** till **funktionens hashing**. Dra även **utdata** till experimentet. Anslut **utdata** till **funktionens hashing**.

![utdata-till-funktionen för-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klicka på **publicera webb tjänst**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klicka på **Ja** för att publicera experimentet.

![Ja att publicera](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testa webbtjänsten
En AzureML-webbtjänst består av skalning på Mottagarsidan (begäran/svar-service) och BES (batch execution service) slutpunkter. RSS är för synkron körning. BES är för asynkron jobbkörning. Om du vill testa din webb tjänst med exempel på python-källan nedan kan du behöva ladda ned och installera Azure SDK för python (se: [så här installerar du python](/azure/python/python-sdk-azure-install)).

Du kommer också att behöva **arbets ytan**, **tjänsten**och **api_key** av ditt experiment för exempel källan nedan. Du kan hitta arbets ytan och tjänsten genom att klicka på **begär ande/svar** eller **batch-körning** för ditt experiment på webb tjänstens instrument panel.

![Sök-arbetsyta-och-tjänst](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Du kan hitta **api_key** genom att klicka på experimentet på instrument panelen för webb tjänster.

![Sök-api-nyckel](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Testa RRS-slutpunkt
##### <a name="test-button"></a>Knappen Testa
Ett enkelt sätt att testa resurs slut punkten är att klicka på **testa** på instrument panelen för webb tjänsten.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Skriv **det här är en lämplig dag** för **col2**. Klicka på bockmarkeringen.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Du ser något som liknar

![exempel på-utdata](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Exempelkod
Ett annat sätt att testa din RRS är från klientkoden. Om du klickar på **begär/svara** på instrument panelen och rullar till slutet visas exempel kod för C#, python och R. Du kommer också att se syntaxen för begäran om resurs poster, inklusive URI, rubriker och brödtext för begäran.

Den här guiden visar en fungerande Python-exemplet. Du måste ändra den med **arbets ytan**, **tjänsten**och **api_key** av experimentet.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>Testa BES-slutpunkt
Klicka på **batch-körning** på instrument panelen och rulla längst ned. Exempelkod för visas C#, Python och R. Du kan även se syntaxen för BES-begäranden för att skicka ett jobb startar ett jobb, få status eller resultat för ett jobb och ta bort ett jobb.

Den här guiden visar en fungerande Python-exemplet. Du måste ändra den med **arbets ytan**, **tjänsten**och **api_key** av experimentet. Dessutom måste du ändra **lagrings kontots namn**, **lagrings konto nyckel**och **namn på lagrings behållare**. Slutligen måste du ändra platsen för **indatafilen** och platsen för **utdatafilen**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "wb+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the following code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
