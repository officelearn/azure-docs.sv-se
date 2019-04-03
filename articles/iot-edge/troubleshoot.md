---
title: Felsökning – Azure IoT Edge | Microsoft Docs
description: Använd den här artikeln lär du dig standard diagnostiska färdigheter för Azure IoT Edge, som hämtar Komponentstatus och loggar och lösa vanliga problem
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 83595bf045de412954c176028babc4f94fcb21e1
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847542"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Vanliga problem och lösningar för Azure IoT Edge

Om du får problem med att köra Azure IoT Edge i din miljö kan du använda den här artikeln som en guide för felsökning och lösningar. 

## <a name="standard-diagnostic-steps"></a>Standarddiagnostikåtgärder 

När det uppstår ett problem, lär du dig mer om tillståndet för din IoT Edge-enhet genom att granska behållarloggarna och meddelandena som skickas till och från enheten. Använd kommandona och verktygen i det här avsnittet för att samla in information. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrollera status för IoT Edge Security Manager och dess loggfiler:

I Linux:
- Visa status för IoT Edge-Säkerhetshanteraren:

   ```bash
   sudo systemctl status iotedge
   ```

- Visa loggar av IoT Edge-Säkerhetshanteraren:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Visa mer detaljerad loggar av IoT Edge-Säkerhetshanteraren:

   - Redigera inställningar för iotedge-daemon:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Uppdatera följande rader:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Starta om IoT Edge-Security-Daemon:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

I Windows:
- Visa status för IoT Edge-Säkerhetshanteraren:

   ```powershell
   Get-Service iotedge
   ```

- Visa loggar av IoT Edge-Säkerhetshanteraren:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false} |
   format-table -autosize -wrap
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Om IoT Edge Security Manager inte körs, kontrollera din yaml-konfigurationsfil

> [!WARNING]
> YAML-filer får inte innehålla flikar som indrag. Använd 2 blanksteg i stället.

I Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Kontrollera behållarloggarna för problem

När IoT Edge Security Daemon körs kan du titta på loggarna för behållarna för att identifiera problem. Börja med dina distribuerade behållare och titta på behållarna som utgör IoT Edge-körningen: edgeAgent och edgeHub. IoT Edge-agentloggarna ger vanligtvis information om livscykeln för varje behållare. IoT Edge hub-loggarna ger information om meddelanden och routning. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Visa meddelandena som skickas via IoT Edge hub

Du kan visa meddelanden som skickas via IoT Edge hub och samla in information från utförliga loggar från körningsbehållarna. Om du vill aktivera utförliga loggar på de här behållarna ange `RuntimeLogLevel` i konfigurationsfilen yaml. Att öppna filen:

I Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Som standard den `agent` elementet kommer att se ut som i följande exempel:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Ersätt `env: {}` med:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-filer får inte innehålla flikar som indrag. Använd 2 blanksteg i stället.

Spara filen och starta om säkerhetshanteraren IoT Edge.

Du kan också kontrollera meddelandena som skickas mellan IoT Hub och IoT Edge-enheterna. Visa dessa meddelanden med hjälp av den [Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) -tillägget (tidigare Azure IoT Toolkit-tillägg) för Visual Studio Code. Mer information finns i [praktiskt verktyg när du utvecklar med Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Starta om behållare
När du undersöker i loggarna och meddelandena information, kan du prova att starta om behållare:

```
iotedge restart <container name>
```

Starta om körningsbehållarna IoT Edge:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Starta om IoT Edge-säkerhetshanteraren

Om problemet fortfarande beständig lagring, kan du starta om säkerhetshanteraren IoT Edge.

I Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

I Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge-agenten avslutas efter ungefär en minut

Modulen edgeAgent startar och körs under ungefär en minut innan den stoppar. Loggarna indikerar att IoT Edge-agenten försöker ansluta till IoT Hub över AMQP och försöker sedan ansluta med AMQP via WebSocket. När det misslyckas avslutas IoT Edge-agenten. 

Exempel edgeAgent loggar:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Rotorsak
En nätverkskonfiguration på värdnätverket förhindrar IoT Edge-agenten från att nå nätverket. Agenten försöker ansluta via AMQP (port 5671) först. Om anslutningen misslyckas provar agenten WebSockets (port 443).

IoT Edge-körningen ställer in ett nätverk för varje modul att kommunicera på. På Linux är nätverket en nätverksbrygga. I Windows använder den NAT. Det här problemet är vanligare på Windows-enheter som använder Windows-containrar som använder NAT-nätverket. 

### <a name="resolution"></a>Lösning
Kontrollera att det finns en väg till internet för IP-adresserna som är tilldelade till den här nätverksbryggan/NAT-nätverket. Ibland åsidosätter en VPN-konfiguration på värden IoT Edge-nätverket. 

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub inte går att starta

Modulen edgeHub misslyckas att start- och skriver ut följande meddelande till loggarna: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Rotorsak
Några andra processer på värddatorn har bundit port 443. IoT Edge hub mappar portarna 5671 och 443 för användning i gatewayscenarier. Den här portmappningen misslyckas om någon annan process redan har bundits till porten. 

### <a name="resolution"></a>Lösning
Hitta och stoppa processen som använder port 443. Den här processen är vanligtvis en webbserver.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge-agenten har inte åtkomst till en moduls avbildning (403)
En behållare kan inte köras och edgeAgent loggarna visar ett 403-fel. 

### <a name="root-cause"></a>Rotorsak
Iot Edge-agenten har inte behörighet att få åtkomst till en moduls avbildning. 

### <a name="resolution"></a>Lösning
Se till att dina autentiseringsuppgifter för registret har angetts korrekt i ditt manifest för distribution

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge security daemon misslyckas med ett ogiltigt värdnamn

Kommandot `sudo journalctl -u iotedge` misslyckas och skriver ut följande meddelande: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Rotorsak
IoT Edge-körningen stöder bara värdnamn som är kortare än 64 tecken. Fysiska datorer vanligtvis har inte lång värdnamn, men problemet är vanligare på en virtuell dator. Automatiskt genererade värdnamnen för Windows-datorerna i Azure, i synnerhet tenderar att vara långa. 

### <a name="resolution"></a>Lösning
När du ser det här felet kan lösa du det genom att konfigurera DNS-namnet på den virtuella datorn och sedan ange DNS-namn som värdnamnet i installationskommandot.

1. Gå till översiktssidan för den virtuella datorn i Azure-portalen. 
2. Välj **konfigurera** med DNS-namnet. Om den virtuella datorn redan har ett DNS-namn som har konfigurerats, behöver du inte konfigurera en ny. 

   ![Konfigurera DNS-namnet på virtuell dator](./media/troubleshoot/configure-dns.png)

3. Ange ett värde för **DNS-Namnetiketten** och välj **spara**.
4. Kopiera nya DNS-namn som ska vara i formatet  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. I den virtuella datorn använder du följande kommando du ställer in IoT Edge-körningen med DNS-namn:

   - I Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - I Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Stabilitetsproblem på resursen begränsad enheter 
Du kan stöta på instabilitet begränsad t.ex. för enheter Raspberry Pi, särskilt när det används som en gateway. Symtom är utanför minne-undantag i edge hub-modul, efterföljande enheter kan inte ansluta eller enheten slutar att skicka telemetrimeddelanden efter ett par timmar.

### <a name="root-cause"></a>Rotorsak
IoT Edge-hubben, som är en del av IoT Edge-körningen, optimeras för prestanda som standard och att allokera stora mängder minne. Denna optimering är inte idealiskt för begränsad edge-enheter och kan orsaka stabilitetsproblem med.

### <a name="resolution"></a>Lösning
Ange en miljövariabel för IoT Edge-hubben **OptimizeForPerformance** till **FALSKT**. Det finns två sätt att göra detta:

I Användargränssnittet: 

I portalen navigerar du till **enhetsinformation** > **ange moduler** > **konfigurera avancerade Edge-körningsinställningar**. Skapa en miljövariabel för modulen Edge Hub kallas *OptimizeForPerformance* som har angetts till *FALSKT*.

![OptimizeForPerformance inställd på false](./media/troubleshoot/optimizeforperformance-false.png)

**OR**

I manifestet distribution:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Det går inte att hämta IoT Edge daemon loggar på Windows
Om du får en EventLogException när du använder `Get-WinEvent` på Windows, kontrollerar du din registerposter.

### <a name="root-cause"></a>Rotorsak
Den `Get-WinEvent` PowerShell-kommandot är beroende av en registerpost för att hitta loggarna genom att en specifik `ProviderName`.

### <a name="resolution"></a>Lösning
Ange en registerpost för IoT Edge-daemon. Skapa en **iotedge.reg** filen med följande innehåll och importera i Windows-registret genom att dubbelklicka på den eller använda den `reg import iotedge.reg` kommando:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge-modulen misslyckas med att skicka ett meddelande till edgeHub med 404-fel

En anpassad IoT Edge-modulen misslyckas med att skicka ett meddelande till edgeHub med 404 `Module not found` fel. IoT Edge-daemon skriver ut följande meddelande till loggarna: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

### <a name="root-cause"></a>Rotorsak
IoT Edge-daemon framtvingar process-ID för alla moduler som ansluter till edgeHub av säkerhetsskäl. Verifierar att alla meddelanden som skickas av en modul hämtas från huvudsakliga process-ID för modulen. Om ett meddelande som skickas av en modul från en annan process-ID än först upprättas, annars avvisar meddelandet med ett 404-fel-meddelande.

### <a name="resolution"></a>Lösning
Se till att samma process-ID alltid ska användas av anpassade IoT Edge-modulen skicka meddelanden till edgeHub. Till exempel se till att `ENTRYPOINT` i stället för `CMD` kommandot i Docker-filen, eftersom `CMD` leder till en process-ID för modulen och en annan process-ID för bash-kommando som körs huvudprogrammet medan `ENTRYPOINT` leder till en enkel process-ID.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Brandväggsinställningar och portinställningar konfigurationsregler för IoT Edge-distribution
Azure IoT Edge kan kommunikation från en lokal server till Azure-molnet med IoT Hub-protokoll som stöds, se [välja ett kommunikationsprotokoll](../iot-hub/iot-hub-devguide-protocols.md). För ökad säkerhet är kommunikationskanaler mellan Azure IoT Edge och Azure IoT Hub alltid konfigurerad för att vara utgående. Den här konfigurationen baseras på den [tjänster Assisted Communication mönstret](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), vilket minimerar risken för angrepp för en skadlig enhet att utforska. Inkommande kommunikation är endast krävs för specifika scenarier där Azure IoT Hub behöver att skicka meddelanden till Azure IoT Edge-enhet. Meddelanden från moln till enhet är skyddade med säkra TLS-kanaler och ytterligare skyddas med X.509-certifikat och moduler för TPM-enhet. Azure IoT Edge Security Manager styr hur den här kommunikationen kan vara etablerade, se [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

IoT Edge innehåller förbättrad konfigurationen för att skydda Azure IoT Edge-körningen och distribuerade moduler, men det är fortfarande beroende av underliggande datorn och nätverket. Därför det är viktigt att se till att rätt nätverk och brandväggsreglerna har ställts in för säker edge till molnet kommunikation. I följande tabell kan användas som en riktlinje när konfigurationen brandväggsregler för de underliggande servrarna där Azure IoT Edge-körningen finns upp:

|Protokoll|Port|inkommande|Utgående|Riktlinjer|
|--|--|--|--|--|
|MQTT|8883|BLOCKERADE (standard)|BLOCKERADE (standard)|<ul> <li>Konfigurera utgående (utgående) för att vara öppen när du använder MQTT som kommunikationsprotokoll.<li>1883 för MQTT stöds inte av IoT Edge. <li>Inkommande (inkommande) anslutningar ska blockeras.</ul>|
|AMQP|5671|BLOCKERADE (standard)|ÖPPEN (standard)|<ul> <li>Standard kommunikationsprotokoll för IoT Edge. <li> Måste konfigureras för att vara öppen om Azure IoT Edge inte är konfigurerad för andra protokoll som stöds eller AMQP är det önskade kommunikationsprotokollet.<li>5672 för AMQP stöds inte av IoT Edge.<li>Blockera den här porten när protokoll som stöds av Azure IoT Edge använder en annan IoT-hubb.<li>Inkommande (inkommande) anslutningar ska blockeras.</ul></ul>|
|HTTPS|443|BLOCKERADE (standard)|ÖPPEN (standard)|<ul> <li>Konfigurera utgående (utgående) är öppen på 443 för IoT Edge etablering. Den här konfigurationen krävs när du använder manuella skript eller Azure IoT Device Provisioning-tjänsten (DPS). <li>Inkommande (inkommande)-anslutningen ska vara öppen endast för specifika scenarier: <ul> <li>  Om du har en transparent gateway med lövenheter som kan skicka metodbegäranden. I det här fallet behöver inte Port 443 är öppen för externa nätverk att ansluta till IoTHub eller tillhandahålla IoTHub-tjänster via Azure IoT Edge. Därför kan den inkommande regeln begränsas till endast öppna inkommande (inkommande) från det interna nätverket. <li> För klient till enheten (C2D)-scenarier.</ul><li>80 för HTTP stöds inte av IoT Edge.<li>Om icke-HTTP-protokoll (till exempel AMQP eller MQTT) inte kan konfigureras i företaget. meddelanden kan skickas via WebSockets. Port 443 används för WebSocket-kommunikation i så fall.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge-modul för agenten kontinuerligt rapporter ”tom konfigurationsfilen' och inga moduler startar på enheten

Enheten har problem med att starta moduler som anges i distributionen. Endast edgeAgent körs men kontinuerligt rapporterar '... tom konfigurationsfilen'.

### <a name="potential-root-cause"></a>Eventuella grundorsaken
Som standard startar IoT Edge moduler i sin egen isolerad behållare nätverk. Enheten kan ha problem med DNS-namnmatchning i det här privata nätverket.

### <a name="resolution"></a>Lösning

**Alternativ 1: Ange DNS-server i behållaren motorn inställningarna**

Ange DNS-server för din miljö i behållaren motorinställningar som gäller för alla behållare moduler igång av motorn. Skapa en fil med namnet `daemon.json` anger DNS-server som ska användas. Exempel:

```
{
    "dns": ["1.1.1.1"]
}
```

Exemplet ovan anger DNS-servern till en offentligt tillgänglig DNS-tjänst. Om edge-enhet inte kan komma åt denna IP-adress från dess miljö måste du ersätta det med DNS-serveradress som är tillgänglig.

Plats `daemon.json` på rätt plats för din plattform: 

| Plattform | Plats |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-värd med Windows-behållare | `C:\ProgramData\iotedge-moby-data\config` |

Om platsen innehåller redan `daemon.json` Lägg till den **dns** nyckeln till den och spara filen.

*Starta om motorn för behållaren för uppdateringarna ska börja gälla*

| Plattform | Kommando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Alternativ 2: Ange DNS-server i IoT Edge-distribution per modul**

Du kan ställa in DNS-server för varje modul *createOptions* i IoT Edge-distribution. Exempel:

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Se till att konfigurera detta för den *edgeAgent* och *edgeHub* samt moduler. 

## <a name="next-steps"></a>Nästa steg
Tror du att du har hittat ett fel i IoT Edge-plattformen? [Skicka in ett ärende](https://github.com/Azure/iotedge/issues) så att vi kan fortsätta att förbättra. 

Om du har fler frågor kan du skapa en [supportförfrågan](https://portal.azure.com/#create/Microsoft.Support) om du behöver hjälp. 

