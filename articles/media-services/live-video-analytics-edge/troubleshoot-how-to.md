---
title: Felsöka live video analys på IoT Edge – Azure
description: Den här artikeln beskriver fel söknings steg för video analys i real tid för IoT Edge.
ms.topic: how-to
ms.date: 05/24/2020
ms.openlocfilehash: c235dd27da1d370531c1668c40586d4ae479aec7
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261123"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Felsöka live video analys på IoT Edge

Den här artikeln beskriver fel söknings steg för video analys i real tid för IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

### <a name="diagnostics"></a>Diagnostik

Som en del av distributionen av video analyser i real tid konfigurerar du Azure-resurser som IoT Hub och IoT Edge enheten. Som ett första steg för att diagnostisera problem garanterar alltid att gränsen är rätt inställd genom att följa dessa anvisningar:

1. [Kör kommandot check](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Kontrol lera din IoT Edge version](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Kontrol lera status för IoT Edge Security Manager och dess loggar](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Visa meddelanden som går via IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Starta om behållare](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Kontrol lera brand Väggs-och port konfigurations reglerna](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>För distributions problem

Om gräns infrastrukturen är fin kan du söka efter problem med distributions manifest filen. För att distribuera live video analys i IoT Edge modul på gräns enheten tillsammans med andra IoT-moduler, använder du ett distributions manifest som innehåller Edge-hubben, Edge-agenten och andra moduler med deras egenskaper. Om JSON inte är korrekt formaterad, kan du få ett fel meddelande som nedan: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Det gick inte att parsa JSON från filen: ' <deployment manifest.json> ' för argumentet Content ' med undantaget: "extra data: rad 101 kolumn 1 (char 5325)"

Om det här felet uppstår rekommenderar vi att du kontrollerar JSON-filen för saknade hakparenteser eller andra problem med filens struktur. Du kan använda en klient som [anteckningar + + med JSON Viewer plugin-program](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) eller ett online-verktyg som https://jsonformatter.curiousconcept.com/ för att verifiera fil strukturen.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Distribution – diagnostisera med Media Graph Direct-metoder 

När live video analys på IoT Edge modul har distribuerats korrekt på gräns enheten kan du skapa och köra medie diagrammet genom att anropa [direkta metoder](direct-methods.md). Du kan använda portalen för att köra diagnostiken av medie diagram via direkta metoder:

1. Via portalen går du till den IoT Hub som är ansluten till din Edge-enhet.
    1. När du är på bladet IoT Hub letar du upp den automatiska enhets hanterings >IoT Edge.
    1. Om du klickar på IoT Edge bör du ta fram en lista över gräns enheter. Välj den enhet som du vill diagnostisera.
         
        ![Gräns enheter](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Kontrol lera om svars koden är 200-OK. Det finns olika svars koder för [IoT Edge runtime](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) , till exempel:
        1. 400-distributions konfigurationen är felaktig eller ogiltig.
        1. 417 – enheten har ingen distributions konfigurations uppsättning.
        1. 412-schema versionen i distributions konfigurationen är ogiltig.
        1. 406 – den IoT Edge enheten är offline eller skickar inte status rapporter.
        1. 500 – ett fel uppstod i IoT Edge Runtime.
    1. Om du klickar på enheten bör du även visa en lista över förväntade IoT Edge-moduler som har distribuerats och deras status
    1. Om kolumnerna "anges i distributionen" och "rapporteras av enhet" visar "Ja", kan du anropa direkt metoder i live video analys på IoT Edge modul. Klicka på modulen så kommer du till en skärm där du kan kontrol lera önskade och rapporterade egenskaper och kan anropa direkta metoder. 
        1. Genom att kontrol lera rapporterade och önskade egenskaper kan du förstå om modulens egenskaper har synkroniserats med distributionen. Om de inte har det kan du starta om din gräns 
        1. Använd den [direkta metod](direct-methods.md) guiden för att anropa några metoder, särskilt enkla som GraphTopologyList. I guiden anges även förväntade nytto laster och felkoder för begäran och svar. När de enkla direkta metoderna har genomförts kan du se till att Live Video Analytics Edge-modulen fungerar som den ska.
        
        ![Direkt metod](./media/troubleshoot-how-to/direct-method.png) 
1. Om du får en status 501-kod kontrollerar du att namnet på den direkta metoden är korrekt. Om metod namnet och nytto lasten för begäran är korrekta bör du få tillbaka resultatet tillsammans med lyckad kod = 200. Om nytto lasten för begäran är felaktig får du status = 400 och en svars nytto last som visar felkoden och meddelandet som ska hjälpa till att diagnostisera problemet med ditt direkta metod anrop. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Efter distribution – diagnostisera loggar för problem under körning 

Behållar loggarna för vår Edge-modul bör ha diagnostik<!--<todo:add link to diagnostics doc>--> information som ska hjälpa dig att felsöka problem under modul körning. Du kan [kontrol lera behållar loggar för problem](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) och själv diagnostisera, men om alla kontrollerna ovan har utförts och du fortfarande har problem, kan du samla in loggar från IoT Edge-enheten [med kommandot "support bunt"](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) som kan analyseras ytterligare av Azure-teamet. Du kan [Kontakta](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oss för support och skicka in de insamlade loggarna.

## <a name="common-error-resolutions"></a>Lösningar för vanliga fel

Live Video Analytics distribueras som en IoT Edge modul på gräns enheten och fungerar tillsammans med IoT Edge agent-och hubb-moduler. Några av de vanliga fel som du kommer att följa med i Live Video Analytics-distributionen beror på problemen med den underliggande IoT-infrastrukturen. Några vanliga fel som IoT Edge agent och hubb kan vara:

1. [IoT Edge agenten stannar efter ungefär en minut](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [IoT Edge agenten kan inte komma åt en moduls avbildning (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [Edge agent module rapporterar tom konfigurations fil och inga moduler startar på enheten](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [IoT Edge hubben kan inte startas](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [IoT Edge Security daemon Miss lyckas med ett ogiltigt värdnamn](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [Live video analys eller andra anpassade IoT Edge-moduler kan inte skicka ett meddelande till Edge Hub med 404-fel](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [IoT Edge-modulen har distribuerats försvinner sedan från enheten](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Edge Ställ in skript problem

Som en del av vår dokumentation har vi tillhandahållit ett [konfigurerat skript](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) för att distribuera gräns-och moln resurser för att komma igång med Live Video Analytics Edge. I det här avsnittet har vi fångat fel som du kanske möter med skriptet och hur du kan felsöka dem.

Skriptet körs delvis för att skapa få resurser men Miss lyckas med följande meddelande:

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
1. Se till att du har installerat följande tillägg. När den här hand boken skrivs är versionen för tillägg följande:

    |||
    |---|---|
    |azure-cli   |      2.5.1|
    |kommandot-modules-nspkg         |   2.0.3|
    |grundläggande  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetridata| 1.0.4|
    |Tillägg    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Om något av tilläggen är äldre än ovanstående versions nummer uppdaterar du tillägget till den senaste versionen med hjälp av kommandot:

    ```
    az extension update --name <Extension name>
    ```

    Till exempel, `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Exempel på problem med appen

Som en del av vår utgåva har vi tillhandahållit en del .NET-exempel kod för att få våra utvecklares community-startat. I det här avsnittet har vi fångat fel som du kan stöta på när du kör exempel koden och hur du felsöker sådana fel.

1. Program.cs Miss lyckas med följande fel vid direkt metod anropet:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Se till att du har [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) installerade i vs Code-miljön och anslutningen till IoT Hub installationen. (Ctrl + Shift + P och välj sedan Välj IoT Hub metod för att ansluta till din prenumeration och IoT Hub)
1. Kontrol lera om du kan anropa en direkt metod i Edge-modulen via VS Code (anropa till exempel GraphToplogyList med följande nytto Last {" @apiVersion ": "1,0"}) och du bör få följande svar tillbaka. 

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

    ![Visual Studio-koden](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Om ovanstående Miss lyckas kan du prova följande:
    1. Gå till kommando tolken på din Edge-enhet och skriv.
    
    ```
    sudo systemctl restart iotedge
    ```

    Då startas gräns enheten och alla moduler. Vänta några minuter och kör följande för att bekräfta att modulerna körs, innan du försöker använda DirectMethod igen.

    ```
    sudo iotedge list
    ```
    1. Om detta inte är fallet kan du försöka starta om den virtuella datorn eller datorn.
    1. Om detta Miss lyckas kan du köra följande för att hämta en ZIP-fil med alla [relevanta loggar](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) som ska bifogas till det [support ärendet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Om du får ett felsvar 400-kod ska du se till att nytto lasten för metod anrop är korrekt utformad som i hand boken för [Direct-metoden](direct-methods.md) .
1. Om du får status 200-koden anger det att hubben fungerar bra och att modul distributionen är korrekt och svarar. Nästa steg är att kontrol lera om apparnas konfigurationer är korrekta. Din app-konfiguration består av följande fält i filen appSettings. JSON. Kontrol lera att deviceId och moduleId är korrekta. Ett enkelt sätt att kontrol lera detta är via Azure IoT Hub Extension-avsnittet i VSCode. Värdena i appSettings. JSON-filen och avsnittet IoT Hub ska matcha.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)

1. Se till att du har angett IoT Hub anslutnings sträng i appSettings. JSON och inte anslutnings strängen för IoT Hub enheten eftersom deras [format](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) skiljer sig åt.

### <a name="live-video-analytics-working-with-external-modules"></a>Live video analys fungerar med externa moduler

Live video analys via HTTP-tillägget kan utöka medie grafen för att skicka och ta emot data från andra IoT Edge moduler över HTTP med REST.  Som ett [särskilt exempel](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) kan Media grafen skicka video bild rutor som bilder till en extern härlednings modul som Yolo v3 och ta emot JSON-baserade analys resultat tillbaka. I en sådan topologi är slut målet för händelserna oftast IoT Hub. I situationer där du inte ser uthärlednings händelser i hubben, kontrol lera följande:

1. Kontrol lera om navet som Media Graph publicerar till vs. det du undersöker är detsamma. Ibland när du skapar flera distributioner, kommer du att få flera hubbar och kan kontrol lera fel hubb för händelser.
1. Kontrol lera via VSCode om den externa modulen distribueras och körs. I exempel bilden här är rtspsim och ka IoT Edge moduler som kör externa till lvaEdge-modulen.

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)
1. Kontrol lera om du skickar händelser till rätt URL-slutpunkt. Den externa AI-behållaren exponerar en URL och en port som den tar emot och returnerar data från POST-begäranden. Den här URL: en har angetts som en slut punkt: URL-egenskap för http-tilläggsbegäranden. Som det visas i [topologins URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) anges URL-parametern inferencing. Se till att standardvärdet för parametern ( http://yolov3/score) eller det överförda värdet är korrekt och att du kan testa om det fungerar med hjälp av sväng.  
    1. Som exempel är Yolo v3-behållare som körs på den lokala datorn och IP-adressen för containern 172.17.0.3 (Använd Docker för att söka efter IP-adress).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Returnerat resultat:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Om du kör en eller flera instanser av ett diagram som använder http-tilläggsbegäranden, bör du ha ett RAM frekvens filter innan varje http-Tilläggsprovider kan hantera bild rutor per sekund (FPS) för video flödet. I vissa situationer där processorn/minnet på Edge-datorn är mycket utnyttjad kan du förlora vissa utgångna störningar. Ange ett lågt värde för egenskapen maximumFps i filtret för bild Rute frekvensen för att åtgärda detta. Du kan ställa in den på 0,5 ("maximumFps": 0,5) på varje instans av grafen och sedan köra igen för att kontrol lera om det finns några utgångs händelser på hubben.
    1. Du kan också få en kraftfullare Edge-dator med högre processor och minne.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Flera direkta metoder parallellt – timeout-problem 

Live video analys på IoT Edge ger en direkt metod baserad programmerings modell som gör det möjligt att konfigurera flera topologier och flera diagram instanser. Som en del av installationen av topologin och grafen kommer du att anropa flera direkta metod anrop i Edge-modulen. Om du anropar dessa flera metod anrop, särskilt de som startar och stoppar diagrammen parallellt, kan det uppstå tids gränser som nedan. 

Sammansättnings initierings metoden Microsoft. Media. LiveVideoAnalytics. test. Feature. Edge. AssemblyInitializer. InitializeAssemblyAsync utlöste ett undantag. Microsoft. Azure. devices. Common. Exceptions. IotHubException: Microsoft. Azure. devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Vi rekommenderar att du inte anropar direkta metoder parallellt, men gör det på ett sekventiellt sätt, t. ex.  ett direkt metod anrop endast efter det att föregående har slutförts. 

## <a name="next-steps"></a>Nästa steg

[Självstudie: Event-baserad videoinspelning till molnet och uppspelningen från molnet](event-based-video-recording-tutorial.md)
