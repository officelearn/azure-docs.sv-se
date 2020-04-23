---
title: Felsöka – Azure IoT Edge | Microsoft-dokument
description: Använd den här artikeln om du vill lära dig vanliga diagnostiska kunskaper för Azure IoT Edge, till exempel hämta komponentstatus och loggar, och lösa vanliga problem
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2e15dffac73b4a50b1ef9288feaeb6073dea91e0
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086529"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Vanliga problem och lösningar för Azure IoT Edge

Om du får problem med att köra Azure IoT Edge i din miljö kan du använda den här artikeln som en guide för felsökning och lösningar.

## <a name="run-the-iotedge-check-command"></a>Kör kommandot iotedge "check"

Ditt första steg vid felsökning av IoT `check` Edge bör vara att använda kommandot, som kör en samling konfigurations- och anslutningstester för vanliga problem. Kommandot `check` är tillgängligt i [version 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) och senare.

Du kan `check` köra kommandot på följande `--help` sätt eller inkludera flaggan om du vill visa en fullständig lista med alternativ:

* På Linux:

  ```bash
  sudo iotedge check
  ```

* I Windows:

  ```powershell
  iotedge check
  ```

Felsökningsverktyget kör många kontroller som sorteras i de följande tre kategorierna:

* Konfigurationskontroller: Undersöker information som kan hindra Edge-enheter från att ansluta till molnet, inklusive problem med *config.yaml* och behållarmotorn.
* Anslutningskontroller: Verifierar att IoT Edge-körningen kan komma åt portar på värdenheten och alla IoT Edge-komponenter kan ansluta till IoT Hub.
* Kontroller av produktionsberedskap: Söker efter rekommenderade metodtips för produktion, till exempel tillståndet för certifikatutfärdarecertifikat (Device Certificate Authority) och moduleloggfilskonfiguration.

Information om var och en av diagnostikkontrollerna som det här verktyget körs, inklusive vad du ska göra om du får ett fel eller en varning, finns i [felsökning av IoT Edge.](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Samla in felsökningsinformation med kommandot iotedge 'support-bundle'

När du behöver samla in loggar från en IoT Edge-enhet `support-bundle` är det bekvämaste sättet att använda kommandot. Som standard samlar det här kommandot in modul, IoT Edge Security Manager och containermotorloggar, "iotedge check" JSON-utdata och annan användbar felsökningsinformation. Det komprimerar dem till en enda fil för enkel delning. Kommandot `support-bundle` är tillgängligt i [version 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) och senare.

Kör `support-bundle` kommandot med `--since` flaggan för att ange hur länge från det förflutna du vill hämta loggar. Till `6h` exempel kommer att få loggar sedan `6d` de senaste 6 timmarna, sedan de senaste 6 dagarna, `6m` sedan de senaste 6 minuterna och så vidare. Inkludera `--help` flaggan om du vill visa en fullständig lista med alternativ.

* På Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* I Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> Utdata `support-bundle` från kommandot kan innehålla värd-, enhets- och modulnamn, information som loggas av dina moduler etc. Tänk på detta om du delar produktionen i ett offentligt forum.

## <a name="standard-diagnostic-steps"></a>Standarddiagnostikåtgärder

Om du stöter på ett problem kan du läsa mer om tillståndet för din IoT Edge-enhet genom att granska behållarloggarna och meddelandena som skickas till och från enheten. Använd kommandona och verktygen i det här avsnittet för att samla in information.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrollera status för IoT Edge Security Manager och dess loggar

På Linux:

* Så här visar du status för IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Så här visar du loggarna för IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Så här visar du mer detaljerade loggar för IoT Edge Security Manager:

  * Redigera inställningarna för iotedge daemon:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Uppdatera följande rader:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Starta om IoT Edge Security Daemon:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

I Windows:

* Så här visar du status för IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Så här visar du loggarna för IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Om IoT Edge Security Manager inte körs kontrollerar du konfigurationsfilen för yaml

> [!WARNING]
> YAML-filer kan inte innehålla flikar som indrag. Använd 2 blanksteg istället. Element på översta nivån bör inte ha några inledande blanksteg.

På Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Kontrollera om det finns problem med behållarloggar

När IoT Edge Security Daemon körs, titta på loggarna för behållarna för att upptäcka problem. Börja med dina distribuerade behållare och titta sedan på behållarna som utgör IoT Edge-körningen: edgeAgent och edgeHub. IoT Edge-agentloggarna ger vanligtvis information om livscykeln för varje behållare. IoT Edge-hubb loggarna innehåller information om meddelanden och routning.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Visa meddelanden som går via IoT Edge-hubben

Du kan visa meddelanden som går igenom IoT Edge-hubben och samla in insikter från utförliga loggar från körningsbehållarna. Om du vill aktivera utförliga loggar `RuntimeLogLevel` på dessa behållare anger du i yaml-konfigurationsfilen. Så här öppnar du filen:

På Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Som standard `agent` ser elementet ut som följande exempel:

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
   > YAML-filer kan inte innehålla flikar som identifiering. Använd 2 blanksteg istället. Objekt på den översta nivån kan inte ha inledande blanktecken.

Spara filen och starta om säkerhetshanteraren för IoT Edge.

Du kan också kontrollera meddelandena som skickas mellan IoT Hub och IoT Edge-enheterna. Visa dessa meddelanden med hjälp av [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Mer information finns i [Praktiskt verktyg när du utvecklar med Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Starta om behållare

När du har undersökt loggar och meddelanden för information kan du prova att starta om behållare:

```cmd
iotedge restart <container name>
```

Starta om IoT Edge-körningsbehållarna:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Starta om säkerhetshanteraren för IoT Edge

Om problemet kvarstår kan du prova att starta om säkerhetshanteraren för IoT Edge.

På Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

I Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge-agenten stannar efter ungefär en minut

EdgeAgent-modulen startar och körs i ungefär en minut och stannar sedan. Loggarna anger att IoT Edge-agenten försöker ansluta till IoT Hub över AMQP och sedan försöker ansluta med AMQP via WebSocket. När det misslyckas avslutas IoT Edge-agenten.

Exempel edgeAgent loggar:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Rotorsak**

En nätverkskonfiguration i värdnätverket hindrar IoT Edge-agenten från att nå nätverket. Agenten försöker ansluta via AMQP (port 5671) först. Om anslutningen misslyckas försöker den WebSockets (port 443).

IoT Edge-körningen ställer in ett nätverk för varje modul att kommunicera på. På Linux är nätverket en nätverksbrygga. I Windows använder den NAT. Det här problemet är vanligare på Windows-enheter som använder Windows-containrar som använder NAT-nätverket.

**Upplösning**

Kontrollera att det finns en väg till internet för IP-adresserna som är tilldelade till den här nätverksbryggan/NAT-nätverket. Ibland åsidosätter en VPN-konfiguration på värden IoT Edge-nätverket.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge-hubben startar inte

EdgeHub-modulen startar inte och skriver ut följande meddelande till loggarna:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Rotorsak**

Några andra processer på värddatorn har bundit port 443. IoT Edge-hubben mappar portar 5671 och 443 för användning i gateway-scenarier. Den här portmappningen misslyckas om någon annan process redan har bundits till porten.

**Upplösning**

Hitta och stoppa processen som använder port 443. Den här processen är vanligtvis en webbserver.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge-agenten kan inte komma åt en moduls bild (403)

En behållare kan inte köras och edgeAgent-loggarna visar ett 403-fel.

**Rotorsak**

IoT Edge-agenten har inte behörighet att komma åt en moduls avbildning.

**Upplösning**

Kontrollera att registerautentiseringsuppgifterna har angetts korrekt i distributionsmanifestet

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge-säkerhetsdemon misslyckas med ett ogiltigt värdnamn

Kommandot `sudo journalctl -u iotedge` misslyckas och skriver ut följande meddelande:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Rotorsak**

IoT Edge-körningen kan bara stödja värdnamn som är kortare än 64 tecken. Fysiska datorer har vanligtvis inte långa värdnamn, men problemet är vanligare på en virtuell dator. De automatiskt genererade värdnamnen för virtuella Windows-datorer som finns i Azure, i synnerhet, tenderar att vara långa.

**Upplösning**

När du ser det här felet kan du lösa det genom att konfigurera DNS-namnet på den virtuella datorn och sedan ange DNS-namnet som värdnamn i installationskommandot.

1. I Azure-portalen navigerar du till översiktssidan för din virtuella dator.
2. Välj **konfigurera** under DNS-namn. Om den virtuella datorn redan har konfigurerat ett DNS-namn behöver du inte konfigurera ett nytt.

   ![Konfigurera DNS-namn på virtuell dator](./media/troubleshoot/configure-dns.png)

3. Ange ett värde för **DNS-namnetiketten** och välj **Spara**.
4. Kopiera det nya DNS-namnet, som ska vara i formatet ** \<\>DNSnamelabel\< . vmlocation\>.cloudapp.azure.com**.
5. Inuti den virtuella datorn använder du följande kommando för att ställa in IoT Edge-körningen med ditt DNS-namn:

   * På Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * I Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Stabilitetsproblem på resursbegränsade enheter

Du kan stöta på stabilitetsproblem på begränsade enheter som Raspberry Pi, särskilt när de används som en gateway. Symptomen inkluderar utanför minnesundantag i kantnavet modulen, nedströms enheter kan inte ansluta eller enheten slutar skicka telemetrimeddelanden efter några timmar.

**Rotorsak**

IoT Edge-hubben, som är en del av IoT Edge-körningen, är optimerad för prestanda som standard och försöker allokera stora minnesbitar. Den här optimeringen är inte idealisk för begränsade kantenheter och kan orsaka stabilitetsproblem.

**Upplösning**

För IoT Edge-hubben anger du en miljövariabel **OptimizeForPerformance** till **false**. Det finns två sätt att ställa in miljövariabler:

På Azure Portal:

I IoT-hubben väljer du din IoT Edge-enhet och från sidan med enhetsinformation och väljer **Ange körtidsinställningar** > **för**moduler . Skapa en miljövariabel för Edge Hub-modulen *OptimizeForPerformance* som är inställd på *false*.

![OptimizeForPerformance inställd på false](./media/troubleshoot/optimizeforperformance-false.png)

**Eller**

I distributionsmanifestet:

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

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Kan inte få IoT Edge daemon loggar på Windows

Om du får en EventLogException när du använder i `Get-WinEvent` Windows kontrollerar du registerposterna.

**Rotorsak**

PowerShell-kommandot `Get-WinEvent` är beroende av att en registerpost finns för `ProviderName`att hitta loggar av en specifik .

**Upplösning**

Ange en registerpost för IoT Edge-demonen. Skapa en **iotedge.reg-fil** med följande innehåll och importera till Windows-registret genom `reg import iotedge.reg` att dubbelklicka på den eller använda kommandot:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge-modulen kan inte skicka ett meddelande till edgeHub med 404 fel

En anpassad IoT Edge-modul kan inte skicka ett meddelande `Module not found` till edgeHub med ett 404-fel. IoT Edge-demonen skriver ut följande meddelande till loggarna:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Rotorsak**

IoT Edge-demonen tillämpar processidentifiering för alla moduler som ansluter till edgeHub av säkerhetsskäl. Den verifierar att alla meddelanden som skickas av en modul kommer från modulens huvudsakliga process-ID. Om ett meddelande skickas av en modul från ett annat process-ID än vad som ursprungligen fastställdes, avvisas meddelandet med ett 404-felmeddelande.

**Upplösning**

Från och med version 1.0.7 är alla modulprocesser auktoriserade att ansluta. Om det inte går att uppgradera till 1.0.7 gör du följande steg. Mer information finns i [1.0.7 release changelog](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Kontrollera att samma process-ID alltid används av den anpassade IoT Edge-modulen för att skicka meddelanden till edgeHub. Se till exempel `ENTRYPOINT` till `CMD` att i stället för `CMD` kommandot i Docker-filen, eftersom kommer att leda till ett process-ID för modulen och ett annat process-ID för bash kommandot kör huvudprogrammet medan `ENTRYPOINT` kommer att leda till ett enda process-ID.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Konfigurationsregler för brandvägg och port för IoT Edge-distribution

Azure IoT Edge tillåter kommunikation från en lokal server till Azure-moln med hjälp av IoT Hub-protokoll som stöds och se [välja ett kommunikationsprotokoll](../iot-hub/iot-hub-devguide-protocols.md). För förbättrad säkerhet är kommunikationskanaler mellan Azure IoT Edge och Azure IoT Hub alltid konfigurerade för att vara utgående. Den här konfigurationen baseras på [mönstret Assisterad kommunikation för tjänster](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), vilket minimerar angreppsytan för en skadlig entitet att utforska. Inkommande kommunikation krävs endast för specifika scenarier där Azure IoT Hub behöver skicka meddelanden till Azure IoT Edge-enheten. Meddelanden från molnet till enheten är skyddade med säkra TLS-kanaler och kan säkras ytterligare med X.509-certifikat och TPM-enhetsmoduler. Azure IoT Edge Security Manager styr hur den här kommunikationen kan upprättas, se [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

IoT Edge ger förbättrad konfiguration för att skydda Azure IoT Edge-körning och distribuerade moduler, men den är fortfarande beroende av den underliggande datorn och nätverkskonfigurationen. Därför är det absolut nödvändigt att se till att rätt nätverks- och brandväggsregler har ställts in för säker kant till molnkommunikation. Följande tabell kan användas som en riktlinje när konfigurationsbrandväggsregler för underliggande servrar där Azure IoT Edge-körningen finns:

|Protokoll|Port|Inkommande|Utgående|Riktlinjer|
|--|--|--|--|--|
|MQTT|8883|BLOCKERAD (Standard)|BLOCKERAD (Standard)|<ul> <li>Konfigurera Utgående (utgående) så att det är öppet när MQTT används som kommunikationsprotokoll.<li>1883 för MQTT stöds inte av IoT Edge. <li>Inkommande (inkommande) anslutningar ska blockeras.</ul>|
|AMQP|5671|BLOCKERAD (Standard)|ÖPPEN (standard)|<ul> <li>Standardkommunikationsprotokoll för IoT Edge. <li> Måste konfigureras för att vara Öppen om Azure IoT Edge inte är konfigurerad för andra protokoll som stöds eller amqp är det önskade kommunikationsprotokollet.<li>5672 för AMQP stöds inte av IoT Edge.<li>Blockera den här porten när Azure IoT Edge använder ett annat IoT Hub-protokoll som stöds.<li>Inkommande (inkommande) anslutningar ska blockeras.</ul></ul>|
|HTTPS|443|BLOCKERAD (Standard)|ÖPPEN (standard)|<ul> <li>Konfigurera utgående (utgående) så att det är öppet på 443 för IoT Edge-etablering. Den här konfigurationen krävs när du använder manuella skript eller DPS (Azure IoT Device Provisioning Service). <li>Inkommande (inkommande) anslutning bör endast vara öppen för specifika scenarier: <ul> <li>  Om du har en transparent gateway med lövenheter som kan skicka metodbegäranden. I det här fallet behöver port 443 inte vara öppen för externa nätverk för att ansluta till IoTHub eller tillhandahålla IoTHub-tjänster via Azure IoT Edge. Således kan den inkommande regeln begränsas till att endast öppna Inkommande (Inkommande) från det interna nätverket. <li> För C2D-scenarier (Client to Device).</ul><li>80 för HTTP stöds inte av IoT Edge.<li>Om icke-HTTP-protokoll (till exempel AMQP eller MQTT) inte kan konfigureras i företaget. meddelandena kan skickas via WebSockets. Port 443 kommer att användas för WebSocket-kommunikation i så fall.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge Agent-modulen rapporterar kontinuerligt "tom config-fil" och inga moduler startar på enheten

Enheten har problem med att starta moduler som definierats i distributionen. Endast edgeAgent körs men kontinuerligt rapporterar "tom config-fil...".

**Rotorsak**

Som standard startar IoT Edge moduler i sitt eget isolerade behållarnätverk. Enheten kan ha problem med DNS-namnmatchning i det här privata nätverket.

**Upplösning**

**Alternativ 1: Ange DNS-server i inställningar för behållarmotor**

Ange DNS-servern för din miljö i behållarmotorinställningarna, som gäller för alla behållarmoduler som startas av motorn. Skapa en `daemon.json` fil med namnet som anger den DNS-server som ska användas. Ett exempel:

```json
{
    "dns": ["1.1.1.1"]
}
```

I exemplet ovan anges DNS-servern på en DNS-tjänst som är allmänt tillgänglig. Om kantenheten inte kan komma åt den här IP-adressen från sin miljö ersätter du den med DNS-serveradress som är tillgänglig.

Placera `daemon.json` på rätt plats för din plattform:

| Plattform | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-värd med Windows-behållare | `C:\ProgramData\iotedge-moby\config` |

Om platsen redan `daemon.json` innehåller filen lägger du till **dns-nyckeln** i den och sparar filen.

Starta om behållarmotorn så att uppdateringarna börjar gälla.

| Plattform | Kommando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Alternativ 2: Ange DNS-server i IoT Edge-distribution per modul**

Du kan ange DNS-server för varje moduls *createOptions* i IoT Edge-distributionen. Ett exempel:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Var noga med att ställa in den här konfigurationen för *edgeAgent* och *edgeHub* moduler också.

## <a name="next-steps"></a>Nästa steg

Tror du att du har hittat ett fel i IoT Edge-plattformen? [Skicka in ett problem](https://github.com/Azure/iotedge/issues) så att vi kan fortsätta att förbättra.

Om du har fler frågor skapar du en [supportförfrågan om](https://portal.azure.com/#create/Microsoft.Support) hjälp.
