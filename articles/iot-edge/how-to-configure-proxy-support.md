---
title: Konfigurera enheter för nätverk - proxyservrar i Azure IoT Edge | Microsoft Docs
description: Så här konfigurerar du Azure IoT Edge-körningen och några internet-ansluten IoT Edge-moduler för att kommunicera via en proxyserver.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4fa5402b87eea969a5a4093000dda06d3cb5675d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216268"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver

IoT Edge-enheter skicka HTTPS-begäranden för att kommunicera med IoT Hub. Om enheten är ansluten till ett nätverk som använder en proxyserver, måste du konfigurera IoT Edge-körningen att kommunicera med servern. Proxy-servrar kan också påverka enskilda IoT Edge-moduler om de gör HTTP eller HTTPS-begäranden som inte går via IoT Edge hub. 

Konfigurera en IoT Edge-enhet för att arbeta med en proxyserver följer de här stegen: 

1. Installera IoT Edge-körningen på din enhet. 
2. Konfigurera Docker-daemon och IoT Edge-daemon på enheten för att använda proxyservern.
3. Konfigurera egenskaper för edgeAgent i config.yaml-filen på din enhet.
4. Ange miljövariabler för IoT Edge-körningen och andra IoT-Edge moduler i manifestet distribution.

## <a name="know-your-proxy-url"></a>Vet proxy-URL

För att konfigurera både Docker-daemon och IoT Edge på din enhet, som du behöver veta din proxy-URL.

Proxy-URL: er ta följande format: **protokollet**://**proxy_host**:**proxy_port**.

* Den **protokollet** är antingen HTTP eller HTTPS. Docker-daemon kan använda antingen protokollet, beroende på dina inställningar för behållarregister, men IoT Edge-daemon och runtime behållare bör alltid använda HTTPS.

* Den **proxy_host** är en adress för proxyservern. Om proxyservern kräver autentisering, kan du ange dina autentiseringsuppgifter som en del av proxy-värden med följande format: **användaren**:**lösenord**\@**proxy_host** .

* Den **proxy_port** är den nätverksport som proxyn svarar på trafik.

## <a name="install-the-runtime"></a>Installera runtime

Om du installerar IoT Edge-körningen på en Linux-enhet, konfigurera package manager att gå igenom din proxyserver för att komma åt installationspaketet. Till exempel [konfigurera apt-get för att använda en http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). När du har konfigurerat din Pakethanteraren, följer du anvisningarna i [installera Azure IoT Edge-körningen på Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) eller [installera Azure IoT Edge-körningen på Linux (x64)](how-to-install-iot-edge-linux.md) som vanligt.

Om du installerar IoT Edge-körningen på en Windows-enhet, måste du gå igenom proxyservern två gånger. Den första anslutningen är att hämta installationsfilen för skript och den andra anslutningen är under installationen för att ladda ned de nödvändiga komponenterna. Du kan konfigurera proxyinformation i Windows-inställningar eller ta med din proxyinformation direkt i installationsskriptet. Följande powershell-skript är ett exempel på en windows-installationen med de `-proxy` argument:

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -proxy <proxy URL>
```

Om du har komplicerat autentiseringsuppgifter för proxyservern inte kan ingå i URL: en, använder du den `-ProxyCredential` parameter i `-InvokeWebRequestParameters`. Exempel:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Mer information om proxyparametrar finns i [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Mer information om installationsalternativ finns i [installera Azure IoT Edge-körningen på Windows](how-to-install-iot-edge-windows.md).

När IoT Edge-körningen har installerats, Använd följande avsnitt för att konfigurera den med din proxyinformation. 

## <a name="configure-the-daemons"></a>Konfigurera Daemon

Moby och IoT Edge-Daemon körs på din IoT Edge-enhet måste konfigureras för att använda proxyservern. Daemonen Moby gör begäranden att hämta behållaravbildningar från behållarregister. Daemonen IoT Edge gör webbegäranden till att kommunicera med IoT Hub.

### <a name="moby-daemon"></a>Daemon för Moby

Eftersom Moby bygger på Docker, finns i Docker-dokumentationen för att konfigurera Moby daemon med miljövariabler. De flesta behållarregister (inklusive DockerHub och Azure-Behållarregister) stöder HTTPS-begäranden, så är den parameter som du bör ange **HTTPS_PROXY**. Om avbildningar hämtas från ett register som inte stöder transport layer security (TLS) så bör du ange den **HTTP_PROXY** parametern. 

Välj den artikel som gäller för din IoT Edge-enhetens operativsystem: 

* [Konfigurera Docker-daemon på Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Daemonen Moby på Linux-enheter och ser namnet Docker.
* [Konfigurera Docker-daemon på Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Daemonen Moby på Windows-enheter kallas iotedge moby. Namnen skiljer sig eftersom det är möjligt att köra både Docker Desktop- och Moby parallellt på en Windows-enhet. 

### <a name="iot-edge-daemon"></a>Daemon för IoT Edge

IoT Edge-daemon har konfigurerats på ett liknande sätt att daemonen Moby. Alla begäranden som IoT Edge skickar till IoT Hub använder HTTPS. Använd följande steg för att ange en miljövariabel för tjänsten baserat på ditt operativsystem. 

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

Öppna filen config.yaml på din IoT Edge-enhet. På Linux-system använder den här filen finns på **/etc/iotedge/config.yaml**. På Windows-System, den här filen finns på **C:\ProgramData\iotedge\config.yaml**. Konfigurationsfilen är skyddad, så du behöver administrativ behörighet att komma åt den. På Linux-system, som innebär att med hjälp av den `sudo` kommandot innan du öppnar filen i din prioriterade textredigerare. På Windows betyder det att öppna en textredigerare, t.ex på Kör som administratör och sedan öppna filen. 

I filen config.yaml hitta den **Edge-agenten modulen spec** avsnittet. IoT Edge-agenten definitionen innehåller en **env** parametern där du kan lägga till miljövariabler. 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

Ta bort klammerparenteser som platshållare för parametern env och lägga till nya variabeln på en ny rad. Kom ihåg att indrag i YAML är två blanksteg. 

```yaml
https_proxy: "<proxy URL>"
```

IoT Edge-körningen använder AMQP som standard för att kommunicera med IoT Hub. Vissa proxyservrar blockera AMQP portar. Om så är fallet, sedan behöva du också konfigurera edgeAgent för att använda AMQP via WebSocket. Lägg till en andra miljövariabel.

```yaml
UpstreamProtocol: "AmqpWs"
```

![edgeAgent definition med miljövariabler](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Spara ändringarna i config.yaml och Stäng redigeraren. Starta om IoT Edge för att ändringarna ska börja gälla. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Konfigurera distribution manifest  

När din IoT Edge-enhet har konfigurerats för att fungera med proxyservern, måste du fortsätta att deklarera miljövariablerna i framtida distribution manifest. Konfigurera alltid två moduler för körning, edgeAgent och edgeHub att kommunicera via proxyserver så att de kan hantera en anslutning med IoT Hub. Andra IoT Edge-moduler som ansluter till internet ska konfigureras för proxyservern. Moduler som dirigerar skicka meddelanden till edgeHub eller som endast kommunicerar med andra moduler på enheten behöver dock inte proxy-serverinformation. 

Du kan skapa distributionen manifest med Azure-portalen eller manuellt genom att redigera en JSON-fil. 

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

