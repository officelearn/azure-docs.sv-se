---
title: Felsök – Azure IoT Edge | Microsoft Docs
description: Använd den här artikeln för att lära dig om diagnostiska färdigheter för Azure IoT Edge, till exempel att hämta komponent status och loggar och lösa vanliga problem
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086529"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Vanliga problem och lösningar för Azure IoT Edge

Om du får problem med att köra Azure IoT Edge i din miljö kan du använda den här artikeln som en guide för felsökning och lösningar.

## <a name="run-the-iotedge-check-command"></a>Kör kommandot iotedge ' check '

Ditt första steg när du felsöker IoT Edge bör vara att `check` använda kommandot, som kör en samling konfigurations-och anslutnings test för vanliga problem. `check` Kommandot är tillgängligt i [version 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) och senare.

Du kan köra `check` kommandot på följande sätt, eller inkludera `--help` flaggan om du vill se en fullständig lista med alternativ:

* I Linux:

  ```bash
  sudo iotedge check
  ```

* I Windows:

  ```powershell
  iotedge check
  ```

Fel söknings verktyget kör många kontroller som är sorterade i följande tre kategorier:

* Konfigurations kontroller: granskar information som kan förhindra att Edge-enheter ansluter till molnet, inklusive problem med *config. yaml* och behållar motorn.
* Anslutnings kontroller: verifierar att IoT Edge runtime kan komma åt portar på värd enheten och alla IoT Edge-komponenter kan ansluta till IoT Hub.
* Kontroll av produktions beredskap: söker efter rekommenderade produktions metoder, t. ex. tillstånd för certifikat från enhetens certifikat utfärdare och modulens logg fils konfiguration.

Information om var och en av de diagnostiska kontroller som utförs av verktyget, inklusive vad du ska göra om du får ett fel eller en varning, finns i [IoT Edge Felsök checkar](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Samla in felsöknings information med kommandot iotedge support-bunt

När du behöver samla in loggar från en IoT Edge enhet är det enklaste sättet att använda `support-bundle` kommandot. Som standard samlar detta kommando in modul, IoT Edge Security Manager-och container Engine-loggar, "iotedge check" JSON-utdata och annan användbar felsöknings information. Den komprimerar dem till en enda fil för enkel delning. `support-bundle` Kommandot är tillgängligt i [version 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) och senare.

Kör `support-bundle` kommandot med `--since` flaggan för att ange hur lång tid från det förflutna du vill hämta loggar. Till exempel `6h` får loggar sedan de senaste 6 timmarna, `6d` sedan de senaste 6 dagarna, `6m` sedan de senaste 6 minuterna och så vidare. Ta med `--help` flaggan om du vill se en fullständig lista med alternativ.

* I Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* I Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> Utdata från `support-bundle` kommandot kan innehålla värden, enhets-och Modulnamn, information som loggas av dina moduler osv. Tänk på detta om du delar utdata i ett offentligt forum.

## <a name="standard-diagnostic-steps"></a>Standarddiagnostikåtgärder

Om du stöter på ett problem kan du lära dig mer om status för din IoT Edge-enhet genom att granska behållar loggarna och de meddelanden som skickas till och från enheten. Använd kommandona och verktygen i det här avsnittet för att samla in information.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrol lera status för IoT Edge Security Manager och dess loggar

I Linux:

* Så här visar du status för IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Så här visar du loggarna för IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* För att visa mer detaljerade loggar av IoT Edge Security Manager:

  * Redigera daemon-inställningarna för iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Uppdatera följande rader:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Starta om IoT Edge Security daemon:

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

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Om IoT Edge Security Manager inte körs kontrollerar du konfigurations filen för yaml

> [!WARNING]
> YAML-filer får inte innehålla tabbar som indrag. Använd 2 Blank steg i stället. Element på den översta nivån får inte innehålla några inledande blank steg.

I Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Kontrol lera behållar loggar för problem

När IoT Edge Security Daemon körs tittar du på loggarna för behållarna för att identifiera problem. Börja med dina distribuerade behållare och titta sedan på de behållare som utgör den IoT Edge körningen: edgeAgent och edgeHub. IoT Edge agent loggar ger vanligt vis information om livs cykeln för varje behållare. IoT Edge Hubbs loggar innehåller information om meddelanden och routning.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Visa meddelanden som går via IoT Edge Hub

Du kan visa meddelanden som går via IoT Edge hubben och samla in insikter från utförliga loggar från runtime-behållare. Om du vill aktivera utförliga loggar på de här behållarna anger `RuntimeLogLevel` du konfigurations filen för yaml. Så här öppnar du filen:

I Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Som standard ser `agent` elementet ut som i följande exempel:

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
   > YAML-filer får inte innehålla flikar som identitet. Använd 2 Blank steg i stället. Toppnivå objekt får inte ha inledande blank steg.

Spara filen och starta om IoT Edge Security Manager.

Du kan också kontrollera meddelandena som skickas mellan IoT Hub och IoT Edge-enheterna. Visa dessa meddelanden med hjälp av [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Mer information finns i [användbart verktyg när du utvecklar med Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Starta om behållare

När du har undersökt loggarna och meddelandena för information kan du prova att starta om behållare:

```cmd
iotedge restart <container name>
```

Starta om IoT Edge runtime-behållare:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Starta om IoT Edge Security Manager

Om problemet fortfarande kvarstår kan du prova att starta om IoT Edge Security Manager.

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

**Rotorsak**

En nätverks konfiguration på värd nätverket hindrar IoT Edge-agenten från att nå nätverket. Agenten försöker ansluta via AMQP (port 5671) först. Om anslutningen Miss lyckas försöker den WebSockets (port 443).

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

**Rotorsak**

Några andra processer på värddatorn har bundit port 443. IoT Edge Hub mappar portarna 5671 och 443 för användning i Gateway-scenarier. Den här portmappningen misslyckas om någon annan process redan har bundits till porten.

**Lösning**

Hitta och stoppa processen som använder port 443. Den här processen är vanligtvis en webbserver.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agenten har inte åtkomst till en moduls avbildning (403)

Det går inte att köra en behållare och edgeAgent-loggarna visar ett 403-fel.

**Rotorsak**

IoT Edge agent har inte behörighet att komma åt en moduls avbildning.

**Lösning**

Kontrol lera att autentiseringsuppgifterna för registret är korrekt angivna i distributions manifestet

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge Security daemon Miss lyckas med ett ogiltigt värdnamn

Kommandot `sudo journalctl -u iotedge` Miss lyckas och skriver ut följande meddelande:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Rotorsak**

IoT Edge runtime kan bara stödja värdnamn som är kortare än 64 tecken. Fysiska datorer har vanligt vis inte långa värdnamn, men problemet är vanligare på en virtuell dator. Automatiskt genererade värdnamn för virtuella Windows-datorer som är värdbaserade i Azure, i synnerhet tenderar att vara långa.

**Lösning**

När du ser det här felet kan du lösa det genom att konfigurera den virtuella datorns DNS-namn och sedan ange DNS-namnet som värdnamn i installations kommandot.

1. I Azure Portal går du till översikts sidan för den virtuella datorn.
2. Välj **Konfigurera** under DNS-namn. Om du redan har konfigurerat ett DNS-namn på den virtuella datorn behöver du inte konfigurera någon ny.

   ![Konfigurera DNS-namnet för den virtuella datorn](./media/troubleshoot/configure-dns.png)

3. Ange ett värde för **DNS-namn etikett** och välj **Spara**.
4. Kopiera det nya DNS-namnet, vilket ska vara i formatet ** \<DNSnamelabel\>.\< vmlocation\>. cloudapp.Azure.com**.
5. I den virtuella datorn använder du följande kommando för att konfigurera IoT Edge runtime med ditt DNS-namn:

   * I Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * I Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Stabilitets problem på resurs begränsade enheter

Du kan stöta på stabilitets problem på begränsade enheter som Raspberry Pi, särskilt när de används som en gateway. Symptom innehåller minnes undantag i Edge Hub-modulen, underordnade enheter kan inte ansluta eller så slutar enheten att skicka telemetri meddelanden efter några timmar.

**Rotorsak**

IoT Edge Hub, som är en del av IoT Edge runtime, är optimerad för prestanda som standard och försöker allokera stora mängder minne. Den här optimeringen är inte idealisk för begränsade gräns enheter och kan orsaka stabilitets problem.

**Lösning**

För IoT Edge Hub ställer du in en miljö variabel **OptimizeForPerformance** på **falskt**. Det finns två sätt att ange miljövariabler:

På Azure Portal:

I IoT Hub väljer du IoT Edge enheten och på sidan enhets information och väljer **Ange** > **Inställningar**för moduler. Skapa en miljö variabel för Edge Hub-modulen med namnet *OptimizeForPerformance* som har angetts till *false*.

![OptimizeForPerformance har angetts till false](./media/troubleshoot/optimizeforperformance-false.png)

**ELLER**

I distributions manifestet:

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

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Det går inte att hämta IoT Edge daemon-loggar i Windows

Om du får en EventLogException när du `Get-WinEvent` använder i Windows kontrollerar du register posterna.

**Rotorsak**

`Get-WinEvent` PowerShell-kommandot är beroende av att en register post är tillgänglig för att hitta loggar med en `ProviderName`speciell.

**Lösning**

Ange en register post för IoT Edge daemon. Skapa en **iotedge. reg** -fil med följande innehåll och importera den till Windows-registret genom att dubbelklicka på den eller använda `reg import iotedge.reg` kommandot:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge modul kan inte skicka ett meddelande till edgeHub med 404-fel

En anpassad IoT Edge-modul kan inte skicka ett meddelande till edgeHub med ett 404 `Module not found` -fel. IoT Edge daemon skriver ut följande meddelande till loggarna:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Rotorsak**

IoT Edge daemon framtvingar process-ID för alla moduler som ansluter till edgeHub av säkerhets skäl. Den verifierar att alla meddelanden som skickas av en modul kommer från huvud process-ID: t för modulen. Om ett meddelande skickas av en modul från ett annat process-ID än vad som upprättats, kommer det att neka meddelandet med ett 404-fel meddelande.

**Lösning**

Från och med version 1.0.7 har alla modulblad behörighet att ansluta. Om det inte går att uppgradera till 1.0.7 utför du följande steg. Mer information finns i [1.0.7 Release ändringsloggen](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Kontrol lera att samma process-ID alltid används av den anpassade IoT Edge-modulen för att skicka meddelanden till edgeHub. Se till `ENTRYPOINT` exempel i stället för `CMD` kommandot i Docker-filen, eftersom `CMD` kommer att leda till ett process-ID för modulen och ett annat process-ID för bash-kommandot som kör huvud programmet `ENTRYPOINT` , kommer att leda till ett enda process-ID.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Brand Väggs-och port konfigurations regler för IoT Edge distribution

Azure IoT Edge tillåter kommunikation från en lokal server till Azure-molnet med IoT Hub protokoll som stöds, se [välja ett kommunikations protokoll](../iot-hub/iot-hub-devguide-protocols.md). För förbättrad säkerhet är kommunikations kanaler mellan Azure IoT Edge och Azure-IoT Hub alltid konfigurerade att vara utgående. Den här konfigurationen baseras på [kommunikations mönstret tjänster](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), som minimerar angrepps ytan för en skadlig entitet att utforska. Inkommande kommunikation krävs endast för vissa scenarier där Azure IoT Hub behöver skicka meddelanden till Azure IoT Edge-enheten. Meddelanden från moln till enhet skyddas med hjälp av säkra TLS-kanaler och kan skyddas ytterligare med X. 509-certifikat och TPM-enhets moduler. Azure IoT Edge Security Manager styr hur kommunikationen kan upprättas, se [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Även om IoT Edge ger förbättrad konfiguration för att skydda Azure IoT Edge Runtime och distribuerade moduler, är det fortfarande beroende av den underliggande datorn och nätverks konfigurationen. Därför är det absolut nödvändigt att se till att rätt nätverks-och brand Väggs regler har kon figurer ATS för säker Edge till moln kommunikation. Följande tabell kan användas som en rikt linje när du konfigurerar brand Väggs regler för de underliggande servrarna där Azure IoT Edge runtime finns:

|Protokoll|Port|Inkommande|Autentiseringsnivå|Riktlinjer|
|--|--|--|--|--|
|MQTT|8883|BLOCKERAd (standard)|BLOCKERAd (standard)|<ul> <li>Konfigurera utgående (utgående) som ska öppnas när MQTT används som kommunikations protokoll.<li>1883 för MQTT stöds inte av IoT Edge. <li>Inkommande (inkommande) anslutningar ska blockeras.</ul>|
|AMQP|5671|BLOCKERAd (standard)|ÖPPNA (standard)|<ul> <li>Standard kommunikations protokoll för IoT Edge. <li> Måste konfigureras för att vara öppen om Azure IoT Edge inte har kon figurer ATS för andra protokoll som stöds eller om AMQP är det önskade kommunikations protokollet.<li>5672 för AMQP stöds inte av IoT Edge.<li>Blockera den här porten när Azure IoT Edge använder ett annat IoT Hub protokoll som stöds.<li>Inkommande (inkommande) anslutningar ska blockeras.</ul></ul>|
|HTTPS|443|BLOCKERAd (standard)|ÖPPNA (standard)|<ul> <li>Konfigurera utgående (utgående) som ska öppnas på 443 för IoT Edge etablering. Den här konfigurationen krävs när du använder manuella skript eller DPS (Azure IoT Device Provisioning service). <li>Inkommande (inkommande) anslutning ska endast vara öppen för vissa scenarier: <ul> <li>  Om du har en transparent Gateway med löv enheter som kan skicka metod begär Anden. I det här fallet behöver port 443 inte vara öppen för externa nätverk för att ansluta till IoTHub eller tillhandahålla IoTHub-tjänster via Azure IoT Edge. Det innebär att den inkommande regeln kan begränsas till endast öppna inkommande (inkommande) från det interna nätverket. <li> För C2D-scenarier (Client to Device).</ul><li>80 för HTTP stöds inte av IoT Edge.<li>Om icke-HTTP-protokoll (till exempel AMQP eller MQTT) inte kan konfigureras i företaget. meddelandena kan skickas via WebSockets. Port 443 kommer att användas för WebSocket-kommunikation i så fall.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge agent module rapporterar kontinuerligt den tomma config-filen och inga moduler startar på enheten

Enheten har problem med att starta moduler som definierats i distributionen. Endast edgeAgent körs men löpande rapportering av Tom config-fil...

**Rotorsak**

Som standard startar IoT Edge moduler i egna isolerade behållar nätverk. Enheten kan ha problem med DNS-namnmatchning i det här privata nätverket.

**Lösning**

**Alternativ 1: Ange DNS-server i behållar motor inställningarna**

Ange DNS-servern för din miljö i inställningar för container motor som ska gälla för alla behållar moduler som startas av motorn. Skapa en fil med `daemon.json` namnet ange den DNS-server som ska användas. Ett exempel:

```json
{
    "dns": ["1.1.1.1"]
}
```

Exemplet ovan anger DNS-servern till en offentligt tillgänglig DNS-tjänst. Om gräns enheten inte kan komma åt den här IP-adressen från dess miljö ersätter du den med den DNS-serveradress som är tillgänglig.

Placera `daemon.json` på rätt plats för din plattform:

| Plattform | Plats |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-värd med Windows-behållare | `C:\ProgramData\iotedge-moby\config` |

Om platsen redan innehåller `daemon.json` fil, lägger du till **DNS-** nyckeln till den och sparar filen.

Starta om behållar motorn för att uppdateringarna ska börja gälla.

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

Se till att ange den här konfigurationen för *edgeAgent* -och *edgeHub* -modulerna också.

## <a name="next-steps"></a>Nästa steg

Tror du att du har hittat ett fel i IoT Edge-plattformen? [Skicka in ett ärende](https://github.com/Azure/iotedge/issues) så att vi kan fortsätta att förbättra.

Om du har fler frågor kan du skapa en [supportbegäran](https://portal.azure.com/#create/Microsoft.Support) för hjälp.
