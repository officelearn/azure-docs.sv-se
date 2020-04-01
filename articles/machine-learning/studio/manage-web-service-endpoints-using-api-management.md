---
title: Hantera webbtjänster med API Management
titleSuffix: ML Studio (classic) - Azure
description: En guide som visar hur du hanterar AzureML-webbtjänster med API Management. Hantera REST API-slutpunkter genom att definiera användaråtkomst, användningsbegränsning och övervakning av instrumentpanelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 7064101c21c11b48d8616dbeaa2fd9075660fd3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473470"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Hantera Azure Machine Learning Studio (klassiska) webbtjänster med HJÄLP AV API Management

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du snabbt kommer igång med API Management för att hantera dina Azure Machine Learning Studio (klassiska) webbtjänster.

## <a name="what-is-azure-api-management"></a>Vad är Azure API Management?
Azure API Management är en Azure-tjänst som låter dig hantera dina REST API-slutpunkter genom att definiera användaråtkomst, användningsbegränsning och instrumentpanelsövervakning. Mer information finns på [azure API-hanteringsplatsen.](https://azure.microsoft.com/services/api-management/) Information om hur du kommer igång med Azure API Management finns [i import- och publiceringsguiden](/azure/api-management/import-and-publish). Den här andra guiden, som den här guiden baseras på, innehåller fler avsnitt, inklusive meddelandekonfigurationer, nivåprissättning, svarshantering, användarautentisering, skapa produkter, utvecklarprenumerationer och instrumentpaneler för användning.

## <a name="prerequisites"></a>Krav
För att kunna slutföra den här guiden behöver du:

* Ett Azure-konto.
* Ett AzureML-konto.
* Arbetsytan, tjänsten och api_key för ett AzureML-experiment som distribueras som en webbtjänst. Mer information om hur du skapar ett AzureML-experiment finns i [snabbstarten i Studio](create-experiment.md). Information om hur du distribuerar ett Studio-experiment (klassiskt) som en webbtjänst finns i hur du distribuerar ett [AzureML-experiment](deploy-a-machine-learning-web-service.md) som en webbtjänst. Alternativt har bilaga A instruktioner för hur du skapar och testar ett enkelt AzureML-experiment och distribuerar det som en webbtjänst.

## <a name="create-an-api-management-instance"></a>Skapa en API Management-instans

Du kan hantera din Azure Machine Learning-webbtjänst med en API Management-instans.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **+ Skapa en resurs**.
3. Skriv "API-hantering" i sökrutan och välj sedan resursen "API management".
4. Klicka på **Skapa**.
5. **Värdet Namn** används för att skapa en unik WEBBADRESS (i det här exemplet används "demoazureml").
6. Välj en **prenumeration,** **resursgrupp**och **plats** för tjänstinstansen.
7. Ange ett värde för **organisationsnamn** (i det här exemplet används "demoazureml").
8. Ange **administratörsmeddelandet** – det här e-postmeddelandet används för meddelanden från API Management-systemet.
9. Klicka på **Skapa**.

Det kan ta upp till 30 minuter innan en ny tjänst skapas.

![skapa-tjänst](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Skapa API:et
När tjänstinstansen har skapats är nästa steg att skapa API:et. Ett API består av en uppsättning åtgärder som kan anropas från ett klientprogram. API-åtgärder körs via en proxy till befintliga webbtjänster. Den här guiden skapar API:er som proxy till befintliga AzureML RRS- och BES-webbtjänster.

Så här skapar du API:et:

1. Öppna tjänstinstansen som du skapade i Azure-portalen.
2. Välj **API:er**i det vänstra navigeringsfönstret .

   ![api-management-meny](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klicka på **Lägg till API**.
2. Ange ett **webb-API-namn** (i det här exemplet används "AzureML Demo API").
3. För **webbadress till webbtjänsten**anger du "`https://ussouthcentral.services.azureml.net`".
4. Ange ett **url-suffix för webb-API". Detta blir den sista delen av webbadressen som kunderna använder för att skicka begäranden till tjänstinstansen (i det här exemplet används "azureml-demo").
5. För **URL-schema för webb-API**väljer du **HTTPS**.
6. För **produkter**väljer du **Starter**.
7. Klicka på **Spara**.


## <a name="add-the-operations"></a>Lägg till åtgärderna

Åtgärder läggs till och konfigureras till ett API i utgivarportalen. Om du vill komma åt utgivarportalen klickar du på **Publisher-portalen** i Azure-portalen för din API Management-tjänst, väljer **API:er**, **Åtgärder**och klickar sedan på **Lägg till åtgärd**.

![tillägg](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Det **nya åtgärdsfönstret** visas och fliken **Signatur** väljs som standard.

## <a name="add-rrs-operation"></a>Lägg till RRS-åtgärd
Skapa först en åtgärd för AzureML RRS-tjänsten:

1. För **HTTP-verbet**väljer du **POST**.
2. Skriv "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`" för **URL-mallen**.
3. Ange ett **visningsnamn** (i det här exemplet används "RRS Execute").

   ![add-rrs-operation-signatur](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klicka på **Svar** > **LÄGG till** till vänster och välj **200 OK**.
5. Klicka på **Spara** om du vill spara åtgärden.

   ![add-rrs-operation-response](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Lägg till BES-åtgärder

> [!NOTE]
> Skärmdumpar ingår inte här för BES-åtgärderna eftersom de är mycket lika dem för att lägga till RRS-åtgärden.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Skicka (men inte starta) ett batchkörningsjobb

1. Klicka på Lägg till **åtgärd** om du vill lägga till en BES-åtgärd i API:et.
2. För **HTTP-verbet**väljer du **POST**.
3. Skriv "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`" för **URL-mallen**.
4. Ange ett **visningsnamn** (i det här exemplet används "BES Submit").
5. Klicka på **Svar** > **LÄGG till** till vänster och välj **200 OK**.
6. Klicka på **Spara**.

### <a name="start-a-batch-execution-job"></a>Starta ett batchkörningsjobb

1. Klicka på Lägg till **åtgärd** om du vill lägga till en BES-åtgärd i API:et.
2. För **HTTP-verbet**väljer du **POST**.
3. För **HTTP-verbet**`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`skriver du " ".
4. Ange ett **visningsnamn** (i det här exemplet används "BES Start").
6. Klicka på **Svar** > **LÄGG till** till vänster och välj **200 OK**.
7. Klicka på **Spara**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Hämta status eller resultat för ett batchkörningsjobb

1. Klicka på Lägg till **åtgärd** om du vill lägga till en BES-åtgärd i API:et.
2. För **HTTP-verbet**väljer du **GET**.
3. Skriv "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`" för **URL-mallen**.
4. Ange ett **visningsnamn** (i det här exemplet används "BES-status").
6. Klicka på **Svar** > **LÄGG till** till vänster och välj **200 OK**.
7. Klicka på **Spara**.

### <a name="delete-a-batch-execution-job"></a>Ta bort ett batchkörningsjobb

1. Klicka på Lägg till **åtgärd** om du vill lägga till en BES-åtgärd i API:et.
2. För **HTTP-verbet**väljer du **TA BORT**.
3. Skriv "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`" för **URL-mallen**.
4. Ange ett **visningsnamn** (i det här exemplet används "BES Delete").
5. Klicka på **Svar** > **LÄGG till** till vänster och välj **200 OK**.
6. Klicka på **Spara**.

## <a name="call-an-operation-from-the-developer-portal"></a>Anropa en åtgärd från Developer-portalen

Åtgärder kan anropas direkt från utvecklarportalen, vilket ger ett bekvämt sätt att visa och testa driften av ett API. I det här steget anropar du **RRS Execute-metoden** som har lagts till i **AzureML Demo API**. 

1. Klicka på **utvecklarportalen**.

   ![utvecklarportal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klicka på **API:er** från den övre menyn och klicka sedan på **AzureML Demo API** för att se vilka åtgärder som är tillgängliga.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Välj **RRS Execute** för åtgärden. Klicka på **Prova det**.

   ![try-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. För **parametrar för begäran**skriver du **arbetsytan** och **tjänsten**, skriver "2.0 för **apiversion**och "true" för **mer information**. Du hittar din **arbetsyta** och **tjänst** i instrumentpanelen för AzureML-webbtjänsten (se **Testa webbtjänsten** i tillägg A).

   För **begär rubriker**klickar du på Lägg till **sidhuvud** och skriver "Content-Type" och "application/json". Klicka på **Lägg till sidhuvud** igen och skriv "Auktorisering" och "Bärare * \<av tjänsten\>API-KEY".* Du hittar din API-KEY i instrumentpanelen för AzureML-webbtjänsten (se **Testa webbtjänsten** i tillägg A).

   För **begäran**, `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`skriv .

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klicka på **Skicka**.

   ![skicka](./media/manage-web-service-endpoints-using-api-management/send.png)

När en åtgärd har anropats visar utvecklarportalen den **begärda URL:en** från backend-tjänsten, **svarsstatusen,** **svarsrubrikerna**och **eventuella svarsinnehåll**.

![svarsstatus](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Tillägg A - Skapa och testa en enkel AzureML-webbtjänst
### <a name="creating-the-experiment"></a>Skapa experimentet
Nedan följer stegen för att skapa ett enkelt AzureML-experiment och distribuera det som en webbtjänst. Webbtjänsten tar som indata en kolumn med godtycklig text och returnerar en uppsättning funktioner som representeras som heltal. Ett exempel:

| Text | Hashad text |
| --- | --- |
| Detta är en bra dag |1 1 2 2 0 2 0 1 |

Börja med att använda valfri webbläsare [https://studio.azureml.net/](https://studio.azureml.net/) navigerar du till: och anger dina autentiseringsuppgifter för att logga in. Skapa sedan ett nytt tomt experiment.

![sök-experimentmallar](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Byt namn på den till **SimpleFeatureHashingExperiment**. Expandera **sparade datauppsättningar** och dra **bokrecensioner från Amazon** till experimentet.

![enkel-funktion-hash-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expandera **dataomvandling** och **växling** och dra **Markera kolumner i datauppsättning** till experimentet. Anslut **bokrecensioner från Amazon** för att välja kolumner i **datauppsättningen**.

![Ansluta datauppsättningsmodulen för bokrecensioner till en projektkolumnermodul](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klicka på **Markera kolumner i datauppsättningen** och klicka sedan på **Starta kolumnväljare** och välj **Col2**. Klicka på bocken om du vill tillämpa ändringarna.

![Markera kolumner med kolumnnamn](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expandera **textanalys** och dra **funktions hashing** till experimentet. Anslut välj kolumner i datauppsättning **till funktionshage**. **Select Columns in Dataset**

![ansluta projekt-kolumner](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typ **3** för **hash-bitstorleken**. Då skapas 8 (23) kolumner.

![hash-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Nu kanske du vill klicka på **Kör** för att testa experimentet.

![Köra](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Skapa en webbtjänst
Nu skapa en webbtjänst. Expandera **webbtjänsten** och dra **Indata** till experimentet. Anslut **indata** till **funktionshage**. Dra även **utdata** till experimentet. Anslut **utdata** till **funktionshage**.

![utgång till funktion-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klicka på **Publicera webbtjänst**.

![publicera-webb-tjänst](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klicka på **Ja** om du vill publicera experimentet.

![ja-till-publicera](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testa webbtjänsten
En AzureML-webbtjänst består av slutpunkter för RSS-meddelanden (begäran/svar) och BES (batchexekveringstjänst). RSS är för synkron körning. BES är för asynkron jobbkörning. Om du vill testa webbtjänsten med exempel på Python-källan nedan kan du behöva hämta och installera Azure SDK för Python (se: [Så här installerar](/azure/developer/python/azure-sdk-install)du Python ).

Du behöver också **arbetsytan,** **tjänsten**och **api_key** för experimentet för exempelkällan nedan. Du hittar arbetsytan och tjänsten genom att klicka på **Request/Response** eller **Batch Execution** för experimentet på instrumentpanelen för webbtjänsten.

![sök-arbetsyta och service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Du hittar **api_key** genom att klicka på experimentet i webbtjänstens instrumentpanel.

![hitta-api-nyckel](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Testa RRS-ändpunkt
##### <a name="test-button"></a>Knappen Testa
Ett enkelt sätt att testa RRS-slutpunkten är att klicka på **Testa** på webbtjänstens instrumentpanel.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **Detta är en bra dag** för **col2**. Klicka på bocken.

![ange-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Du kommer att se något i stil med

![exempelutdata](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Exempelkod
Ett annat sätt att testa din RRS är från din klientkod. Om du klickar på **Begär/svar** på instrumentpanelen och bläddrar längst ned visas exempelkoden för C#, Python och R. Syntaxen för RRS-begäran, inklusive URI, rubriker och brödtext.

Den här guiden visar ett fungerande Python-exempel. Du måste ändra den med **arbetsytan,** **tjänsten**och **api_key** för experimentet.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
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

#### <a name="test-bes-endpoint"></a>Test BES-slutpunkt
Klicka på **Batchkörning** på instrumentpanelen och rulla längst ned. Du kommer att se exempelkod för C#, Python och R. Syntaxen för BES-begäranden om att skicka ett jobb, starta ett jobb, få status eller resultat för ett jobb och ta bort ett jobb.

Den här guiden visar ett fungerande Python-exempel. Du måste ändra den med **arbetsytan,** **tjänsten**och **api_key** för experimentet. Dessutom måste du ändra **lagringskontonamnet,** **lagringskontonyckeln**och **lagringsbehållarens namn**. Slutligen måste du ändra platsen för **indatafilen** och platsen för **utdatafilen**.

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
