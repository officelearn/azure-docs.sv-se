---
title: Konfigurera Azure IoT Edge-enheter för nätverket proxyservrar | Microsoft Docs
description: Så här konfigurerar du Azure IoT Edge-körningen och några internet-ansluten IoT Edge-moduler för att kommunicera via en proxyserver.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 09/24/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6e6a1d2f758cabca41ac405a01de1f0d8bfd0a7b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037464"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver

IoT Edge-enheter skicka HTTPS-begäranden för att kommunicera med IoT Hub. Om enheten är ansluten till ett nätverk som använder en proxyserver, måste du konfigurera IoT Edge-körningen att kommunicera med servern. Proxy-servrar kan också påverka enskilda IoT Edge-moduler om de gör HTTP eller HTTPS-begäranden som inte går via Edge hub. 

Konfigurera en IoT Edge-enhet för att arbeta med en proxyserver följer de här stegen: 

1. Installera IoT Edge-körningen på din enhet. 
2. Konfigurera Docker-daemon och IoT Edge-daemon på enheten för att använda proxyservern.
3. Konfigurera egenskaper för edgeAgent i config.yaml-filen på din enhet.
4. Ange miljövariabler för IoT Edge-körningen och andra IoT-Edge moduler i manifestet distribution. 

## <a name="install-the-runtime"></a>Installera runtime

Om du installerar IoT Edge-körningen på en Linux-enhet, konfigurera package manager att gå igenom din proxyserver för att komma åt installationspaketet. Till exempel [konfigurera apt-get för att använda en http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). När du har konfigurerat din Pakethanteraren, följer du anvisningarna i [installera Azure IoT Edge-körningen på Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) eller [installera Azure IoT Edge-körningen på Linux (x64)](how-to-install-iot-edge-linux.md) som vanligt. 

Om du installerar IoT Edge-körningen på en Windows-enhet, måste du gå igenom proxyserver för att komma åt installationspaketet. Du kan konfigurera proxyinformation i Windows-inställningar eller ta med din proxyinformation direkt i installationsskriptet. Följande powershell-skript är ett exempel på en windows-installationen med de `-proxy` argument:

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows
```

Mer information och vilka installationsalternativ, se [installera Azure IoT Edge-körningen på Windows för användning med Windows-behållare](how-to-install-iot-edge-windows-with-windows.md) eller [installera Azure IoT Edge-körningen på Windows för användning med Linux-behållare](how-to-install-iot-edge-windows-with-linux.md).

När IoT Edge-körningen har installerats, Använd följande avsnitt för att konfigurera den med din proxyinformation. 

## <a name="configure-the-daemons"></a>Konfigurera Daemon

Docker och IoT Edge-Daemon körs på din IoT Edge-enhet måste konfigureras för att använda proxyservern. Docker-daemon gör begäranden att hämta behållaravbildningar från behållarregister. Daemonen IoT Edge gör webbegäranden till att kommunicera med IoT Hub.

### <a name="docker-daemon"></a>Docker-daemon

I Docker-dokumentationen för att konfigurera Docker-daemon med miljövariabler. De flesta behållarregister (inklusive DockerHub och Azure-Behållarregister) stöder HTTPS-begäranden, så är den variabel som du bör ange **HTTPS_PROXY**. Om avbildningar hämtas från ett register som inte stöder transport layer security (TLS), så du bör ange den **HTTP_PROXY**. 

Välj den artikel som gäller för din Docker-version: 

* [Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Docker för Windows](https://docs.docker.com/docker-for-windows/#proxies)

### <a name="iot-edge-daemon"></a>Daemon för IoT Edge

IoT Edge-daemon har konfigurerats på liknande sätt som Docker-daemon. Alla begäranden som IoT Edge skickar till IoT Hub använder HTTPS. Använd följande steg för att ange en miljövariabel för tjänsten baserat på ditt operativsystem. 

#### <a name="linux"></a>Linux

Öppna en redigerare i terminalen för att konfigurera IoT Edge-daemon. 

```bash
sudo systemctl edit iotedge
```

Ange följande text, ersätta  **\<proxy-URL >** med din proxyserveradress och port. Spara och avsluta. 

```text
[Service]
Environment="https_proxy=<proxy URL>"
```

Uppdatera hanteraren för att hämta den nya konfigurationen för iotedge.

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

## <a name="configure-the-edge-agent"></a>Konfigurera Edge-agenten

Edge-agenten är den första modulen ska börja på alla IoT Edge-enheter. Den startas för första gången baserat på informationen i filen config.yaml IoT Edge. Edge-agenten ansluter sedan till IoT Hub att hämta distributionen manifest, som förklarar vad andra moduler bör vara distribueras på enheten.

Öppna filen config.yaml på din IoT Edge-enhet. På Linux-system använder den här filen finns på **/etc/iotedge/config.yaml**. På Windows-System, den här filen finns på **C:\ProgramData\iotedge\config.yaml**. Konfigurationsfilen är skyddad, så du behöver administrativ behörighet att komma åt den. På Linux-system, som innebär att med hjälp av den `sudo` kommandot innan du öppnar filen i din prioriterade textredigerare. På Windows betyder det att öppna en textredigerare, t.ex på Kör som administratör och sedan öppna filen. 

I filen config.yaml hitta den **Edge-agenten modulen spec** avsnittet. Edge-agenten definitionen innehåller en **env** parametern där du kan lägga till miljövariabler. 

![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)

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

När din IoT Edge-enhet har konfigurerats för att fungera med proxyservern kan behöva du samtidigt deklarera miljövariabler i alla framtida distribution manifest. De två moduler för körning, edgeAgent och edgeHub, bör du alltid ha proxyservern som konfigurerats för att upprätthålla kommunikation med IoT Hub. Du kan konfigurera alla IoT Edge-modul att kommunicera via en proxyserver, men det är inte nödvändigt för moduler som dirigerar skicka meddelanden till edgeHub eller som endast kommunicerar med andra moduler på enheten. 

Du kan skapa distributionen manifest med Azure-portalen eller manuellt genom att redigera en JSON-fil. 

### <a name="azure-portal"></a>Azure Portal

När du använder den **ange moduler** guiden för att skapa distributioner för IoT Edge-enheter, alla moduler som har en **miljövariabler** avsnitt som du kan använda för att konfigurera proxy server-anslutningar. 

Om du vill konfigurera Edge-agent och Edge hub-moduler, Välj **konfigurera avancerade Edge-körningsinställningar** på det första steget i guiden. 

![Konfigurera avancerade Edge-körningsinställningar](./media/how-to-configure-proxy-support/configure-runtime.png)

Lägg till den **https_proxy** miljövariabeln både Edge-agenten och moduldefinitioner för Edge hub. Om du har lagt till den **UpstreamProtocol** miljövariabeln i filen config.yaml på IoT Edge-enhet, lägga till som i Edge-agenten modulen definitionen för. 

![Ange miljövariabler](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

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

Om du har lagt till den **UpstreamProtocol** miljövariabeln i filen confige.yaml på IoT Edge-enhet, lägga till som i Edge-agenten modulen definitionen för. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om rollerna för den [IoT Edge-körningen](iot-edge-runtime.md).

Felsöka installationen och konfigurationen med [vanliga problem och lösningar för Azure IoT Edge](troubleshoot.md)

