---
title: Felsökning – Azure IoT Edge | Microsoft Docs
description: Använd den här artikeln lär du dig standard diagnostiska färdigheter för Azure IoT Edge, som hämtar Komponentstatus och loggar och lösa vanliga problem
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 93e3a5ed442c975f75045d86d6b890ee4113c465
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514263"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Vanliga problem och lösningar för Azure IoT Edge

Om du får problem med att köra Azure IoT Edge i din miljö kan du använda den här artikeln som en guide för felsökning och lösningar.

## <a name="run-the-iotedge-check-command"></a>Kör kommandot iotedge ' check '

Ditt första steg när du felsöker IoT Edge bör vara att använda kommandot `check`, som utför en samling konfigurations-och anslutnings test för vanliga problem. Kommandot `check` är tillgängligt i [version 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) och senare.

Du kan köra kommandot `check` på följande sätt, eller ta med flaggan `--help` för att se en fullständig lista med alternativ:

* I Linux:

  ```bash
  sudo iotedge check
  ```

* I Windows:

  ```powershell
  iotedge check
  ```

Typerna av kontroller som körs av verktyget kan klassificeras som:

* Konfigurations kontroller: granskar information som kan förhindra att Edge-enheter ansluter till molnet, inklusive problem med *config. yaml* och behållar motorn.
* Anslutnings kontroller: verifierar att IoT Edge runtime kan komma åt portar på värd enheten och alla IoT Edge-komponenter kan ansluta till IoT Hub.
* Kontroll av produktions beredskap: söker efter rekommenderade produktions metoder, t. ex. tillstånd för certifikat från enhetens certifikat utfärdare och modulens logg fils konfiguration.

En fullständig lista över diagnostiska kontroller finns i [inbyggda fel söknings funktioner](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Standarddiagnostikåtgärder

Om du stöter på ett problem kan du lära dig mer om status för din IoT Edge-enhet genom att granska behållar loggarna och de meddelanden som skickas till och från enheten. Använd kommandona och verktygen i det här avsnittet för att samla in information.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrol lera status för IoT Edge Security Manager och dess loggar

I Linux:

* Visa status för IoT Edge-Säkerhetshanteraren:

   ```bash
   sudo systemctl status iotedge
   ```

* Visa loggar av IoT Edge-Säkerhetshanteraren:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Visa mer detaljerad loggar av IoT Edge-Säkerhetshanteraren:

  * Redigera inställningar för iotedge-daemon:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Uppdatera följande rader:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Starta om IoT Edge-Security-Daemon:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

I Windows:

* Visa status för IoT Edge-Säkerhetshanteraren:

   ```powershell
   Get-Service iotedge
   ```

* Visa loggar av IoT Edge-Säkerhetshanteraren:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Om IoT Edge Security Manager inte körs, kontrollera din yaml-konfigurationsfil

> [!WARNING]
> YAML-filer får inte innehålla tabbar som indrag. Använd 2 blanksteg i stället. Element på den översta nivån får inte innehålla några inledande blank steg.

I Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Kontrollera behållarloggarna för problem

När IoT Edge Security Daemon körs kan du titta på loggarna för behållarna för att identifiera problem. Börja med dina distribuerade behållare och titta sedan på de behållare som utgör den IoT Edge körningen: edgeAgent och edgeHub. IoT Edge agent loggar ger vanligt vis information om livs cykeln för varje behållare. IoT Edge Hubbs loggar innehåller information om meddelanden och routning.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Visa meddelanden som går via IoT Edge Hub

Du kan visa meddelanden som går via IoT Edge hubben och samla in insikter från utförliga loggar från runtime-behållare. Om du vill aktivera utförliga loggar på de här behållarna ange `RuntimeLogLevel` i konfigurationsfilen yaml. Att öppna filen:

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
   > YAML-filer får inte innehålla flikar som indrag. Använd 2 blanksteg i stället. Toppnivå objekt får inte ha inledande blank steg.

Spara filen och starta om säkerhetshanteraren IoT Edge.

Du kan också kontrollera meddelandena som skickas mellan IoT Hub och IoT Edge-enheterna. Visa dessa meddelanden med hjälp av [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Mer information finns i [praktiskt verktyg när du utvecklar med Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Starta om behållare

När du undersöker i loggarna och meddelandena information, kan du prova att starta om behållare:

```cmd
iotedge restart <container name>
```

Starta om körningsbehållarna IoT Edge:

```cmd
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

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agenten stoppas efter en minut

EdgeAgent-modulen startar och körs för ungefär en minut och stoppas sedan. Loggarna indikerar att IoT Edge-agenten försöker ansluta till IoT Hub över AMQP och försöker sedan ansluta med AMQP över WebSocket. När det Miss lyckas avslutas IoT Edge agenten.

Exempel på edgeAgent-loggar:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Rotor saken**

En nätverks konfiguration på värd nätverket hindrar IoT Edge-agenten från att nå nätverket. Agenten försöker ansluta via AMQP (port 5671) först. Om anslutningen misslyckas provar agenten WebSockets (port 443).

IoT Edge-körningen ställer in ett nätverk för varje modul att kommunicera på. På Linux är nätverket en nätverksbrygga. I Windows använder den NAT. Det här problemet är vanligare på Windows-enheter som använder Windows-containrar som använder NAT-nätverket.

**Lösning**

Kontrollera att det finns en väg till internet för IP-adresserna som är tilldelade till den här nätverksbryggan/NAT-nätverket. Ibland åsidosätter en VPN-konfiguration på värden IoT Edge-nätverket.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hubben kan inte startas

EdgeHub-modulen kan inte startas och skriver ut följande meddelande till loggarna:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Rotor saken**

Några andra processer på värddatorn har bundit port 443. IoT Edge Hub mappar portarna 5671 och 443 för användning i Gateway-scenarier. Den här portmappningen misslyckas om någon annan process redan har bundits till porten.

**Lösning**

Hitta och stoppa processen som använder port 443. Den här processen är vanligtvis en webbserver.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agenten har inte åtkomst till en moduls avbildning (403)

Det går inte att köra en behållare och edgeAgent-loggarna visar ett 403-fel.

**Rotor saken**

IoT Edge agent har inte behörighet att komma åt en moduls avbildning.

**Lösning**

Se till att dina autentiseringsuppgifter för registret har angetts korrekt i ditt manifest för distribution

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge security daemon misslyckas med ett ogiltigt värdnamn

Kommandot `sudo journalctl -u iotedge` misslyckas och skriver ut följande meddelande:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Rotor saken**

IoT Edge-körningen stöder bara värdnamn som är kortare än 64 tecken. Fysiska datorer vanligtvis har inte lång värdnamn, men problemet är vanligare på en virtuell dator. Automatiskt genererade värdnamnen för Windows-datorerna i Azure, i synnerhet tenderar att vara långa. 

**Lösning**

När du ser det här felet kan lösa du det genom att konfigurera DNS-namnet på den virtuella datorn och sedan ange DNS-namn som värdnamnet i installationskommandot.

1. Gå till översiktssidan för den virtuella datorn i Azure-portalen.
2. Välj **konfigurera** med DNS-namnet. Om den virtuella datorn redan har ett DNS-namn som har konfigurerats, behöver du inte konfigurera en ny.

   ![Konfigurera DNS-namnet på virtuell dator](./media/troubleshoot/configure-dns.png)

3. Ange ett värde för **DNS-Namnetiketten** och välj **spara**.
4. Kopiera nya DNS-namn som ska vara i formatet  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. I den virtuella datorn använder du följande kommando du ställer in IoT Edge-körningen med DNS-namn:

   * I Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * I Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Stabilitetsproblem på resursen begränsad enheter

Du kan stöta på instabilitet begränsad t.ex. för enheter Raspberry Pi, särskilt när det används som en gateway. Symtom är utanför minne-undantag i edge hub-modul, efterföljande enheter kan inte ansluta eller enheten slutar att skicka telemetrimeddelanden efter ett par timmar.

**Rotor saken**

IoT Edge Hub, som är en del av IoT Edge runtime, är optimerad för prestanda som standard och försöker allokera stora mängder minne. Denna optimering är inte idealiskt för begränsad edge-enheter och kan orsaka stabilitetsproblem med.

**Lösning**

För IoT Edge Hub ställer du in en miljö variabel **OptimizeForPerformance** på **falskt**. Det finns två sätt att göra detta:

På Azure Portal:

I IoT Hub väljer du IoT Edge enheten och på sidan enhets information och väljer **Ange moduler** > **körnings inställningar**. Skapa en miljö variabel för Edge Hub-modulen med namnet *OptimizeForPerformance* som har angetts till *false*.

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

**Rotor saken**

Den `Get-WinEvent` PowerShell-kommandot är beroende av en registerpost för att hitta loggarna genom att en specifik `ProviderName`.

**Lösning**

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

**Rotor saken**

IoT Edge-daemon framtvingar process-ID för alla moduler som ansluter till edgeHub av säkerhetsskäl. Verifierar att alla meddelanden som skickas av en modul hämtas från huvudsakliga process-ID för modulen. Om ett meddelande som skickas av en modul från en annan process-ID än först upprättas, annars avvisar meddelandet med ett 404-fel-meddelande.

**Lösning**

Från och med version 1.0.7 har alla modulblad behörighet att ansluta. Om det inte går att uppgradera till 1.0.7 utför du följande steg. Mer information finns i [1.0.7 Release ändringsloggen](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Se till att samma process-ID alltid ska användas av anpassade IoT Edge-modulen skicka meddelanden till edgeHub. Till exempel, se till att `ENTRYPOINT` i stället för `CMD` kommando i Docker-filen, eftersom `CMD` leder till ett process-ID för modulen och ett annat process-ID för bash-kommandot som kör huvud programmet, medan `ENTRYPOINT` leder till ett enda process-ID.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Brandväggsinställningar och portinställningar konfigurationsregler för IoT Edge-distribution

Azure IoT Edge tillåter kommunikation från en lokal server till Azure-molnet med IoT Hub protokoll som stöds, se [välja ett kommunikations protokoll](../iot-hub/iot-hub-devguide-protocols.md). För ökad säkerhet är kommunikationskanaler mellan Azure IoT Edge och Azure IoT Hub alltid konfigurerad för att vara utgående. Den här konfigurationen baseras på den [tjänster Assisted Communication mönstret](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), vilket minimerar risken för angrepp för en skadlig enhet att utforska. Inkommande kommunikation är endast krävs för specifika scenarier där Azure IoT Hub behöver att skicka meddelanden till Azure IoT Edge-enhet. Meddelanden från moln till enhet är skyddade med säkra TLS-kanaler och ytterligare skyddas med X.509-certifikat och moduler för TPM-enhet. Azure IoT Edge Security Manager styr hur den här kommunikationen kan vara etablerade, se [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

IoT Edge innehåller förbättrad konfigurationen för att skydda Azure IoT Edge-körningen och distribuerade moduler, men det är fortfarande beroende av underliggande datorn och nätverket. Därför är det absolut nödvändigt att se till att rätt nätverks-och brand Väggs regler har kon figurer ATS för säker Edge till moln kommunikation. Följande tabell kan användas som en rikt linje när du konfigurerar brand Väggs regler för de underliggande servrarna där Azure IoT Edge runtime finns:

|Protokoll|Port|inkommande|Utgående|Vägledning|
|--|--|--|--|--|
|MQTT|8883|BLOCKERADE (standard)|BLOCKERADE (standard)|<ul> <li>Konfigurera utgående (utgående) för att vara öppen när du använder MQTT som kommunikationsprotokoll.<li>1883 för MQTT stöds inte av IoT Edge. <li>Inkommande (inkommande) anslutningar ska blockeras.</ul>|
|AMQP|5671|BLOCKERADE (standard)|ÖPPEN (standard)|<ul> <li>Standard kommunikationsprotokoll för IoT Edge. <li> Måste konfigureras för att vara öppen om Azure IoT Edge inte är konfigurerad för andra protokoll som stöds eller AMQP är det önskade kommunikationsprotokollet.<li>5672 för AMQP stöds inte av IoT Edge.<li>Blockera den här porten när protokoll som stöds av Azure IoT Edge använder en annan IoT-hubb.<li>Inkommande (inkommande) anslutningar ska blockeras.</ul></ul>|
|HTTPS|443|BLOCKERADE (standard)|ÖPPEN (standard)|<ul> <li>Konfigurera utgående (utgående) är öppen på 443 för IoT Edge etablering. Den här konfigurationen krävs när du använder manuella skript eller Azure IoT Device Provisioning-tjänsten (DPS). <li>Inkommande (inkommande)-anslutningen ska vara öppen endast för specifika scenarier: <ul> <li>  Om du har en transparent gateway med lövenheter som kan skicka metodbegäranden. I det här fallet behöver inte Port 443 är öppen för externa nätverk att ansluta till IoTHub eller tillhandahålla IoTHub-tjänster via Azure IoT Edge. Därför kan den inkommande regeln begränsas till endast öppna inkommande (inkommande) från det interna nätverket. <li> För klient till enheten (C2D)-scenarier.</ul><li>80 för HTTP stöds inte av IoT Edge.<li>Om icke-HTTP-protokoll (till exempel AMQP eller MQTT) inte kan konfigureras i företaget. meddelanden kan skickas via WebSockets. Port 443 används för WebSocket-kommunikation i så fall.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge agent module rapporterar kontinuerligt den tomma config-filen och inga moduler startar på enheten

Enheten har problem med att starta moduler som definierats i distributionen. Endast edgeAgent körs men löpande rapportering av Tom config-fil...

**Rotor saken**

Som standard startar IoT Edge moduler i egna isolerade behållar nätverk. Enheten kan ha problem med DNS-namnmatchning i det här privata nätverket.

**Lösning**

**Alternativ 1: Ange DNS-server i behållar motor inställningarna**

Ange DNS-servern för din miljö i behållar motor inställningarna som ska gälla för alla behållar moduler som startas av motorn. Skapa en fil med namnet `daemon.json` ange vilken DNS-server som ska användas. Ett exempel:

```json
{
    "dns": ["1.1.1.1"]
}
```

Exemplet ovan anger DNS-servern till en offentligt tillgänglig DNS-tjänst. Om gräns enheten inte kan komma åt den här IP-adressen från dess miljö ersätter du den med den DNS-serveradress som är tillgänglig.

Placera `daemon.json` på rätt plats för din plattform: 

| Plattform | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-värd med Windows-behållare | `C:\ProgramData\iotedge-moby\config` |

Om platsen redan innehåller `daemon.json`-filen lägger du till **DNS-** nyckeln till den och sparar filen.

*Starta om behållar motorn för att uppdateringarna ska börja gälla*

| Plattform | Kommando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (admin PowerShell) | `Restart-Service iotedge-moby -Force` |

**Alternativ 2: Ange DNS-server i IoT Edge distribution per modul**

Du kan ställa in DNS-servern för varje moduls *createOptions* i IoT Edge distributionen. Ett exempel:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Se till att ange detta för *edgeAgent* -och *edgeHub* -modulerna också.

## <a name="next-steps"></a>Nästa steg

Tror du att du har hittat ett fel i IoT Edge-plattformen? [Skicka in ett ärende](https://github.com/Azure/iotedge/issues) så att vi kan fortsätta att förbättra.

Om du har fler frågor kan du skapa en [supportförfrågan](https://portal.azure.com/#create/Microsoft.Support) om du behöver hjälp.
