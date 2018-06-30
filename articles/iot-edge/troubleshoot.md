---
title: Felsöka Azure IoT Edge | Microsoft Docs
description: Lös vanliga problem och lär dig felsökning av Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ec396e8a1ad36e85e1291995345ca1de24668d0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128068"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Vanliga problem och lösningar för Azure IoT Edge

Om du får problem med att köra Azure IoT Edge i din miljö kan du använda den här artikeln som en guide för felsökning och lösningar. 

## <a name="standard-diagnostic-steps"></a>Standarddiagnostikåtgärder 

När det uppstår ett problem kan du läsa mer om IoT Edge-enhetens tillstånd genom att granska behållarloggarna och meddelandena som skickas till och från enheten. Använd kommandona och verktygen i det här avsnittet för att samla in information. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrollera status för IoT Edge Security Manager och dess loggfiler:

På Linux:
- Visa status för IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

- Visa loggar för IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Visa mer detaljerad loggar för IoT Edge Security Manager:

   - Redigera inställningar för iotedge daemon:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Uppdatera följande rader:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Starta om daemonen IoT kant säkerhet:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

I Windows:
- Visa status för IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

- Visa loggar för IoT Edge Security Manager:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false}
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Om Säkerhetshanteraren IoT kant inte körs Kontrollera konfigurationsfilen yaml

> [!WARNING]
> YAML-filer får inte innehålla flikar som indrag. Använd 2 utrymmen i stället.

På Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

I Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Loggarna behållare för problem

När IoT Edge Security Daemon körs kan du titta på loggar av behållarna för att identifiera problem. Börja med dina distribuerade behållare och titta på behållarna som utgör IoT Edge-körningen: Edge-agent och Edge Hub. Edge-agentloggarna ger vanligtvis information om livscykeln för varje behållare. Edge Hub-loggarna ger information om meddelanden och routning. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Visa meddelanden som skickas via Edge-hubb

Visa meddelanden som skickas via navet kant och samla insikter om egenskaper uppdateringar med utförlig loggar från edgeAgent och edgeHub runtime behållarna. Aktivera utförlig loggar på dessa behållare genom att ange den `RuntimeLogLevel` miljövariabeln: 

På Linux:
    
   ```cmd
   export RuntimeLogLevel="debug"
   ```
    
I Windows:
    
   ```powershell
   [Environment]::SetEnvironmentVariable("RuntimeLogLevel", "debug")
   ```

Du kan också kontrollera meddelandena som skickas mellan IoT Hub och IoT Edge-enheterna. Visa meddelandena genom att använda tillägget [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) för Visual Studio Code. Mer information finns i [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Praktiskt verktyg när du utvecklar med Azure IoT).

### <a name="restart-containers"></a>Starta om behållare
När du undersöker loggar och meddelanden för information, kan du prova att starta om behållare:

```
iotedge restart <container name>
```

Starta om IoT kant runtime-behållare:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Starta om säkerhetshanteraren IoT kant

Om problemet fortfarande beständighet, kan du starta om säkerhetshanteraren IoT kant.

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
Kontrollera att autentiseringsuppgifterna registret har angetts korrekt i din distributionsmanifestet

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT-Edge security daemon misslyckas med ett ogiltigt värdnamn

Kommandot `sudo journalctl -u iotedge` misslyckas och följande meddelande: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Rotorsak
IoT-Edge-körning stöder bara värdnamn som är kortare än 64 tecken. Detta vanligtvis inte ett problem för fysiska datorer, men kan uppstå när du ställer in körning på en virtuell dator. De automatiskt genererade värdnamn för Windows-datorerna i Azure, i synnerhet tenderar att vara lång. 

### <a name="resolution"></a>Lösning
När du ser det här felet kan åtgärda du det genom att konfigurera DNS-namnet på den virtuella datorn och sedan ange DNS-namn som värdnamnet i installationskommandot.

1. Gå till översiktssidan av den virtuella datorn i Azure-portalen. 
2. Välj **konfigurera** under DNS-namn. Om den virtuella datorn redan har konfigurerat en DNS-namn, behöver du inte konfigurera en ny. 

   ![Konfigurera DNS-namn](./media/troubleshoot/configure-dns.png)

3. Ange ett värde för **DNS-namnetikett** och välj **spara**.
4. Kopiera den nya DNS-namn som ska vara i formatet  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. Inuti den virtuella datorn använder du följande kommando för att ställa in IoT kant runtime med DNS-namn:

   - På Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - I Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="next-steps"></a>Nästa steg
Tror du att du har hittat ett fel i IoT Edge-plattformen? [Skicka in ett problem](https://github.com/Azure/iotedge/issues) så att vi kan fortsätta att förbättra oss. 
