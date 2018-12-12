---
title: Hantera Machine Learning Studio-webbtjänster som använder API Management – Azure | Microsoft Docs
description: En guide som visar hur du hanterar AzureML-webbtjänster som använder API Management. Hantera din REST API-slutpunkter genom att definiera användaråtkomst, begränsning av nätverksbandbredd och instrumentpanelen för övervakning.
keywords: Machine learning api management
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
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
ms.openlocfilehash: 76898d2b55d5187e12b91dbd6c9a85af6a274726
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091721"
---
# <a name="manage-azure-machine-learning-studio-web-services-using-api-management"></a>Hantera Azure Machine Learning Studio-webbtjänster som använder API Management
## <a name="overview"></a>Översikt
Den här guiden visar hur du snabbt kommer igång med att använda API Management för att hantera dina Azure Machine Learning Studio-webbtjänster.

## <a name="what-is-azure-api-management"></a>Vad är Azure API Management?
Azure API Management är en Azure-tjänst som låter dig hantera REST API-slutpunkter genom att definiera användaråtkomst, begränsning av nätverksbandbredd och instrumentpanelen för övervakning. Klicka på [här](https://azure.microsoft.com/services/api-management/) för information om Azure API Management. Klicka på [här](../../api-management/api-management-get-started.md) en guide om hur du kommer igång med Azure API Management. Den här andra guiden som den här guiden är baserad på omfattar flera ämnen, t.ex. meddelande-konfigurationer, priserna för nivån, hantering av svar, autentisering av användare, skapa produkter, developer prenumerationer och användning dashboarding.

## <a name="what-is-azureml"></a>Vad är AzureML?
AzureML är en Azure-tjänst för maskininlärning som hjälper dig att enkelt bygga, distribuera och dela avancerade Analyslösningar. Klicka på [här](https://azure.microsoft.com/services/machine-learning/) mer information om AzureML.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:

* Ett Azure-konto. Om du inte har ett Azure-konto, klickar du på [här](https://azure.microsoft.com/pricing/free-trial/) mer information om hur du skapar ett kostnadsfritt konto.
* Ett AzureML-konto. Om du inte har en AzureML-konto, klickar du på [här](https://studio.azureml.net/) mer information om hur du skapar ett kostnadsfritt konto.
* Arbetsytan, service och api_key för en AzureML-experiment som distribueras som en webbtjänst. Klicka på [här](create-experiment.md) mer information om hur du skapar ett AzureML-experiment. Klicka på [här](publish-a-machine-learning-web-service.md) för information om hur du distribuerar en AzureML experiment som en webbtjänst. Bilaga A har också instruktioner för hur du skapar och testar ett enkelt AzureML-experiment och distribuera den som en webbtjänst.

## <a name="create-an-api-management-instance"></a>Skapa en API Management-instans

Du kan hantera din Azure Machine Learning-webbtjänst med en API Management-instans.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **+ Skapa en resurs**.
3. Skriv ”API management” i sökrutan och välj ”API management”-resursen.
4. Klicka på **Skapa**.
5. Den **namn** värde som används för att skapa en unik URL (det här exemplet används ”demoazureml”).
6. Välj en **prenumeration**, **resursgrupp**, och **plats** för din tjänstinstans.
7. Ange ett värde för **organisationsnamn** (det här exemplet används ”demoazureml”).
8. Ange din **administratörens e-postadress** -e-postmeddelandet som ska användas för meddelanden från API Management-systemet.
9. Klicka på **Skapa**.

Det kan ta upp till 30 minuter för en ny tjänst skapas.

![Skapa tjänst](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Skapa API: et
När tjänstinstansen har skapats är nästa steg att skapa API: et. Ett API består av en uppsättning åtgärder som kan anropas från ett klientprogram. API-åtgärder körs via en proxy till befintliga webbtjänster. Den här guiden skapar API: er som proxy för att de befintliga AzureML RRS och BES-webbtjänsterna.

Skapa API: et:

1. Öppna den tjänstinstans som du just har skapat i Azure-portalen.
2. I det vänstra navigeringsfönstret väljer **API: er**.

   ![API-hantering-menyn](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klicka på **Lägg till API**.
2. Ange en **Web API-namn** (det här exemplet används ”AzureML Demo-API”).
3. För **-webbtjänstens URL**, ange ”`https://ussouthcentral.services.azureml.net`”.
4. Ange en ** webb-API: ets webbadressuffix ”. Detta blir den sista delen av URL: en som kunder använder för att skicka förfrågningar till tjänstinstansen (det här exemplet används ”azureml-demo”).
5. För **Web API URL-schema**väljer **HTTPS**.
6. För **produkter**väljer **Starter**.
7. Klicka på **Spara**.


## <a name="add-the-operations"></a>Lägg till åtgärder

Åtgärder läggs till och konfigurerat till ett API i publisher-portalen. Du kommer åt utgivarportalen genom att klicka på **utgivarportalen** i Azure-portalen för API Management-tjänsten väljer **API: er**, **Operations**, klicka sedan på **Lägg till åtgärden**.

![Lägg till åtgärd](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Den **nya åtgärden** visas fönstret och **signatur** fliken väljs som standard.

## <a name="add-rrs-operation"></a>Lägg till RRS-åtgärd
Skapa en åtgärd för AzureML RRS-tjänsten:

1. För den **HTTP-verbet**väljer **POST**.
2. För den **URL: en mall**, typ ”`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`”.
3. Ange en **visningsnamn** (det här exemplet används ”RRS kör”).

   ![Lägg till rrs-åtgärden-signaturer](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
5. Klicka på **spara** att spara den här åtgärden.

   ![Lägg till rrs-åtgärden-svar](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Lägga till BES-åtgärder

> [!NOTE]
> Skärmbilder ingår inte för BES-åtgärder som de är mycket likartade de för att lägga till RRS-åtgärden.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Skicka (men inte att starta) en Batch Execution-jobb

1. Klicka på **Lägg till åtgärd** att lägga till en åtgärd för BES-API: et.
2. För den **HTTP-verbet**väljer **POST**.
3. För den **URL: en mall**, typ ”`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`”.
4. Ange en **visningsnamn** (det här exemplet används ”BES skicka”).
5. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
6. Klicka på **Spara**.

### <a name="start-a-batch-execution-job"></a>Starta ett jobb i Batch Execution

1. Klicka på **Lägg till åtgärd** att lägga till en åtgärd för BES-API: et.
2. För den **HTTP-verbet**väljer **POST**.
3. För den **HTTP-verbet**, typ ”`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`”.
4. Ange en **visningsnamn** (det här exemplet används ”BES Start”).
6. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
7. Klicka på **Spara**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Hämta status eller resultatet av ett jobb i Batch Execution

1. Klicka på **Lägg till åtgärd** att lägga till en åtgärd för BES-API: et.
2. För den **HTTP-verbet**väljer **hämta**.
3. För den **URL: en mall**, typ ”`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`”.
4. Ange en **visningsnamn** (det här exemplet används ”BES Status”).
6. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
7. Klicka på **Spara**.

### <a name="delete-a-batch-execution-job"></a>Ta bort ett jobb i Batch Execution

1. Klicka på **Lägg till åtgärd** att lägga till en åtgärd för BES-API: et.
2. För den **HTTP-verbet**väljer **ta bort**.
3. För den **URL: en mall**, typ ”`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`”.
4. Ange en **visningsnamn** (det här exemplet används ”BES bort”).
5. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**.
6. Klicka på **Spara**.

## <a name="call-an-operation-from-the-developer-portal"></a>Anropa en åtgärd från utvecklarportalen

Kan anropa åtgärder direkt från utvecklarportalen, vilket är ett enkelt sätt att visa och testa åtgärderna i ett API. I det här steget ska du ringa det **RRS köra** metod som har lagts till i den **AzureML Demo API**. 

1. Klicka på **utvecklarportalen**.

   ![Developer-portalen](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klicka på **API: er** från den översta menyn och sedan på **AzureML Demo API** att se de tillgängliga åtgärderna.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Välj **RRS köra** för åtgärden. Klicka på **prova**.

   ![Försök it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. För **Begäranparametrar**, typ din **arbetsytan** och **service**, typ ”2.0 för den **apiversion**, och det är” true ”för **information**. Du kan hitta din **arbetsytan** och **service** i instrumentpanelen för webbtjänsten AzureML (se **testa webbtjänsten** i bilaga A).

   För **begärandehuvuden**, klickar du på **Lägg till sidhuvud** och Skriv ”Content-Type” och ”application/json”. Klicka på **Lägg till sidhuvud** igen och Skriv ”Authorization” och ”ägar  *\<din tjänst API-NYCKELN\>*”. Du hittar din API-nyckel i instrumentpanelen för webbtjänsten AzureML (se **testa webbtjänsten** i bilaga A).

   För **Begärandetext**, typen `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klicka på **skicka**.

   ![Skicka](./media/manage-web-service-endpoints-using-api-management/send.png)

När en åtgärd har anropats visar utvecklarportalen den **begärda URL: en** från backend-tjänsten, den **svarsstatus**, **svarshuvuden**, och alla  **Svarsinnehållet**.

![status för Webbsvar](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Bilaga A – skapa och testa en enkel AzureML webbtjänsten
### <a name="creating-the-experiment"></a>Skapa experimentet
Nedan visas stegen för att skapa ett enkelt AzureML-experiment och distribuera den som en webbtjänst. Tar för web-tjänst som indata för en kolumn med valfri text och returnerar en uppsättning funktioner som representeras som heltal. Exempel:

| Text | Hashade Text |
| --- | --- |
| Det här är en bra dag |1 1 2 2 0 2 0 1 |

Börja med en webbläsare, navigera till: [ https://studio.azureml.net/ ](https://studio.azureml.net/) och ange dina autentiseringsuppgifter för inloggning. Skapa sedan ett nytt tomt experiment.

![Sök efter-experiment-mallar](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Byt namn på den till **SimpleFeatureHashingExperiment**. Expandera **sparade datauppsättningar** och dra **boken granskningar från Amazon** till ditt experiment.

![enkel-funktion-hash-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expandera **Dataomvandling** och **manipulering av** och dra **Välj kolumner i datauppsättning** till ditt experiment. Ansluta **boka granskningar från Amazon** till **Välj kolumner i datauppsättning**.

![select-columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klicka på **Välj kolumner i datauppsättning** och klicka sedan på **starta kolumnväljaren** och välj **Col2**. Klicka på bockmarkeringen för att tillämpa ändringarna.

![select-columns](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expandera **textanalys** och dra **funktions-hashning** till experimentet. Ansluta **Välj kolumner i datauppsättning** till **funktions-hashning**.

![ansluta projektkolumner](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typ **3** för den **hash-bitsize**. Detta skapar 8 (23) kolumner.

![hash-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Nu kan du klicka på **kör** att testa experimentet.

![Kör](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Skapa en webbtjänst
Nu ska du skapa en webbtjänst. Expandera **webbtjänsten** och dra **indata** till ditt experiment. Ansluta **indata** till **funktions-hashning**. Också dra **utdata** till ditt experiment. Ansluta **utdata** till **funktions-hashning**.

![utdata-till-funktionen för-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klicka på **publicera webbtjänst**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klicka på **Ja** att publicera experimentet.

![Ja att publicera](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testa webbtjänsten
En AzureML-webbtjänst består av skalning på Mottagarsidan (begäran/svar-service) och BES (batch execution service) slutpunkter. RSS är för synkron körning. BES är för asynkron jobbkörning. Om du vill testa din webbtjänst med exemplet Python källan nedan, du kan behöva hämta och installera Azure SDK för Python (se: [så här installerar du Python](../../python-how-to-install.md)).

Du måste också den **arbetsytan**, **service**, och **api_key** av experimentet för källan exemplet nedan. Du hittar den arbetsytan och tjänsten genom att klicka på antingen **begäran/svar** eller **batchkörning** för experimentet i instrumentpanelen för webbtjänsten.

![Sök-arbetsyta-och-tjänst](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Du hittar den **api_key** genom att klicka på ditt experiment i instrumentpanelen för webbtjänsten.

![Sök-api-nyckel](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Testa RRS-slutpunkt
##### <a name="test-button"></a>Knappen Testa
Ett enkelt sätt att testa RRS-slutpunkten är att klicka på **testa** på instrumentpanelen för webbtjänsten.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **det här är en bra dag** för **col2**. Klicka på bockmarkeringen.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Du ser något som liknar

![exempel på-utdata](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Exempelkod
Ett annat sätt att testa din RRS är från klientkoden. Om du klickar på **begäran/svar** på instrumentpanelen och bläddra till slutet ser du exempelkod för C#, Python och R. Du kan även se syntaxen för RRS-begäran, inklusive begärande-URI, rubriker och brödtext.

Den här guiden visar en fungerande Python-exemplet. Du kommer att behöva ändra den med den **arbetsytan**, **service**, och **api_key** av experimentet.

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
Klicka på **Batch-körningen** på instrumentpanelen och bläddra till slutet. Exempelkod för visas C#, Python och R. Du kan även se syntaxen för BES-begäranden för att skicka ett jobb startar ett jobb, få status eller resultat för ett jobb och ta bort ett jobb.

Den här guiden visar en fungerande Python-exemplet. Du måste ändra den med den **arbetsytan**, **service**, och **api_key** av experimentet. Dessutom kan du behöva ändra den **lagringskontonamn**, **lagringskontonyckel**, och **namnet på lagringsbehållaren**. Slutligen behöver du ändra platsen för den **indatafilen** och platsen för den **utdatafilen**.

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
