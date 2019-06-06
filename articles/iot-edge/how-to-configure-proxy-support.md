---
title: Konfigurera enheter för nätverk - proxyservrar i Azure IoT Edge | Microsoft Docs
description: Så här konfigurerar du Azure IoT Edge-körningen och några internet-ansluten IoT Edge-moduler för att kommunicera via en proxyserver.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1c0da1a768b894f543b9089643622c31d6a8758d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730152"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver

IoT Edge-enheter skicka HTTPS-begäranden för att kommunicera med IoT Hub. Om enheten är ansluten till ett nätverk som använder en proxyserver, måste du konfigurera IoT Edge-körningen att kommunicera med servern. Proxy-servrar kan också påverka enskilda IoT Edge-moduler om de gör HTTP eller HTTPS-begäranden som inte går via IoT Edge hub. 

Den här artikeln beskriver följande fyra steg för att konfigurera och hantera en IoT Edge-enhet bakom en proxyserver: 

1. **Installera IoT Edge-körningen på din enhet.**

   IoT Edge-installationsskript Hämta paket och filer från internet, så att enheten behöver kommunicera via en proxyserver för att göra dessa begäranden. Detaljerade anvisningar finns i den [installera runtime via en proxyserver](#install-the-runtime-through-a-proxy) i den här artikeln. För Windows-enheter, installationsskriptet innehåller också en [offlineinstallation](how-to-install-iot-edge-windows.md#offline-installation) alternativet. 

   Det här steget är en engångsprocess som utförs på IoT Edge-enhet när du först konfigurera den. Samma anslutningar krävs även när du uppdaterar IoT Edge-körningen. 

2. **Konfigurera Docker-daemon och IoT Edge-daemon på din enhet.**

   IoT Edge använder två Daemon på enheten som behöver att skicka webbförfrågningar genom proxyservern. IoT Edge-daemon ansvarar för kommunikation med IoT Hub. Daemonen Moby ansvarar för hantering av behållare, så kommunicerar med behållarregister. Detaljerade anvisningar finns i den [konfigurera Daemon](#configure-the-daemons) i den här artikeln. 

   Det här steget är en engångsprocess som utförs på IoT Edge-enhet när du först konfigurera den.

3. **Konfigurera egenskaper för IoT Edge-agenten i config.yaml-filen på din enhet.**

   IoT Edge-daemon startas modulen edgeAgent inledningsvis, men sedan modulen edgeAgent ansvarar för att hämta manifestet distribution från IoT Hub och startar de andra modulerna. Konfigurera miljövariabler för edgeAgent-modulen för IoT Edge-agenten att göra den första anslutningen till IoT Hub till manuellt på själva enheten. Efter den första anslutningen kan du konfigurera modulen edgeAgent via en fjärranslutning. Detaljerade anvisningar finns i den [konfigurerar IoT Edge-agenten](#configure-the-iot-edge-agent) i den här artikeln.

   Det här steget är en engångsprocess som utförs på IoT Edge-enhet när du först konfigurera den.

4. **Ange miljövariabler för alla moduler som kommunicerar via proxy för alla framtida modulen distributioner.**

   När din IoT Edge-enhet ställer in och är anslutna till IoT Hub via proxyservern, måste du upprätthålla anslutningen i alla framtida modulen distributioner. Detaljerade anvisningar finns i den [konfigurera distribution manifest](#configure-deployment-manifests) i den här artikeln. 

   Det här steget är en pågående process via en fjärranslutning så att varje ny modul eller distribution av uppdatering underhåller enhetens möjlighet att kommunicera via proxyservern. 

## <a name="know-your-proxy-url"></a>Vet proxy-URL

Innan du utför stegen i den här artikeln som du behöver veta din proxy-URL.

Proxy-URL: er ta följande format: **protokollet**://**proxy_host**:**proxy_port**.

* Den **protokollet** är antingen HTTP eller HTTPS. Docker-daemon kan använda antingen protokollet, beroende på dina inställningar för behållarregister, men IoT Edge-daemon och runtime behållare bör alltid använda HTTPS.

* Den **proxy_host** är en adress för proxyservern. Om proxyservern kräver autentisering, kan du ange dina autentiseringsuppgifter som en del av proxy-värden med följande format: **användaren**:**lösenord**\@**proxy_host** .

* Den **proxy_port** är den nätverksport som proxyn svarar på trafik.

## <a name="install-the-runtime-through-a-proxy"></a>Installera runtime via en proxyserver

Om din IoT Edge-enhet körs på Windows eller Linux, som du behöver komma åt installationspaket genom proxyservern. Följ instruktionerna för att installera IoT Edge-körning via en proxyserver för ditt operativsystem. 

### <a name="linux"></a>Linux

Om du installerar IoT Edge-körningen på en Linux-enhet, konfigurera package manager att gå igenom din proxyserver för att komma åt installationspaketet. Till exempel [konfigurera apt-get för att använda en http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). När du har konfigurerat din Pakethanteraren, följer du anvisningarna i [installera Azure IoT Edge-körningen på Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) eller [installera Azure IoT Edge-körningen på Linux (x64)](how-to-install-iot-edge-linux.md) som vanligt.

### <a name="windows"></a>Windows

Om du installerar IoT Edge-körningen på en Windows-enhet, måste du gå igenom proxyservern två gånger. Den första anslutningen hämtar skriptet installationsfilen och den andra anslutningen är under installationen för att ladda ned de nödvändiga komponenterna. Du kan konfigurera proxyinformation i Windows-inställningar eller ta med din proxyinformation direkt i PowerShell-kommandon. 

Följande steg visar ett exempel på en windows-installationen med de `-proxy` argument:

1. Kommandot Invoke-WebRequest måste proxyinformation till installationsprogrammet för skriptet. Sedan behöver kommandot Distribuera IoTEdge proxyinformation för att ladda ned installationsfilerna. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Kommandot initiera IoTEdge behöver inte gå igenom proxyserver, så det andra steget kräver endast proxyinformation för Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Mer information om proxyparametrar finns i [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Läs mer om installationsalternativen för Windows, inklusive offline installation [installera Azure IoT Edge-körningen på Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurera Daemon

IoT Edge är beroende av två daemons som körs på IoT Edge-enhet. Daemonen Moby gör begäranden att hämta behållaravbildningar från behållarregister. Daemonen IoT Edge gör webbegäranden till att kommunicera med IoT Hub.

Både Moby och IoT Edge-Daemon måste konfigureras för att använda proxyservern för pågående enhetens funktioner. Det här steget utförs på IoT Edge-enhet under den inledande enhetsinställningen. 

### <a name="moby-daemon"></a>Daemon för Moby

Eftersom Moby bygger på Docker, finns i Docker-dokumentationen för att konfigurera Moby daemon med miljövariabler. De flesta behållarregister (inklusive DockerHub och Azure-Behållarregister) stöder HTTPS-begäranden, så är den parameter som du bör ange **HTTPS_PROXY**. Om avbildningar hämtas från ett register som inte stöder transport layer security (TLS) så bör du ange den **HTTP_PROXY** parametern. 

Välj den artikel som gäller för din IoT Edge-enhetens operativsystem: 

* [Konfigurera Docker-daemon på Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Daemonen Moby på Linux-enheter och ser namnet Docker.
* [Konfigurera Docker-daemon på Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Daemonen Moby på Windows-enheter kallas iotedge moby. Namnen skiljer sig eftersom det är möjligt att köra både Docker Desktop- och Moby parallellt på en Windows-enhet. 

### <a name="iot-edge-daemon"></a>Daemon för IoT Edge

IoT Edge-daemon har konfigurerats på ett liknande sätt att daemonen Moby. Använd följande steg för att ange en miljövariabel för tjänsten baserat på ditt operativsystem. 

IoT Edge-daemon använder alltid HTTPS för att skicka begäranden till IoT Hub.

#### <a name="linux"></a>Linux

Öppna en redigerare i terminalen för att konfigurera IoT Edge-daemon. 

```bash
sudo systemctl edit iotedge
```

Ange följande text, ersätta  **\<proxy-URL >** med din proxyserveradress och port. Spara och avsluta. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Uppdatera hanteraren för att hämta den nya konfigurationen för IoT Edge.

```bash
sudo systemctl daemon-reload
```

Starta om IoT Edge för att ändringarna ska börja gälla.

```bash
sudo systemctl restart iotedge
```

Kontrollera att miljövariabeln har skapats och den nya konfigurationen lästes in. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Öppna ett PowerShell-fönster som administratör och kör följande kommando för att redigera registret med den nya miljövariabeln. Ersätt  **\<proxy-url >** med din proxyserveradress och port. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Starta om IoT Edge för att ändringarna ska börja gälla.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurera IoT Edge-agenten

IoT Edge-agenten är den första modulen ska börja på alla IoT Edge-enheter. Den startas för första gången baserat på informationen i filen config.yaml IoT Edge. IoT Edge-agenten ansluter sedan till IoT Hub att hämta distributionen manifest, som förklarar vad andra moduler bör vara distribueras på enheten.

Det här steget utförs en gång på IoT Edge-enhet under den inledande enhetsinställningen. 

1. Öppna filen config.yaml på din IoT Edge-enhet. På Linux-system använder den här filen finns på **/etc/iotedge/config.yaml**. På Windows-System, den här filen finns på **C:\ProgramData\iotedge\config.yaml**. Konfigurationsfilen är skyddad, så du behöver administrativ behörighet att komma åt den. På Linux-system använder den `sudo` kommandot innan du öppnar filen i din prioriterade textredigerare. Öppna en textredigerare, till exempel Anteckningar som administratör på Windows, och öppna sedan filen. 

2. I filen config.yaml hitta den **Edge-agenten modulen spec** avsnittet. IoT Edge-agenten definitionen innehåller en **env** parametern där du kan lägga till miljövariabler. 

3. Ta bort klammerparenteser som platshållare för parametern env och lägga till nya variabeln på en ny rad. Kom ihåg att indrag i YAML är två blanksteg. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge-körningen använder AMQP som standard för att kommunicera med IoT Hub. Vissa proxyservrar blockera AMQP portar. Om så är fallet, sedan behöva du också konfigurera edgeAgent för att använda AMQP via WebSocket. Lägg till en andra miljövariabel.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent definition med miljövariabler](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Spara ändringarna i config.yaml och Stäng redigeraren. Starta om IoT Edge för att ändringarna ska börja gälla. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Konfigurera distribution manifest  

När din IoT Edge-enhet har konfigurerats för att fungera med proxyservern, måste du fortsätta att deklarera miljövariablerna i framtida distribution manifest. Du kan redigera manifest för distribution med hjälp av guiden för Azure-portalen eller genom att redigera en distribution manifest JSON-fil. 

Konfigurera alltid två moduler för körning, edgeAgent och edgeHub att kommunicera via proxyserver så att de kan hantera en anslutning med IoT Hub. Om du tar bort du proxyinformationen från modulen edgeAgent är det enda sättet att återupprätta anslutningen genom att redigera filen config.yaml på enheten, enligt beskrivningen i föregående avsnitt. 

Andra IoT Edge-moduler som ansluter till internet ska konfigureras för att kommunicera via proxyserver för. Moduler som dirigerar skicka meddelanden till edgeHub eller som endast kommunicerar med andra moduler på enheten behöver dock inte proxy-serverinformation. 

Det här steget är pågående under hela livslängd IoT Edge-enhet. 

### <a name="azure-portal"></a>Azure Portal

När du använder den **ange moduler** guiden för att skapa distributioner för IoT Edge-enheter, alla moduler som har en **miljövariabler** avsnitt som du kan använda för att konfigurera proxy server-anslutningar. 

Om du vill konfigurera IoT Edge-agenten och IoT Edge hub-moduler, Välj **konfigurera avancerade Edge-körningsinställningar** på det första steget i guiden. 

![Konfigurera avancerade Edge-körningsinställningar](./media/how-to-configure-proxy-support/configure-runtime.png)

Lägg till den **https_proxy** miljövariabeln både IoT Edge-agenten och moduldefinitioner för IoT Edge hub. Om du har lagt till den **UpstreamProtocol** miljövariabeln i filen config.yaml på IoT Edge-enhet, lägga till som IoT Edge-agenten modulen definition för. 

![Ange miljövariabeln för https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Alla andra moduler som du lägger till ett distribution-manifest följer samma mönster. På sidan där du anger modulnamn och bild finns ett variabelavsnitt i miljön.

### <a name="json-deployment-manifest-files"></a>JSON-distribution manifestfiler

Om du skapar distributioner för IoT Edge-enheter med hjälp av mallar i Visual Studio Code eller genom att manuellt skapa JSON-filer kan du lägga till miljövariablerna som direkt till varje modul-definition. 

Använd följande JSON-format: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Med miljövariabler som ingår, din modulen definition bör se ut som i följande exempel edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Om du har lagt till den **UpstreamProtocol** miljövariabeln i filen confige.yaml på IoT Edge-enhet, lägga till som IoT Edge-agenten modulen definition för. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om rollerna för den [IoT Edge-körningen](iot-edge-runtime.md).

Felsöka installationen och konfigurationen med [vanliga problem och lösningar för Azure IoT Edge](troubleshoot.md)

