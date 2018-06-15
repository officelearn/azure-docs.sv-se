---
title: Azure Service Fabric omvänd proxy diagnostik | Microsoft Docs
description: Lär dig mer om att övervaka och diagnostisera behandling av begäranden på omvänd proxy.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: 662fc124af71c1ce976037a3544f59e3cea54ef0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207639"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Övervaka och diagnostisera behandling av begäranden på omvänd proxy

Från och med 5.7 för Service Fabric omvänd proxyhändelser som är tillgängliga för samlingen. Händelserna som är tillgängliga i två kanaler, en med endast felhändelser relaterade till behandling av begäranden fel i omvänd proxy och andra kanalen som innehåller utförlig händelser med poster för både slutförda och misslyckade begäranden.

Referera till [samla in händelser för omvänd proxy](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) att aktivera att samla in händelser från dessa kanaler i lokala grupper och Azure Service Fabric-kluster.

## <a name="troubleshoot-using-diagnostics-logs"></a>Felsöka med diagnostik-loggar
Här följer några exempel på hur du tolkar vanliga fel loggarna som en kan uppstå:

1. Omvänd proxy returnerar Svarets statuskod 504 (Timeout).

    En orsak kan bero på tjänsten som inte svarar inom tidsgränsen för begäran.
Den första händelsen nedan loggar information om begäran tas emot på omvänd proxy. Den andra händelsen indikerar att begäran misslyckades vid vidarebefordran till tjänsten på grund av att ”internt fel = ERROR_WINHTTP_TIMEOUT” 

    Nyttolasten innehåller:

    *  **traceId**: detta GUID kan användas för att matcha alla händelser som motsvarar en enskild begäran. I den nedan två händelser, traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, innebär att de tillhör samma begäran.
    *  **requestUrl**: URL (omvänd proxy-URL) som begäran har skickats.
    *  **verbet**: HTTP-verbet.
    *  **remoteAddress**: adressen för klienten skickar en begäran.
    *  **resolvedServiceUrl**: tjänsten slutpunkts-URL som den inkommande begäranden har lösts. 
    *  **Felinformation**: Mer information om felet.

    ```
    {
      "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
      "ProviderName": "Microsoft-ServiceFabric",
      "Id": 51477,
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
      "ProcessId": 57696,
      "Level": "Informational",
      "Keywords": "0x1000000000000021",
      "EventName": "ReverseProxy",
      "ActivityID": null,
      "RelatedActivityID": null,
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
        "verb": "GET",
        "remoteAddress": "::1",
        "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
        "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
      }
    }

    {
      "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
      ...
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
      ...
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "statusCode": 504,
        "description": "Reverse Proxy Timeout",
        "sendRequestPhase": "FinishSendRequest",
        "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
      }
    }
    ```

2. Omvänd proxy returnerar Svarets statuskod 404 (inget hittas). 
    
    Här är en exempel-händelse där omvänd proxy returnerar 404 eftersom den inte gick att hitta matchande tjänstslutpunkten.
    Intressant här nyttolast-poster är:
    *  **processRequestPhase**: Anger fasen under bearbetning av begäran när felet uppstod ***TryGetEndpoint*** engångsfaktorautentisering vid försök att hämta tjänstslutpunkten ska vidarebefordras till. 
    *  **Felinformation**: Visar sökvillkor slutpunkt. Här kan du se att listenerName angetts = **FrontEndListener** slutpunktslista replik innehåller endast en lyssnare med namnet **OldListener**.
    
    ```
    {
      ...
      "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
      "ProcessId": 57696,
      "Level": "Warning",
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
        ...
        "processRequestPhase": "TryGetEndoint",
        "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
      }
    }
    ```
    Ett annat exempel där omvänd proxy kan returnera 404 gick inte att hitta är: ApplicationGateway\Http konfigurationsparameter **SecureOnlyMode** är inställd på Sant med omvänd proxy lyssnar på **HTTPS**, men alla slutpunkter replik är oskyddade (avlyssnar HTTP).
    Omvänd proxy returnerar 404 eftersom det går inte att hitta en slutpunkt som lyssnade på HTTPS för att vidarebefordra begäran. Analysera parametrarna i händelseloggen nyttolast hjälper för att begränsa problemet:
    
    ```
        "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
    ```

3. Begäran om att omvänd proxy misslyckas med ett timeout-fel. 
    Händelseloggarna innehåller en händelse med tog emot begäran information (visas inte här).
    Nästa händelse visar att tjänsten svarade med statuskod 404 och omvänd proxy initierar ett nytt lösa. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Om att samla in alla händelser, ser du tåg av händelser som visar var Lös och vidarebefordra försök.
    Den sista händelsen i serien visar bearbetningen misslyckades med en tidsgräns, tillsammans med antalet lyckade Lös försök.
    
    > [!NOTE]
    > Det rekommenderas att hålla utförlig kanal händelseinsamling inaktiverad som standard och aktivera den för felsökning på grundval av behov.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Om samlingen är aktiverad för kritiska/felhändelser bara finns en händelse med information om tidsgränsen och antalet försök åtgärda. 
    
    Tjänster som har för avsikt att skicka statuskod 404 tillbaka till användaren, bör lägga till ett ”X ServiceFabric”-huvud i svaret. Efter att huvudet har lagts till i svaret, vidarebefordrar omvänd proxy statuskoden tillbaka till klienten.  

4. Fall när klienten har kopplats från begäran.

    Följande händelse registreras när omvänd proxy vidarebefordrar svar till klienten, men klienten kopplas:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Omvänd Proxy returnerar 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST fel returneras om URI-schemat inte har angetts för tjänstslutpunkten i service manifest.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Lös problemet genom att ange URI-schemat i manifestet.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Händelser relaterade till behandling av begäranden websocket loggas inte. Detta kommer att läggas till i nästa utgåva.

## <a name="next-steps"></a>Nästa steg
* [Aggregering av händelse och med Windows Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md) för att aktivera Logginsamling i Azure-kluster.
* Service Fabric-händelser i Visual Studio finns i [övervaka och diagnostisera lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Referera till [konfigurera omvänd proxy för att ansluta till säkra tjänster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) för Azure Resource Manager mallen exempel för att konfigurera secure omvänd proxy med olika Tjänstcertifikatet verifieringsalternativ.
* Läs [Service Fabric omvänd proxy](service-fabric-reverseproxy.md) vill veta mer.
