---
title: Konfigurera enheter för nätverk - proxyservrar i Azure IoT Edge | Microsoft Docs
description: Så här konfigurerar du Azure IoT Edge-körningen och några internet-ansluten IoT Edge-moduler för att kommunicera via en proxyserver.
author: kgremban
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a4ed17ceddf01ec628d80dc3ba9f4d7ee305f3b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457166"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver

IoT Edge-enheter skicka HTTPS-begäranden för att kommunicera med IoT Hub. Om enheten är ansluten till ett nätverk som använder en proxyserver, måste du konfigurera IoT Edge-körningen att kommunicera med servern. Proxyservrar kan också påverka enskilda IoT Edge moduler om de gör HTTP-eller HTTPS-begäranden som inte dirigeras via IoT Edge Hub. 

Den här artikeln vägleder dig genom följande fyra steg för att konfigurera och sedan hantera en IoT Edge enhet bakom en proxyserver: 

1. **Installera IoT Edge runtime på enheten.**

   Installations skripten för IoT Edge hämtar paket och filer från Internet, så din enhet måste kommunicera via proxyservern för att utföra dessa förfrågningar. Detaljerade anvisningar finns i avsnittet [Installera körnings miljön via en proxy](#install-the-runtime-through-a-proxy) i den här artikeln. För Windows-enheter tillhandahåller installations skriptet även ett alternativ för [Offline-installation](how-to-install-iot-edge-windows.md#offline-installation) . 

   Det här steget är en engångs process som utförs på den IoT Edge enheten första gången du ställer in den. Samma anslutningar krävs också när du uppdaterar IoT Edge Runtime. 

2. **Konfigurera Docker daemon och IoT Edge daemon på enheten.**

   IoT Edge använder två bakgrunds program på enheten, som båda måste göra webb förfrågningar via proxyservern. IoT Edge daemon ansvarar för kommunikation med IoT Hub. Moby daemon ansvarar för behållar hantering, och kommunicerar med behållar register. Detaljerade anvisningar finns i avsnittet [Konfigurera daemonar](#configure-the-daemons) i den här artikeln. 

   Det här steget är en engångs process som utförs på den IoT Edge enheten första gången du ställer in den.

3. **Konfigurera egenskaperna för IoT Edge agent i filen config. yaml på enheten.**

   IoT Edge daemon startar edgeAgent-modulen från början, men edgeAgent-modulen ansvarar för att hämta distributions manifestet från IoT Hub och starta alla andra moduler. Konfigurera edgeAgent-modulens miljövariabler manuellt på själva enheten för att den IoT Edge agenten ska göra den första anslutningen till IoT Hub. Efter den första anslutningen kan du konfigurera edgeAgent-modulen från en annan plats. Detaljerade anvisningar finns i avsnittet [konfigurera IoT Edge agent](#configure-the-iot-edge-agent) i den här artikeln.

   Det här steget är en engångs process som utförs på den IoT Edge enheten första gången du ställer in den.

4. **För alla framtida modul distributioner ställer du in miljövariabler för alla moduler som kommunicerar via proxyservern.**

   När din IoT Edge-enhet har kon figurer ATS och anslutits till IoT Hub via proxyservern måste du upprätthålla anslutningen i alla framtida distributioner av modulen. Detaljerade anvisningar finns i avsnittet [Konfigurera distributions manifest](#configure-deployment-manifests) i den här artikeln. 

   Det här steget är en fort löp ande process som utförs på distans, så att varje ny modul eller distributions uppdatering upprätthåller enhetens möjlighet att kommunicera via proxyservern. 

## <a name="know-your-proxy-url"></a>Vet proxy-URL

Innan du påbörjar något av stegen i den här artikeln måste du känna till din proxy-URL.

Proxy-URL: er ska ha följande format: **protokoll**://**proxy_host**:**proxy_port**.

* **Protokollet** är antingen http eller https. Docker daemon kan använda antingen protokollet, beroende på dina behållar register inställningar, men IoT Edge daemon-och runtime-behållare bör alltid använda HTTP för att ansluta till proxyservern.

* **Proxy_host** är en adress för proxyservern. Om proxyservern kräver autentisering kan du ange dina autentiseringsuppgifter som en del av proxy-värden med följande format: **användare**:**lösen ord**\@**proxy_host**.

* **Proxy_port** är nätverks porten där proxyservern svarar på nätverks trafik.

## <a name="install-the-runtime-through-a-proxy"></a>Installera körningen via en proxy

Oavsett om din IoT Edge-enhet körs på Windows eller Linux måste du komma åt installations paketen via proxyservern. Följ stegen för att installera IoT Edge runtime via en proxyserver, beroende på ditt operativ system. 

### <a name="linux"></a>Linux

Om du installerar IoT Edge-körningen på en Linux-enhet, konfigurera package manager att gå igenom din proxyserver för att komma åt installationspaketet. Konfigurera till exempel [apt-get för att använda en http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). När paket hanteraren har kon figurer ATS följer du anvisningarna i [installera Azure IoT Edge runtime på Linux](how-to-install-iot-edge-linux.md) som vanligt.

### <a name="windows"></a>Windows

Om du installerar IoT Edge runtime på en Windows-enhet måste du gå igenom proxyservern två gånger. Den första anslutningen laddar ned installations skript filen och den andra anslutningen är under installationen för att ladda ned nödvändiga komponenter. Du kan konfigurera proxyinformation i Windows-inställningar eller inkludera proxyinformation direkt i PowerShell-kommandona. 

Följande steg visar ett exempel på en Windows-installation med hjälp av argumentet `-proxy`:

1. Kommandot anropa-WebRequest behöver proxyinformation för att komma åt installations skriptet. Sedan behöver kommandot Deploy-IoTEdge information om proxyn för att ladda ned installationsfilerna. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Kommandot Initialize-IoTEdge behöver inte gå via proxyservern, så det andra steget kräver bara proxyinformation för Invoke-webbegäran.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Om du har komplicerade autentiseringsuppgifter för proxyservern som inte kan ingå i URL: en, använder du parametern `-ProxyCredential` i `-InvokeWebRequestParameters`. Exempel:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Mer information om parametrar finns i [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Mer information om installations alternativ för Windows, inklusive offline-installation, finns i [installera Azure IoT Edge runtime i Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurera Daemon

IoT Edge förlitar sig på två daemonar som körs på IoT Edge enheten. Moby daemon gör webb förfrågningar till att hämta behållar avbildningar från behållar register. Daemonen IoT Edge gör webbegäranden till att kommunicera med IoT Hub.

Både Moby och IoT Edge daemon måste konfigureras för att använda proxyservern för kontinuerlig enhets funktion. Det här steget sker på IoT Edge enhet under installationen av den första enheten. 

### <a name="moby-daemon"></a>Moby daemon

Eftersom Moby är byggd på Docker, se Docker-dokumentationen för att konfigurera Moby-daemon med miljövariabler. De flesta behållar register (inklusive DockerHub och Azure Container register) stöder HTTPS-begäranden, så den parameter som du bör ange är **HTTPS_PROXY**. Om du hämtar bilder från ett register som inte stöder TLS (Transport Layer Security) bör du ange **HTTP_PROXY** parameter. 

Välj den artikel som gäller för IoT Edge enhetens operativ system: 

* [Konfigurera Docker daemon på Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Moby daemon på Linux-enheter behåller namn Docker.
* [Konfigurera Docker daemon i Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Daemon för Moby på Windows-enheter kallas iotedge-Moby. Namnen skiljer sig åt eftersom det är möjligt att köra både Docker Desktop och Moby parallellt på en Windows-enhet. 

### <a name="iot-edge-daemon"></a>Daemon för IoT Edge

IoT Edge daemon har kon figurer ATS på liknande sätt som Moby daemon. Använd följande steg för att ange en miljövariabel för tjänsten baserat på ditt operativsystem. 

IoT Edge daemon använder alltid HTTPS för att skicka begär anden till IoT Hub.

#### <a name="linux"></a>Linux

Öppna en redigerare i terminalen för att konfigurera IoT Edge-daemon. 

```bash
sudo systemctl edit iotedge
```

Ange följande text och Ersätt **\<proxy-URL >** med proxyserverns adress och port. Spara och avsluta. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Uppdatera Service Manager för att hämta den nya konfigurationen för IoT Edge.

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

Öppna ett PowerShell-fönster som administratör och kör följande kommando för att redigera registret med den nya miljövariabeln. Ersätt **\<proxy-url >** med proxyserverns adress och port. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Starta om IoT Edge för att ändringarna ska börja gälla.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurera IoT Edge agent

Den IoT Edge agenten är den första modulen som startar på valfri IoT Edge enhet. Den startas för första gången baserat på informationen i filen config.yaml IoT Edge. IoT Edge agenten ansluter sedan till IoT Hub för att hämta distributions manifest, som deklarerar vilka andra moduler som ska distribueras på enheten.

Det här steget sker en gång på IoT Edge enheten under den första installationen av enheten. 

1. Öppna filen config.yaml på din IoT Edge-enhet. På Linux-system finns den här filen på **/etc/iotedge/config.yaml**. På Windows-system finns den här filen på **C:\ProgramData\iotedge\config.yaml**. Konfigurationsfilen är skyddad, så du behöver administrativ behörighet att komma åt den. På Linux-system använder du kommandot `sudo` innan du öppnar filen i önskad text redigerare. Öppna en text redigerare som till exempel anteckningar som administratör i Windows och öppna sedan filen. 

2. I filen config. yaml, letar du reda på avsnittet om **Edge agent module** . Den IoT Edge agent definitionen innehåller en **miljö** -parameter där du kan lägga till miljövariabler. 

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

När din IoT Edge enhet har kon figurer ATS för att fungera med proxyservern måste du fortsätta att deklarera miljövariablerna i framtida distributions manifest. Du kan redigera distributions manifest antingen med hjälp av guiden Azure Portal eller genom att redigera en JSON-fil för distributions manifestet. 

Konfigurera alltid de två modulerna för körning, edgeAgent och edgeHub, för att kommunicera via proxyservern så att de kan underhålla en anslutning med IoT Hub. Om du tar bort proxyinformation från edgeAgent-modulen är det enda sättet att återupprätta anslutningen genom att redigera filen config. yaml på enheten, enligt beskrivningen i föregående avsnitt. 

Andra IoT Edge moduler som ansluter till Internet ska konfigureras för att kommunicera via proxyservern. Moduler som dirigerar sina meddelanden via edgeHub eller som bara kommunicerar med andra moduler på enheten behöver dock inte information om proxyservern. 

Det här steget pågår under hela IoT Edge enhetens livs längd. 

### <a name="azure-portal"></a>Azure Portal

När du använder guiden **Ange moduler** för att skapa distributioner för IoT Edge enheter, har alla moduler ett **miljö variabel** avsnitt som du kan använda för att konfigurera anslutningar till proxyservern. 

Om du vill konfigurera IoT Edge agenten och IoT Edge Hub-moduler, väljer du **Konfigurera avancerade Edge runtime-inställningar** i det första steget i guiden. 

![Konfigurera avancerade Edge-körningsinställningar](./media/how-to-configure-proxy-support/configure-runtime.png)

Lägg till miljövariabeln **https_proxy** i både modulen IoT Edge agent och IoT Edge hubb. Om du har inkluderat miljövariabeln **UpstreamProtocol** i filen config. yaml på din IoT Edge-enhet, lägger du till den i modulen för att skapa IoT Edge-agenten. 

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
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Om du har inkluderat miljövariabeln **UpstreamProtocol** i filen config. yaml på din IoT Edge-enhet, lägger du till den IoT Edge i definitionen för modulen för. 

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

Läs mer om rollerna för [IoT Edge runtime](iot-edge-runtime.md).

Felsök installations-och konfigurations fel med [vanliga problem och lösningar för Azure IoT Edge](troubleshoot.md)

