---
title: Lär dig att hantera AzureML-webbtjänster med hjälp av API-hantering | Microsoft Docs
description: En guide som visar hur du hanterar AzureML-webbtjänster som använder API-hantering.
keywords: Machine learning api-hantering
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 4ca551ed07447e41ec94b0334eac0d235e0a5b6f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835091"
---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Lär dig hur du hanterar AzureML-webbtjänster som använder API Management
## <a name="overview"></a>Översikt
Den här guiden visar hur du Kom snabbt igång med API-hantering för att hantera AzureML-webbtjänster.

## <a name="what-is-azure-api-management"></a>Vad är Azure API Management?
Azure API Management är en Azure-tjänst som låter dig hantera REST API-slutpunkter genom att definiera användaråtkomst, begränsning och instrumentpanelen för övervakning. Klicka på [här](https://azure.microsoft.com/services/api-management/) information om Azure API Management. Klicka på [här](../../api-management/api-management-get-started.md) en guide om hur du kommer igång med Azure API Management. Den här andra guiden som den här guiden bygger på omfattar flera ämnen, t.ex. meddelande konfigurationer, nivå prissättning, svar hantering, autentisering av användare, skapa produkter, developer prenumerationer och användning dashboarding.

## <a name="what-is-azureml"></a>Vad är AzureML?
AzureML är en Azure-tjänst för maskininlärning där du kan enkelt skapa, distribuera och dela avancerade Analyslösningar. Klicka på [här](https://azure.microsoft.com/services/machine-learning/) information om AzureML.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:

* Ett Azure-konto. Om du inte har ett Azure-konto, klickar du på [här](https://azure.microsoft.com/pricing/free-trial/) mer information om hur du skapar ett kostnadsfritt utvärderingskonto.
* AzureML-konto. Om du inte har en AzureML-konto, klickar du på [här](https://studio.azureml.net/) mer information om hur du skapar ett kostnadsfritt utvärderingskonto.
* Arbetsytan, tjänst och api_key för en AzureML-experiment som distribueras som en webbtjänst. Klicka på [här](create-experiment.md) mer information om hur du skapar ett AzureML-experiment. Klicka på [här](publish-a-machine-learning-web-service.md) för information om hur du distribuerar en AzureML experiment som en webbtjänst. Bilaga A har också anvisningar för hur du skapar och testar ett enkelt experiment AzureML och distribuera det som en webbtjänst.

## <a name="create-an-api-management-instance"></a>Skapa en API Management-instans

Du kan hantera dina Azure Machine Learning-webbtjänst med en API Management-instans.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **+ Skapa en resurs**.
3. I sökrutan skriver du ”API management”, och välj sedan ”API management”-resursen.
4. Klicka på **Skapa**.
5. Den **namn** värde används för att skapa en unik URL (det här exemplet använder ”demoazureml”).
6. Välj en **prenumeration**, **resursgruppen**, och **plats** för service-instans.
7. Ange ett värde för **organisationsnamn** (det här exemplet använder ”demoazureml”).
8. Ange din **administratör e-post** -e-postmeddelandet ska användas för meddelanden från API Management-systemet.
9. Klicka på **Skapa**.

Det kan ta upp till 30 minuter för en ny tjänst skapas.

![Skapa tjänst](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Skapa API: et
När instansen för tjänsten har skapats, är nästa steg att skapa API: et. Ett API består av en uppsättning åtgärder som kan anropas från ett klientprogram. API-åtgärder körs via en proxy till befintliga webbtjänster. Den här guiden skapar API: er proxyinställningarna så att de befintliga AzureML RRS och BES-webbtjänsterna.

Skapa API: et:

1. Öppna service-instans som du skapade i Azure-portalen.
2. I det vänstra navigeringsfönstret väljer **API: er**.

   ![management-API-menyn](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klicka på **lägga till API**.
2. Ange en **Web API-namnet** (det här exemplet använder ”AzureML Demo-API”).
3. För **-webbtjänstens URL**, ange ”`https://ussouthcentral.services.azureml.net`”.
4. Ange en ** URL för Web API-suffixet ”. Detta blir den sista delen av den URL som kunder använder för att skicka begäranden till instansen för tjänsten (i det här exemplet används ”azureml-demo”).
5. För **Web API-URL-schema**väljer **HTTPS**.
6. För **produkter**väljer **Starter**.
7. Klicka på **Spara**.


## <a name="add-the-operations"></a>Lägg till åtgärder

Åtgärder läggs till och konfigurerats för att en API i portalen utgivare. Publisher-portalen, klicka på **Publisher portal** i Azure-portalen för API Management-tjänsten väljer **API: er**, **Operations**, klicka på **Lägga till åtgärden**.

![lägga till åtgärden](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Den **nya åtgärden** visas och **signatur** fliken väljs som standard.

## <a name="add-rrs-operation"></a>Lägg till RR-åtgärd
Först skapa en åtgärd för AzureML RRS-tjänsten:

1. För den **HTTP-verbet**väljer **efter**.
2. För den **URL mallen**, typ ”`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`”.
3. Ange en **visningsnamn** (det här exemplet använder ”Resursposter köra”).

   ![Lägg till RR-åtgärden-signaturer](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
5. Klicka på **spara** att spara den här åtgärden.

   ![Lägg till-RR-åtgärden-svar](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Lägga till BES-åtgärder

> [!NOTE]
> Skärmbilder ingår inte för BES-åtgärder som de är mycket lik dem för att lägga till åtgärden Resursposter.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Skicka (men inte startar) ett Batch Execution-jobb

1. Klicka på **lägga till åtgärden** att lägga till en åtgärd för BES-API: et.
2. För den **HTTP-verbet**väljer **efter**.
3. För den **URL mallen**, typ ”`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`”.
4. Ange en **visningsnamn** (det här exemplet använder ”BES skicka”).
5. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
6. Klicka på **Spara**.

### <a name="start-a-batch-execution-job"></a>Starta ett jobb i Batch Execution

1. Klicka på **lägga till åtgärden** att lägga till en åtgärd för BES-API: et.
2. För den **HTTP-verbet**väljer **efter**.
3. För den **HTTP-verbet**, typ ”`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`”.
4. Ange en **visningsnamn** (det här exemplet använder ”BES Start”).
6. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
7. Klicka på **Spara**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Hämta status eller resultatet av ett Batch Execution-jobb

1. Klicka på **lägga till åtgärden** att lägga till en åtgärd för BES-API: et.
2. För den **HTTP-verbet**väljer **hämta**.
3. För den **URL mallen**, typ ”`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`”.
4. Ange en **visningsnamn** (det här exemplet använder ”BES Status”).
6. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
7. Klicka på **Spara**.

### <a name="delete-a-batch-execution-job"></a>Ta bort en Batch Execution-jobb

1. Klicka på **lägga till åtgärden** att lägga till en åtgärd för BES-API: et.
2. För den **HTTP-verbet**väljer **ta bort**.
3. För den **URL mallen**, typ ”`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`”.
4. Ange en **visningsnamn** (det här exemplet använder ”BES bort”).
5. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
6. Klicka på **Spara**.

## <a name="call-an-operation-from-the-developer-portal"></a>Anropa en åtgärd från Developer-portalen

Åtgärder kan anropas direkt från Developer-portalen, vilket ger ett bekvämt sätt att visa och testa driften av ett API. I det här steget kommer du anropar den **Resursposter köra** metod som har lagts till i den **AzureML Demo API**. 

1. Klicka på **utvecklarportalen**.

   ![Developer-portalen](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klicka på **API: er** från den översta menyn och klicka sedan på **AzureML Demo API** att se åtgärderna som är tillgängliga.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Välj **Resursposter köra** för åtgärden. Klicka på **prova**.

   ![Försök it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. För **parametrar för begäran**, Skriv din **arbetsytan** och **service**, typ ”2.0 för den **apiversion**, och” true ”för **information**. Du hittar din **arbetsytan** och **service** i instrumentpanelen för AzureML web service (se **testa webbtjänsten** i bilaga A).

   För **Begäransrubriker**, klickar du på **Lägg till sidhuvud** och Skriv ”Content-Type” och ”application/json”. Klicka på **Lägg till sidhuvud** igen och Skriv ”tillstånd” och ”ägar  *\<tjänsten API-nyckel\>*”. Du kan hitta din API-nyckel i instrumentpanelen för AzureML web service (se **testa webbtjänsten** i bilaga A).

   För **Begärandetext**, typen `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klicka på **skicka**.

   ![Skicka](./media/manage-web-service-endpoints-using-api-management/send.png)

När en åtgärd har anropats developer-portalen visar den **begärda URL: en** från backend-tjänsten, den **svarsstatusen**, **svarshuvuden**, och en **svar innehåll**.

![Response-status](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Bilaga A – skapa och testa en enkel AzureML webbtjänsten
### <a name="creating-the-experiment"></a>Skapa experimentet
Nedan följer du stegen för att skapa ett enkelt experiment AzureML och distribuera den som en webbtjänst. Tar för web-tjänst som indata för en kolumn med valfri text och returnerar en uppsättning funktioner som visas i form av heltal. Exempel:

| Text | Hashformaterats Text |
| --- | --- |
| Det här är en bra dag |1 1 2 2 0 2 0 1 |

Börja med en webbläsare, navigera till: [ https://studio.azureml.net/ ](https://studio.azureml.net/) och ange dina autentiseringsuppgifter för att logga in. Skapa sedan ett nytt tomt experiment.

![Sök-experiment-mallar](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Byt namn på den till **SimpleFeatureHashingExperiment**. Expandera **sparade datauppsättningar** och dra **Book granskningar från Amazon** på experimentet.

![enkel-funktionen-hash-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expandera **Dataomvandling** och **manipulering** och dra **Välj kolumner i datauppsättning** på experimentet. Ansluta **bok granskningar från Amazon** till **Välj kolumner i datauppsättning**.

![select-columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klicka på **Välj kolumner i datauppsättning** och klicka sedan på **starta kolumnväljaren** och välj **Col2**. Klicka på bockmarkeringen för att tillämpa ändringarna.

![select-columns](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expandera **textanalys** och dra **funktions-hashning** på experimentet. Ansluta **Välj kolumner i datauppsättning** till **hash-funktionen**.

![ansluta projektkolumner](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typen **3** för den **Hashing bitsize**. Detta skapar 8 (23) kolumner.

![hash-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Du kan nu vill klickar du på **kör** att testa experimentet.

![Kör](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Skapa en webbtjänst
Nu skapa en webbtjänst. Expandera **webbtjänsten** och dra **indata** på experimentet. Ansluta **indata** till **hash-funktionen**. Också dra **utdata** på experimentet. Ansluta **utdata** till **hash-funktionen**.

![utdata-till-funktionen för-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klicka på **publicerar webbtjänst**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klicka på **Ja** att publicera experimentet.

![Ja för att publicera](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testa webbtjänsten
En AzureML-webbtjänst består av RSS-(begäranden/svar-tjänsten) och slutpunkter för BES (batch execution service). RSS är för synkron körning. BES är asynkron jobbkörningen. Om du vill testa webbtjänsten med exempel Python källan nedan, du kan behöva hämta och installera Azure SDK för Python (se: [hur du installerar Python](../../python-how-to-install.md)).

Du måste också den **arbetsytan**, **service**, och **api_key** av experimentet för källan exemplet nedan. Du kan hitta arbetsytan och tjänsten genom att klicka på antingen **frågor och svar** eller **Batch Execution** för experimentet web service-instrumentpanelen.

![Sök-arbetsytan-och-tjänst](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Du hittar den **api_key** genom att klicka på experimentet web service-instrumentpanelen.

![Sök-api-nyckel](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Testa Resursposter slutpunkt
##### <a name="test-button"></a>Knappen Testa
Ett enkelt sätt att testa RR-slutpunkten är att klicka på **testa** på instrumentpanelen web service.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typen **detta är en bra dag** för **col2**. Klicka på bockmarkeringen.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Du ser något som liknar

![exempel på-utdata](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Exempelkod
Ett annat sätt att testa din RRS är från din klientkod. Om du klickar på **frågor och svar** på instrumentpanelen och rullar du längst ned visas exempelkod för C#, Python och R. Du kan även se syntaxen för RRS-förfrågan, inklusive URI, rubriker och brödtext.

Den här guiden visar en fungerande Python exempel. Du behöver ändra den med den **arbetsytan**, **service**, och **api_key** av experimentet.

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

#### <a name="test-bes-endpoint"></a>Testa BES slutpunkt
Klicka på **Batch-körningen** på instrumentpanelen och rullar du längst ned. Du kommer att se exempelkod för C#, Python och R. Du kan även se syntaxen för BES-förfrågningar till skickar ett jobb, starta ett jobb, status eller resultatet av ett jobb och ta bort ett jobb.

Den här guiden visar en fungerande Python exempel. Du behöver ändra den med den **arbetsytan**, **service**, och **api_key** av experimentet. Dessutom måste du ändra den **lagringskontonamnet**, **lagringskontonyckel**, och **namnet på lagringsbehållaren**. Till sist ska du behöver ändra platsen för den **indatafilen** och platsen för den **utdatafilen**.

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
    with open(output_file, "w+") as f:
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
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
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
