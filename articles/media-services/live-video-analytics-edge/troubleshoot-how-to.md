---
title: Felsöka live video analys på IoT Edge – Azure
description: Den här artikeln beskriver fel söknings steg för video analys i real tid för IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: c297a189f3b13ca8e72daf4eef009bc28fac32bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823198"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Felsöka live video analys på IoT Edge

Den här artikeln beskriver fel söknings steg för LVA (Live Video Analytics) på Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

### <a name="diagnostics"></a>Diagnostik

Som en del av din Live Video Analytics-distribution konfigurerar du Azure-resurser som IoT Hub och IoT Edge enheter. Som ett första steg för att diagnostisera problem bör du alltid kontrol lera att gräns enheten är korrekt konfigurerad genom att följa dessa anvisningar:

1. [Kör `check` kommandot](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Kontrol lera IoT Edge-versionen](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Kontrol lera status för IoT Edge Security Manager och dess loggar](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Visa meddelanden som går via IoT Edge hubben](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Starta om behållare](../../iot-edge/troubleshoot.md#restart-containers).
1. [Kontrol lera brand Väggs-och port konfigurations reglerna](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>För distributions problem

Om gräns infrastrukturen är fin kan du söka efter problem med distributions manifest filen. Om du vill distribuera live video analys på IoT Edge modul på IoT Edge enheten tillsammans med andra IoT-moduler, använder du ett distributions manifest som innehåller IoT Edge Hub, IoT Edge agent och andra moduler och deras egenskaper. Om JSON-koden inte är korrekt utformad kan du få följande fel meddelande: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Det gick inte att parsa JSON från filen: ' <deployment manifest.json> ' för argumentet Content ' med undantaget: "extra data: rad 101 kolumn 1 (char 5325)"

Om det här felet uppstår rekommenderar vi att du kontrollerar JSON för saknade hakparenteser eller andra problem med filens struktur. Om du vill verifiera fil strukturen kan du använda en-klient, till exempel [plugin-programmet för anteckningar + + med JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) eller ett online-verktyg som [JSON-formaterare & verifieraren](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Under distributionen: diagnostisera med Media Graph Direct-metoder 

När live video analys på IoT Edge modul har distribuerats korrekt på IoT Edge enheten, kan du skapa och köra medie diagrammet genom att anropa [direkta metoder](direct-methods.md). Du kan använda Azure Portal för att köra en diagnos av medie diagrammet via direkta metoder:

1. I Azure Portal går du till IoT-hubben som är ansluten till din IoT Edge-enhet.

1. Leta efter **Automatisk enhets hantering**och välj sedan **IoT Edge**.  

1. I listan med gräns enheter väljer du den enhet som du vill diagnostisera.  
         
    ![Skärm bild av Azure Portal som visar en lista över gräns enheter](./media/troubleshoot-how-to/lva-sample-device.png)

1. Kontrol lera om svars koden är *200-OK*. Andra svars koder för [IoT Edge runtime](../../iot-edge/iot-edge-runtime.md) är:
    * 400-distributions konfigurationen är felaktig eller ogiltig.
    * 417 – enheten har ingen distributions konfigurations uppsättning.
    * 412-schema versionen i distributions konfigurationen är ogiltig.
    * 406 – den IoT Edge enheten är offline eller skickar inte status rapporter.
    * 500 – ett fel uppstod i IoT Edge Runtime.

1. Om du får en status 501-kod kontrollerar du att namnet på den direkta metoden är korrekt. Om metod namnet och nytto lasten för begäran är korrekta bör du få resultat tillsammans med lyckad kod = 200. Om nytto lasten för begäran är felaktig får du status = 400 och en svars nytto last som visar felkoden och meddelandet som ska hjälpa till att diagnostisera problemet med ditt direkta metod anrop.
    * Genom att kontrol lera rapporter och önskade egenskaper kan du förstå om modulens egenskaper har synkroniserats med distributionen. Om de inte är det kan du starta om IoT Edge-enheten. 
    * Använd den [direkta metod](direct-methods.md) guiden för att anropa några metoder, särskilt enkla som GraphTopologyList. I guiden anges även förväntade nytto laster och felkoder för begäran och svar. När de enkla direkta metoderna har genomförts kan du vara säker på att live video analys IoT Edge modulen fungerar som den ska.
        
       ![Skärm bild av rutan "direkt metod" för IoT Edge-modulen.](./media/troubleshoot-how-to/direct-method.png) 

1. Om den **angivna distributionen** och **rapporteras av enhets** kolumnerna indikerar *Ja*, kan du anropa direkt metoder i live video analys på IoT Edge modul. Välj modulen för att gå till en sida där du kan kontrol lera önskade och rapporterade egenskaper och anropa direkta metoder. Tänk på följande: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Efter distribution: diagnostisera loggar för problem under körningen 

Behållar loggarna för din IoT Edge-modul bör innehålla diagnostikinformation som hjälper dig att felsöka dina problem under modulens körning. Du kan [kontrol lera behållar loggar för problem](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) och själv diagnostisera problemet. 

Om du har kört alla föregående kontroller och fortfarande stöter på problem samlar du in loggar från IoT Edge-enheten [med `support bundle` kommandot](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) för ytterligare analys av Azure-teamet. Du kan [kontakta oss](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) för support och skicka in de insamlade loggarna.

## <a name="common-error-resolutions"></a>Lösningar för vanliga fel

Live Video Analytics distribueras som en IoT Edge modul på IoT Edge enheten och fungerar tillsammans med IoT Edge agent-och hubb-moduler. Några av de vanliga fel som uppstår när du distribuerar live video analys orsakas av problem med den underliggande IoT-infrastrukturen. Felen är:

* [IoT Edge agenten stannar efter ungefär en minut](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [IoT Edge agenten kan inte komma åt en moduls avbildning (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [IoT Edge agent-modulen rapporterar "Tom konfigurations fil" och inga moduler startar på enheten](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Det gick inte att starta IoT Edge Hub](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [IoT Edge Security daemon Miss lyckas med ett ogiltigt värdnamn](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Live Video Analytics eller någon annan anpassad IoT Edge modul kan inte skicka ett meddelande till Edge Hub med 404-fel](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [IoT Edge modulen har distribuerats och försvinner sedan från enheten](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-setup-script-issues"></a>Problem med installations skript för Edge

Som en del av vår dokumentation har vi tillhandahållit ett [installations skript](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) för att distribuera gräns-och moln resurser och komma igång med Live Video Analytics Edge. I det här avsnittet presenteras några skript fel som kan uppstå, tillsammans med lösningar för att felsöka dem.

Problem: skriptet körs, delvis skapar få resurser, men det Miss lyckas med följande meddelande:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Så här åtgärdar du problemet:

1. Kör följande kommando:

    ```
    az --version
    ```
1. Se till att du har installerat följande tillägg. Från och med publiceringen av den här artikeln är tilläggen och deras versioner:

    | Anknytning | Version |
    |---|---|
    |azure-cli   |      2.5.1|
    |kommandot-modules-nspkg         |   2.0.3|
    |grundläggande  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetri| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Om du har ett installerat tillägg vars version är tidigare än det versions nummer som visas här, uppdaterar du tillägget med hjälp av följande kommando:

    ```
    az extension update --name <Extension name>
    ```

    Du kan till exempel köra `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>Exempel på problem med appen

Som en del av vår utgåva har vi tillhandahållit en del .NET-exempel kod som hjälper dig att få våra startat för utvecklare. I det här avsnittet presenteras några fel som kan uppstå när du kör exempel koden, tillsammans med lösningar för att felsöka dem.

Problem: Program.cs Miss lyckas med följande fel vid direkt metod anropet:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Se till att du har [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) installerade i din Visual Studio Code-miljö och att du har konfigurerat anslutningen till din IoT-hubb. Det gör du genom att välja Ctrl + Shift + P och sedan välja **IoT Hub metod**.

1. Kontrol lera om du kan anropa en direkt metod i IoT Edge-modulen via Visual Studio Code. Anropa till exempel GraphTopologyList med följande nytto Last { &nbsp; " @apiVersion ": "1,0"}. Du bör få följande svar: 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Skärm bild av svaret i Visual Studio Code.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Om föregående lösning Miss lyckas kan du prova följande:

    a. Gå till kommando tolken på din IoT Edge enhet och kör följande kommando:
    
      ```
      sudo systemctl restart iotedge
      ```

      Det här kommandot startar om IoT Edge enheten och alla moduler. Vänta några minuter och innan du försöker använda den direkta metoden igen bekräftar du att modulerna körs genom att köra följande kommando:

      ```
      sudo iotedge list
      ```

    b. Försök att starta om den virtuella datorn eller datorn om den föregående metoden också Miss lyckas.

    c. Om alla metoder inte fungerar kör du följande kommando för att hämta en zippad fil med alla [relevanta loggar](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)och bifoga den till ett [support ärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Om du får ett felsvar *400* -kod ska du kontrol lera att nytto lasten för metod anrop är korrekt utformad, enligt guidens [direkta metoder](direct-methods.md) .
1. Om du får en status *200* -kod, indikerar det att navet fungerar bra och att modul distributionen är korrekt och svarar. 

1. Kontrol lera om appens konfiguration är korrekt. Din app-konfiguration består av följande fält i *appsettings.jsi* filen. Kontrol lera att deviceId och moduleId är korrekta. Ett enkelt sätt att kontrol lera är att gå till avsnittet Azure IoT Hub-tillägg i Visual Studio Code. Värdena i *appsettings.jspå* filen och IoT Hub avsnittet ska överensstämma.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. I *appsettings.jspå* fil kontrollerar du att du har angett anslutnings strängen IoT Hub och *inte* IoT Hub enhets anslutnings sträng, eftersom [anslutnings sträng formaten skiljer sig åt](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Live video analys fungerar med externa moduler

Live video analys via HTTP-tillägget kan utöka medie grafen för att skicka och ta emot data från andra IoT Edge moduler över HTTP med hjälp av REST. Som ett [särskilt exempel](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)kan Media grafen skicka video bild rutor som bilder till en extern härlednings modul som Yolo v3 och ta emot JSON-baserade analys resultat. I en sådan topologi är målet för händelserna främst IoT-hubben. I situationer där du inte ser uthärlednings händelser i hubben, kontrol lera följande:

* Kontrol lera om navet som Media Graph publicerar till och navet du undersöker är desamma. När du skapar flera distributioner kan du få flera hubbar och kontrol lera fel hubb för händelser.
* I Visual Studio Code kontrollerar du om den externa modulen är distribuerad och körs. I exempel bilden här är rtspsim och ka IoT Edge moduler som körs utanför lvaEdge-modulen.

    ![Skärm bild som visar körnings status för moduler i Azure IoT Hub.](./media/troubleshoot-how-to/iot-hub.png)

* Kontrol lera om du skickar händelser till rätt URL-slutpunkt. Den externa AI-behållaren exponerar en URL och en port som tar emot och returnerar data från POST-begäranden. Den här URL-adressen anges som en `endpoint: url` egenskap för HTTP-tilläggs processorn. Som det visas i [topologins URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json), anges slut punkten som inferencing URL-parameter. Kontrol lera att standardvärdet för parametern eller det skickade värdet är korrekt. Du kan testa för att se om det fungerar genom att använda klientens URL (sväng).  

    Här är ett exempel en Yolo v3-behållare som körs på den lokala datorn med IP-adressen 172.17.0.3. Använd Docker för att kontrol lera IP-adressen.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Returnerat resultat:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Om du kör en eller flera instanser av en graf som använder HTTP-tilläggsbegäranden, bör du ha ett RAM frekvens filter före varje HTTP-tilläggsprovider för att hantera bildnings hastigheten för bild rutor per sekund (FPS). 

   I vissa situationer, där processorn eller minnet för Edge-datorn är mycket utnyttjad, kan du förlora vissa härlednings händelser. Du löser problemet genom att ange ett lågt värde för egenskapen maximumFps i filtret för bild Rute frekvens. Du kan ställa in den på 0,5 ("maximumFps": 0,5) på varje instans av grafen och sedan köra instansen igen för att kontrol lera om det finns några utgångs händelser på hubben.

   Du kan också få en kraftfullare Edge-dator med högre processor och minne.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Flera direkta metoder parallellt – timeout-problem 

Live video analys på IoT Edge ger en direkt metod baserad programmerings modell som gör att du kan ställa in flera topologier och flera diagram instanser. Som en del av installationen av topologin och grafen anropar du flera direkta metod anrop i IoT Edge-modulen. Om du anropar dessa flera metod anrop parallellt, särskilt de som startar och stoppar graferna, kan det uppstå ett tids gräns fel som följande: 

Initierings metoden för sammansättningen Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync utlöste ett undantag. Microsoft. Azure. devices. Common. Exceptions. IotHubException: Microsoft. Azure. devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Vi rekommenderar att du *inte* anropar direkta metoder parallellt. Anropa dem i tur och ordning (det vill säga skapa ett direkt metod anrop endast efter att det föregående är klart).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Samla in loggar för att skicka in ett support ärende

När självgående fel söknings steg inte löser problemet kan du gå till Azure Portal och [öppna ett support ärende](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Loggarna kan innehålla personligt identifierbar information (PII), till exempel din IP-adress. Alla lokala kopior av loggarna tas bort så snart vi slutförde undersökningen och stänger support ärendet.  

Följ anvisningarna i nästa avsnitt om du vill samla in relevanta loggar som ska läggas till i biljetten. Du kan ladda upp loggfilerna i **informations** fönstret för support förfrågan.

### <a name="use-the-support-bundle-command"></a>Använda kommandot support-bunt

När du behöver samla in loggar från en IoT Edge-enhet är det enklaste sättet att använda `support-bundle` kommandot. Det här kommandot samlar in:

- Modul loggar
- IoT Edge Security Manager och behållar motor loggar
- Iotedge kontrol lera JSON-utdata
- Användbar felsöknings information

1. Kör `support-bundle` kommandot med flaggan *--efter* flagga för att ange hur lång tid du vill att dina loggar ska gälla. Till exempel får 2H loggar för de senaste två timmarna. Du kan ändra värdet för den här flaggan om du vill inkludera loggar för olika perioder.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Det här kommandot skapar en fil med namnet *support_bundle.zip* i katalogen där du körde kommandot. 
   
1. Bifoga *support_bundle.zip* -filen till support ärendet.

### <a name="live-video-analytics-debug-logs"></a>Real tids fel söknings loggar för video analys

Gör så här för att konfigurera live video analys på IoT Edge modul för att generera fel söknings loggar:

1. Logga in på [Azure Portal](https://portal.azure.com)och gå till din IoT-hubb.
1. I den vänstra rutan väljer du **IoT Edge**.
1. I listan med enheter väljer du ID för mål enheten.
1. Längst upp i fönstret väljer du **Ange moduler**.

   ![Skärm bild av knappen Ange moduler i Azure Portal.](media/troubleshoot-how-to/set-modules.png)

1. I avsnittet **IoT Edge moduler** söker du efter och väljer **lvaEdge**.
1. Välj **alternativ för att skapa behållare**.
1. I avsnittet **bindningar** lägger du till följande kommando:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Det här kommandot binder mapparna loggfiler mellan gräns enheten och behållaren. Om du vill samla in loggarna på en annan plats använder du följande kommando och ersätter **$LOG _LOCATION_ON_EDGE_DEVICE** med den plats som du vill använda: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Välj **Uppdatera**.
1. Välj **Granska + skapa**. Ett lyckat verifierings meddelande har publicerats under en grön banderoll.
1. Välj **Skapa**.
1. Uppdatera **modulens identitet** så att den pekar på parametern DebugLogsDirectory, som pekar på den katalog där loggarna samlas in:

    a. Under tabellen **moduler** väljer du **lvaEdge**.  
    b. Längst upp i fönstret väljer du Modulnamn, med **dubbla**. Ett redigerbart fönster öppnas.  
    c. Lägg till följande nyckel/värde-par under **önskad nyckel**:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Det här kommandot binder mapparna loggfiler mellan gräns enheten och behållaren. Om du vill samla in loggar på en annan plats på enheten:
    > 1. Skapa en bindning för fel söknings logg platsen i avsnittet **bindningar** och ersätt **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** och **$Debug _LOG_LOCATION** med den plats som du vill använda: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Använd följande kommando och Ersätt **$DEBUG _LOG_LOCATION** med den plats som användes i föregående steg:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Välj **Spara**.

1. Återskapa problemet.
1. Anslut till den virtuella datorn från sidan **IoT Hub** på portalen.
1. Zip alla filer i mappen *debugLogs*

   > [!NOTE]
   > Dessa loggfiler är inte avsedda för själv diagnostisering. De är avsedda för Azures teknik team för att analysera dina problem.

   a. I följande kommando ska du se till att ersätta **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** med platsen för fel söknings loggarna på den gräns enhet som du har konfigurerat tidigare.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Bifoga *debugLogs.zip* -filen till support ärendet.

1. Du kan stoppa logg insamling genom att ange värdet i **modulens identitet** , till *Null*. Gå tillbaka till sidan för **modulens identitet med dubbla** och uppdatera följande parameter som:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Nästa steg

[Självstudie: Event-baserad videoinspelning till molnet och uppspelningen från molnet](event-based-video-recording-tutorial.md)
