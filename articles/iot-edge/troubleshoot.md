---
title: Felsöka Azure IoT Edge | Microsoft Docs
description: Lös vanliga problem och lär dig felsökning av Azure IoT Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7b9f9f8295aac0920ae4726289c535aae12c4482
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Vanliga problem och lösningar för Azure IoT Edge

Om du får problem med att köra Azure IoT Edge i din miljö kan du använda den här artikeln som en guide för felsökning och lösningar. 

## <a name="standard-diagnostic-steps"></a>Standarddiagnostikåtgärder 

När det uppstår ett problem kan du läsa mer om IoT Edge-enhetens tillstånd genom att granska behållarloggarna och meddelandena som skickas till och från enheten. Använd kommandona och verktygen i det här avsnittet för att samla in information. 

* Titta på loggarna för Docker-behållarna för att identifiera problem. Börja med dina distribuerade behållare och titta på behållarna som utgör IoT Edge-körningen: Edge-agent och Edge Hub. Edge-agentloggarna ger vanligtvis information om livscykeln för varje behållare. Edge Hub-loggarna ger information om meddelanden och routning. 

   ```cmd
   docker logs <container name>
   ```

* Visa meddelandena som skickas genom Edge Hub och få kunskap om uppdateringar av enhetens egenskaper med utförliga loggar från körningsbehållarna. Du kanske behöver lägga till alternativet ”--auto-cert-gen-force-no-passwords” om du följer snabbstartsartiklarna.

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```

* Om du får problem med nätverksanslutningen kan du ta en titt på Edge-enhetens miljövariabler som enhetens anslutningssträng:

   ```cmd
   docker exec edgeAgent printenv
   ```

Du kan också kontrollera meddelandena som skickas mellan IoT Hub och IoT Edge-enheterna. Visa meddelandena genom att använda tillägget [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) för Visual Studio Code. Mer information finns i [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Praktiskt verktyg när du utvecklar med Azure IoT).

När du har letat efter information i loggarna och meddelandena kan du också försöka starta om Azure IoT Edge-körningen:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Edge-agenten avslutas efter ungefär en minut

Edge-agenten startar och körs under ungefär en minut innan den stoppar. Loggarna indikerar att Edge-agenten försöker ansluta till IoT Hub över AMQP, och att den sedan cirka 30 sekunder senare försöker ansluta med AMQP via websocket. När det misslyckas avslutas Edge-agenten. 

Exempel på Edge-agentloggar:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Rotorsak
En nätverkskonfiguration på värdnätverket förhindrar att Edge-agenten kan ansluta till nätverket. Agenten försöker ansluta via AMQP (port 5671) först. Om det misslyckas provar agenten websockets (port 443).

IoT Edge-körningen ställer in ett nätverk för varje modul att kommunicera på. På Linux är nätverket en nätverksbrygga. I Windows använder den NAT. Det här problemet är vanligare på Windows-enheter som använder Windows-behållare som använder NAT-nätverket. 

### <a name="resolution"></a>Lösning
Kontrollera att det finns en väg till internet för IP-adresserna som är tilldelade till den här nätverksbryggan/NAT-nätverket. Ibland åsidosätter en VPN-konfiguration på värden IoT Edge-nätverket. 

## <a name="edge-hub-fails-to-start"></a>Edge Hub startar inte

Edge Hub startar inte och skriver ut följande meddelande till loggarna: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Rotorsak
Några andra processer på värddatorn har bundit port 443. Edge Hub mappar portarna 5671 och 443 för användning i gatewayscenarier. Den här portmappningen misslyckas om någon annan process redan har bundits till porten. 

### <a name="resolution"></a>Lösning
Hitta och stoppa processen som använder port 443. Den här processen är vanligtvis en webbserver.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Edge-agenten kan inte få åtkomst till en moduls avbildning (403)
En behållare kan inte köras, och Edge-agentloggarna visar ett 403-fel. 

### <a name="root-cause"></a>Rotorsak
Edge-agenten har inte behörighet för att få åtkomst till en moduls avbildning. 

### <a name="resolution"></a>Lösning
Försök köra kommandot `iotedgectl login` igen.

## <a name="iotedgectl-cant-find-docker"></a>iotedgectl kan inte hitta Docker
iotedgectl kan inte köra konfigurationen eller startkommandot och skriver ut följande meddelande till loggarna:
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>Rotorsak
iotedgectl kan inte hitt Docker, vilket är et förhandskrav.

### <a name="resolution"></a>Lösning
Installera Docker, se till att det körs och försök igen.

## <a name="next-steps"></a>Nästa steg
Tror du att du har hittat ett fel i IoT Edge-plattformen? [Skicka in ett problem](https://github.com/Azure/iot-edge/issues) så att vi kan fortsätta att förbättra oss. 
