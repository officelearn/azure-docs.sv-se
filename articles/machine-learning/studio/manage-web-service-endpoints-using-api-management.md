---
title: "Lär dig att hantera AzureML-webbtjänster med hjälp av API-hantering | Microsoft Docs"
description: "En guide som visar hur du hanterar AzureML-webbtjänster som använder API-hantering."
keywords: Machine learning api-hantering
services: machine-learning
documentationcenter: 
author: roalexan
manager: jhubbard
editor: 
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: roalexan
ms.openlocfilehash: 53a6b18fb74db46ccb66c7c70851a9bf364e927c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Lär dig hur du hanterar AzureML-webbtjänster som använder API Management
## <a name="overview"></a>Översikt
Den här guiden visar hur du Kom snabbt igång med API-hantering för att hantera AzureML-webbtjänster.

## <a name="what-is-azure-api-management"></a>Vad är Azure API Management?
Azure API Management är en Azure-tjänst som låter dig hantera REST API-slutpunkter genom att definiera användaråtkomst, begränsning och instrumentpanelen för övervakning. Klicka på [här](https://azure.microsoft.com/services/api-management/) information om Azure API Management. Klicka på [här](../../api-management/api-management-get-started.md) en guide om hur du kommer igång med Azure API Management. Den här andra guiden som den här guiden bygger på omfattar flera ämnen, t.ex. meddelande konfigurationer, nivå prissättning, svar hantering, autentisering av användare, skapa produkter, developer prenumerationer och användning dashboarding.

## <a name="what-is-azureml"></a>Vad är AzureML?
AzureML är en Azure-tjänst för maskininlärning där du kan enkelt skapa, distribuera och dela avancerade Analyslösningar. Klicka på [här](https://azure.microsoft.com/services/machine-learning/) information om AzureML.

## <a name="prerequisites"></a>Krav
Du behöver följande för att slutföra den här guiden:

* Ett Azure-konto. Om du inte har ett Azure-konto, klickar du på [här](https://azure.microsoft.com/pricing/free-trial/) mer information om hur du skapar ett kostnadsfritt utvärderingskonto.
* AzureML-konto. Om du inte har en AzureML-konto, klickar du på [här](https://studio.azureml.net/) mer information om hur du skapar ett kostnadsfritt utvärderingskonto.
* Arbetsytan, tjänst och api_key för en AzureML-experiment som distribueras som en webbtjänst. Klicka på [här](create-experiment.md) mer information om hur du skapar ett AzureML-experiment. Klicka på [här](publish-a-machine-learning-web-service.md) för information om hur du distribuerar en AzureML experiment som en webbtjänst. Bilaga A har också anvisningar för hur du skapar och testar ett enkelt experiment AzureML och distribuera det som en webbtjänst.

## <a name="create-an-api-management-instance"></a>Skapa en API Management-instans
Nedan visas stegen för att hantera din AzureML-webbtjänsten med hjälp av API-hantering. Först skapa en instans av tjänsten. Logga in på den [klassiska portalen](https://manage.windowsazure.com/) och på **ny** > **Apptjänster** > **API Management** > **skapa**.

![Skapa instans](./media/manage-web-service-endpoints-using-api-management/create-instance.png)

Ange ett unikt **URL**. Den här guiden använder **demoazureml** – måste du välja något annat. Välj önskad **prenumeration** och **region** för din tjänstinstans. Klicka på Nästa när du har gjort dina val.

![skapa-tjänsten-1](./media/manage-web-service-endpoints-using-api-management/create-service-1.png)

Ange ett värde för den **organisationsnamn**. Den här guiden använder **demoazureml** – måste du välja något annat. Ange din e-postadress i den **administratör e-post** fältet. Den här e-postadressen används för meddelanden från API Management-systemet.

![skapa-tjänsten-2](./media/manage-web-service-endpoints-using-api-management/create-service-2.png)

Markera kryssrutan om du vill skapa en tjänstinstans. *Det tar upp till 30 minuter innan en ny tjänst skapas*.

## <a name="create-the-api"></a>Skapa API: et
När instansen för tjänsten har skapats, är nästa steg att skapa API: et. Ett API består av en uppsättning åtgärder som kan anropas från ett klientprogram. API-åtgärder körs via en proxy till befintliga webbtjänster. Den här guiden skapar API: er proxyinställningarna så att de befintliga AzureML RRS och BES-webbtjänsterna.

API: er skapas och konfigureras från publisher-portalen API som öppnas via den klassiska Azure-portalen. Välj service-instans för att nå publisher-portalen.

![Välj tjänstinstans](./media/manage-web-service-endpoints-using-api-management/select-service-instance.png)

Klicka på **hantera** i den klassiska Azure-portalen för API Management-tjänsten.

![hantera service](./media/manage-web-service-endpoints-using-api-management/manage-service.png)

Klicka på **API: er** från den **API Management** menyn till vänster och klicka sedan på **lägga till API**.

![management-API-menyn](./media/manage-web-service-endpoints-using-api-management/api-management-menu.png)

Typen **AzureML Demo API** som den **Web API-namnet**. Typen **https://ussouthcentral.services.azureml.net** som den **-webbtjänstens URL**. Typen **azureml-demo** som den **URL för Web API-suffix**. Kontrollera **HTTPS** som den **URL för Web API** schema. Välj **Starter** som **produkter**. När du är klar klickar du på **spara** att skapa API: et.

![Lägg till-nya-api](./media/manage-web-service-endpoints-using-api-management/add-new-api.png)

## <a name="add-the-operations"></a>Lägg till åtgärder
Klicka på **lägga till åtgärden** att lägga till åtgärder i detta API.

![lägga till åtgärden](./media/manage-web-service-endpoints-using-api-management/add-operation.png)

Den **nya åtgärden** visas och **signatur** fliken väljs som standard.

## <a name="add-rrs-operation"></a>Lägg till RR-åtgärd
Först skapa en åtgärd för AzureML RRS-tjänsten. Välj **POST** som den **HTTP-verbet**. Typen **/workspaces/ {arbetsytan} /services/ {tjänsten} / execute? api-version = {apiversion} & information = {information}** som den **URL mallen**. Typen **Resursposter köra** som den **visningsnamn**.

![Lägg till RR-åtgärden-signaturer](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Klicka på **svar** > **lägga till** till vänster och välj **200 OK**. Klicka på **spara** att spara den här åtgärden.

![Lägg till-RR-åtgärden-svar](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Lägga till BES-åtgärder
Skärmbilder ingår inte för BES-åtgärder som de är mycket lik dem för att lägga till åtgärden Resursposter.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Skicka (men inte startar) ett Batch Execution-jobb
Klicka på **lägga till åtgärden** att lägga till åtgärden AzureML BES-API: et. Välj **POST** för den **HTTP-verbet**. Typen **/workspaces/ {arbetsytan} /services/ {tjänsten} / jobb? api-version = {apiversion}** för den **URL mallen**. Typen **BES skicka** för den **visningsnamn**. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**. Klicka på **spara** att spara den här åtgärden.

### <a name="start-a-batch-execution-job"></a>Starta ett jobb i Batch Execution
Klicka på **lägga till åtgärden** att lägga till åtgärden AzureML BES-API: et. Välj **POST** för den **HTTP-verbet**. Typen **/workspaces/ {arbetsytan} /services/ {tjänsten} /jobs/ {jobid} / start? api-version = {apiversion}** för den **URL mallen**. Typen **BES starta** för den **visningsnamn**. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**. Klicka på **spara** att spara den här åtgärden.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Hämta status eller resultatet av ett Batch Execution-jobb
Klicka på **lägga till åtgärden** att lägga till åtgärden AzureML BES-API: et. Välj **hämta** för den **HTTP-verbet**. Typen **/workspaces/ {arbetsytan} /services/ {tjänsten} /jobs/ {jobid}? api-version = {apiversion}** för den **URL mallen**. Typen **BES Status** för den **visningsnamn**. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**. Klicka på **spara** att spara den här åtgärden.

### <a name="delete-a-batch-execution-job"></a>Ta bort en Batch Execution-jobb
Klicka på **lägga till åtgärden** att lägga till åtgärden AzureML BES-API: et. Välj **ta bort** för den **HTTP-verbet**. Typen **/workspaces/ {arbetsytan} /services/ {tjänsten} /jobs/ {jobid}? api-version = {apiversion}** för den **URL mallen**. Typen **BES ta bort** för den **visningsnamn**. Klicka på **svar** > **lägga till** till vänster och välj **200 OK**. Klicka på **spara** att spara den här åtgärden.

## <a name="call-an-operation-from-the-developer-portal"></a>Anropa en åtgärd från utvecklarportalen
Åtgärder kan anropas direkt från Developer-portalen, vilket ger ett bekvämt sätt att visa och testa driften av ett API. I det här steget i guiden kommer du anropar den **Resursposter köra** metod som har lagts till i den **AzureML Demo API**. Klicka på **Developer-portalen** på menyn längst upp i den klassiska portalen.

![Developer-portalen](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

Klicka på **API: er** från den översta menyn och klicka sedan på **AzureML Demo API** att se åtgärderna som är tillgängliga.

![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Välj **Resursposter köra** för åtgärden. Klicka på **prova**.

![Försök it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

De begäranparametrarna, Skriv din **arbetsytan**, **service**, **2.0** för den **apiversion**, och **SANT** för den **information**. Du hittar din **arbetsytan** och **service** i instrumentpanelen för AzureML web service (se **testa webbtjänsten** i bilaga A).

Huvuden för begäran, klickar du på **Lägg till sidhuvud** och skriv **Content-Type** och **application/json**, klicka på **Lägg till sidhuvud** och skriv **auktorisering** och **ägar <YOUR AZUREML SERVICE API-KEY>** . Du kan hitta din **api-nyckel** i instrumentpanelen för AzureML web service (se **testa webbtjänsten** i bilaga A).

Typen **{”indata”: {”input1”: {”ColumnNames”: [”Col2”], ”värden”: [[”det här är en bra dag”]]}}, ”GlobalParameters”: {}}** för begärandetexten.

![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Klicka på **skicka**.

![Skicka](./media/manage-web-service-endpoints-using-api-management/send.png)

När en åtgärd har anropats developer-portalen visar den **begärda URL: en** från backend-tjänsten, den **svarsstatusen**, **svarshuvuden**, och en **svar innehåll**.

![Response-status](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Bilaga A – skapa och testa en enkel AzureML webbtjänsten
### <a name="creating-the-experiment"></a>Skapa experimentet
Nedan följer du stegen för att skapa ett enkelt experiment AzureML och distribuera den som en webbtjänst. Tar för web-tjänst som indata för en kolumn med valfri text och returnerar en uppsättning funktioner som visas i form av heltal. Exempel:

| Text | Hashformaterats Text |
| --- | --- |
| Det här är en bra dag |1 1 2 2 0 2 0 1 |

Börja med en webbläsare, navigera till: [https://studio.azureml.net/](https://studio.azureml.net/) och ange dina autentiseringsuppgifter för att logga in. Skapa sedan ett nytt tomt experiment.

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

![Publicera webbtjänst](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

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

![Test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typen **detta är en bra dag** för **col2**. Klicka på bockmarkeringen.

![Ange data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

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
