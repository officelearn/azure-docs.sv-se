---
title: Konfigurera enheter för nätverks proxyservrar – Azure IoT Edge | Microsoft-dokument
description: Konfigurera Azure IoT Edge-körningen och alla Internet-vända IoT Edge-moduler för att kommunicera via en proxyserver.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 270e6a0173ed0088ff5d37c989947f5272634200
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687187"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver

IoT Edge-enheter skickar HTTPS-begäranden för att kommunicera med IoT Hub. Om enheten är ansluten till ett nätverk som använder en proxyserver måste du konfigurera IoT Edge-körningen så att den kommunicerar via servern. Proxyservrar kan också påverka enskilda IoT Edge-moduler om de gör HTTP- eller HTTPS-begäranden som inte dirigeras via IoT Edge-hubben.

Den här artikeln går igenom följande fyra steg för att konfigurera och sedan hantera en IoT Edge-enhet bakom en proxyserver:

1. **Installera IoT Edge-körningen på enheten.**

   IoT Edge-installationsskripten hämtar paket och filer från Internet, så enheten måste kommunicera via proxyservern för att göra dessa begäranden. Detaljerade steg finns i [avsnittet Installera körningen via ett proxyavsnitt](#install-the-runtime-through-a-proxy) i den här artikeln. För Windows-enheter innehåller installationsskriptet också ett [offlineinstallationsalternativ.](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

   Det här steget är en engångsprocess som utförs på IoT Edge-enheten när du först konfigurerade den. Samma anslutningar krävs också när du uppdaterar IoT Edge-körningen.

2. **Konfigurera Docker-demonen och IoT Edge-demonen på enheten.**

   IoT Edge använder två demoner på enheten, som båda måste göra webbbegäranden via proxyservern. IoT Edge-demonen ansvarar för kommunikationen med IoT Hub. Moby-demonen ansvarar för containerhanteringen, så kommunicerar med containerregister. Detaljerade steg finns i avsnittet [Konfigurera demoner](#configure-the-daemons) i den här artikeln.

   Det här steget är en engångsprocess som utförs på IoT Edge-enheten när du först konfigurerade den.

3. **Konfigurera egenskaperna för IoT Edge-agenten i filen config.yaml på enheten.**

   IoT Edge-demonen startar edgeAgent-modulen först, men sedan ansvarar edgeAgent-modulen för att hämta distributionsmanifestet från IoT Hub och starta alla andra moduler. För att IoT Edge-agenten ska kunna upprätta den första anslutningen till IoT Hub konfigurerar du edgeAgent-modulmiljöns variabler manuellt på själva enheten. Efter den första anslutningen kan du fjärrkonfigurera edgeAgent-modulen. Detaljerade steg finns i avsnittet [Konfigurera IoT Edge-agenten](#configure-the-iot-edge-agent) i den här artikeln.

   Det här steget är en engångsprocess som utförs på IoT Edge-enheten när du först konfigurerade den.

4. **För alla framtida moduldistributioner ställer du in miljövariabler för alla moduler som kommunicerar via proxyn.**

   När din IoT Edge-enhet har konfigurerats och anslutits till IoT Hub via proxyservern måste du underhålla anslutningen i alla framtida moduldistributioner. Detaljerade steg finns i avsnittet [Konfigurera distributionsmanifest](#configure-deployment-manifests) i den här artikeln.

   Det här steget är en pågående process som utförs på distans så att varje ny modul eller distributionsuppdatering upprätthåller enhetens förmåga att kommunicera via proxyservern.

## <a name="know-your-proxy-url"></a>Lär känna din proxy-URL

Innan du påbörjar något av stegen i den här artikeln måste du känna till proxy-URL:en.

Proxy-URL:er har följande format: **protokoll**://**proxy_host:****proxy_port**.

* **Protokollet** är antingen HTTP eller HTTPS. Docker-demonen kan använda båda protokollen, beroende på vilka behållarregisterinställningar som finns, men IoT Edge-demonen och körningsbehållarna bör alltid använda HTTP för att ansluta till proxyn.

* **Den proxy_host** är en adress för proxyservern. Om proxyservern kräver autentisering kan du ange att dina autentiseringsuppgifter som en del av proxyvärden får följande format: **användare:****lösenord**\@**proxy_host**.

* Den **proxy_port** är den nätverksport där proxyn svarar på nätverkstrafik.

## <a name="install-the-runtime-through-a-proxy"></a>Installera körningen via en proxy

Oavsett om din IoT Edge-enhet körs på Windows eller Linux måste du komma åt installationspaketen via proxyservern. Beroende på vilket operativsystem du använder följer stegen för att installera IoT Edge-körningen via en proxyserver.

### <a name="linux-devices"></a>Linux-enheter

Om du installerar IoT Edge-körningen på en Linux-enhet konfigurerar du pakethanteraren så att den går igenom proxyservern för att komma åt installationspaketet. Ställ till exempel [in apt-get för att använda en http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). När pakethanteraren har konfigurerats följer du instruktionerna i [Installera Azure IoT Edge-körningen på Linux](how-to-install-iot-edge-linux.md) som vanligt.

### <a name="windows-devices"></a>Windows-enheter

Om du installerar IoT Edge-körningen på en Windows-enhet måste du gå igenom proxyservern två gånger. Den första anslutningen hämtar installationsskriptfilen och den andra anslutningen är under installationen för att hämta nödvändiga komponenter. Du kan konfigurera proxyinformation i Windows-inställningarna eller inkludera proxyinformation direkt i PowerShell-kommandona.

Följande steg visar ett exempel på `-proxy` en Windows-installation med argumentet:

1. Kommandot Invoke-WebRequest behöver proxyinformation för att komma åt installationsskriptet. Då behöver kommandot Deploy-IoTEdge proxyinformation för att hämta installationsfilerna.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Kommandot Initialize-IoTEdge behöver inte gå igenom proxyservern, så det andra steget kräver bara proxyinformation för Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Om du har komplicerade autentiseringsuppgifter för proxyservern som inte `-ProxyCredential` kan `-InvokeWebRequestParameters`inkluderas i URL:en använder du parametern i . Exempel:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Mer information om proxyparametrar finns i [Anropa-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Mer information om installationsalternativ för Windows, inklusive offlineinstallation, finns i [Installera Azure IoT Edge-körningen i Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurera demonerna

IoT Edge förlitar sig på två demoner som körs på IoT Edge-enheten. Moby-demonen gör webbförfrågningar för att hämta behållaravbildningar från behållarregister. IoT Edge-demonen gör webbförfrågningar för att kommunicera med IoT Hub.

Både Moby och IoT Edge demoner måste konfigureras för att använda proxyservern för pågående enhetsfunktioner. Det här steget sker på IoT Edge-enheten under den första enhetskonfigurationen.

### <a name="moby-daemon"></a>Moby demon

Eftersom Moby är byggt på Docker, se Docker-dokumentationen för att konfigurera Moby-demonen med miljövariabler. De flesta behållarregister (inklusive DockerHub och Azure Container Registerstries) stöder HTTPS-begäranden, så parametern som du bör ange är **HTTPS_PROXY**. Om du hämtar bilder från ett register som inte stöder transportlagersäkerhet (TLS) bör du ange **parametern HTTP_PROXY.**

Välj den artikel som gäller för operativsystemet IoT Edge-enheten:

* [Konfigurera Docker-demon på Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Moby-demonen på Linux-enheter behåller namnet Docker.
* [Konfigurera Docker-demon i Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Moby demon på Windows-enheter kallas iotedge-moby. Namnen är olika eftersom det är möjligt att köra både Docker Desktop och Moby parallellt på en Windows-enhet.

### <a name="iot-edge-daemon"></a>IoT Kant daemon

IoT Edge demon är konfigurerad på ett liknande sätt som Moby demon. Använd följande steg för att ange en miljövariabel för tjänsten, baserat på ditt operativsystem.

IoT Edge-demonen använder alltid HTTPS för att skicka begäranden till IoT Hub.

#### <a name="linux"></a>Linux

Öppna en redigerare i terminalen för att konfigurera IoT Edge-demonen.

```bash
sudo systemctl edit iotedge
```

Ange följande text och ersätt ** \<proxy-URL->** med proxyserverns adress och port. Spara och avsluta sedan.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Uppdatera servicehanteraren för att hämta den nya konfigurationen för IoT Edge.

```bash
sudo systemctl daemon-reload
```

Starta om IoT Edge för att ändringarna ska börja gälla.

```bash
sudo systemctl restart iotedge
```

Kontrollera att din miljövariabel har skapats och att den nya konfigurationen har lästs in.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Öppna ett PowerShell-fönster som administratör och kör följande kommando för att redigera registret med den nya miljövariabeln. Ersätt ** \<proxy url>** med din proxyserver adress och port.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Starta om IoT Edge för att ändringarna ska börja gälla.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurera IoT Edge-agenten

IoT Edge-agenten är den första modulen som startar på en IoT Edge-enhet. Den har startat för första gången baserat på informationen i IoT Edge config.yaml-filen. IoT Edge-agenten ansluter sedan till IoT Hub för att hämta distributionsmanifest, som deklarerar vilka andra moduler som ska distribueras på enheten.

Det här steget sker en gång på IoT Edge-enheten under den första enhetskonfigurationen.

1. Öppna filen config.yaml på IoT Edge-enheten. På Linux-system finns den här filen på **/etc/iotedge/config.yaml**. På Windows-system finns den här filen på **C:\ProgramData\iotedge\config.yaml**. Konfigurationsfilen är skyddad, så du behöver administratörsbehörighet för att komma åt den. På Linux-system `sudo` använder du kommandot innan du öppnar filen i önskad textredigerare. Öppna en textredigerare som Anteckningar som administratör i Windows och öppna sedan filen.

2. Leta reda på avsnittet **Edge Agent-modulens specifikation** i filen config.yaml. IoT Edge-agentdefinitionen innehåller en **env-parameter** där du kan lägga till miljövariabler.

3. Ta bort de kladdiga parenteserna som är platshållare för parametern env och lägg till den nya variabeln på en ny rad. Kom ihåg att indrag i YAML är två blanksteg.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge-körningen använder AMQP som standard för att prata med IoT Hub. Vissa proxyservrar blockerar AMQP-portar. Om så är fallet måste du också konfigurera edgeAgent för att använda AMQP via WebSocket. Lägg till en andra miljövariabel.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent definition med miljövariabler](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Spara ändringarna på config.yaml och stäng redigeraren. Starta om IoT Edge för att ändringarna ska börja gälla.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Konfigurera distributionsmanifest  

När din IoT Edge-enhet har konfigurerats för att fungera med proxyservern måste du fortsätta att deklarera HTTPS_PROXY miljövariabeln i framtida distributionsmanifest. Du kan redigera distributionsmanifest antingen med hjälp av Azure-portalguiden eller genom att redigera en distributionsmanifest JSON-fil.

Konfigurera alltid de två runtime-modulerna edgeAgent och edgeHub för att kommunicera via proxyservern så att de kan upprätthålla en anslutning till IoT Hub. Om du tar bort proxyinformationen från edgeAgent-modulen är det enda sättet att återupprätta anslutningen genom att redigera filen config.yaml på enheten, enligt beskrivningen i föregående avsnitt.

Förutom edgeAgent- och edgeHub-modulerna kan andra moduler behöva proxykonfigurationen. Det här är moduler som behöver komma åt Azure-resurser förutom IoT Hub, till exempel blob-lagring, och måste ha den HTTPS_PROXY variabeln som angetts för modulen i distributionsmanifestfilen.

Följande procedur gäller under hela livslängden för IoT Edge-enheten.

### <a name="azure-portal"></a>Azure Portal

När du använder guiden **Ange moduler** för att skapa distributioner för IoT Edge-enheter har varje modul ett avsnitt **med miljövariabler** som du kan använda för att konfigurera proxyserveranslutningar.

Om du vill konfigurera IoT Edge-agenten och IoT Edge-hubbmodulerna väljer du **Körtidsinställningar** i det första steget i guiden.

![Konfigurera avancerade edge runtime-inställningar](./media/how-to-configure-proxy-support/configure-runtime.png)

Lägg till **den https_proxy** miljövariabeln i både IoT Edge-agenten och IoT Edge-hubmoduldefinitionerna. Om du har inkluderat miljövariabeln **UpstreamProtocol** i filen config.yaml på IoT Edge-enheten lägger du också till den i IoT Edge-agentmoduldefinitionen.

![Ange https_proxy miljövariabel](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Alla andra moduler som du lägger till i ett distributionsmanifest följer samma mönster. På sidan där du anger modulnamn och bild finns ett avsnitt om miljövariabler.

### <a name="json-deployment-manifest-files"></a>JSON-distributionsmanifestfiler

Om du skapar distributioner för IoT Edge-enheter med mallarna i Visual Studio-kod eller genom att manuellt skapa JSON-filer kan du lägga till miljövariablerna direkt i varje moduldefinition.

Använd följande JSON-format:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Med de miljövariabler som ingår bör moduldefinitionen se ut som följande edgeHub-exempel:

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

Om du har inkluderat miljövariabeln **UpstreamProtocol** i filen confige.yaml på IoT Edge-enheten lägger du också till den i IoT Edge-agentmoduldefinitionen.

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

Läs mer om rollerna för [IoT Edge-körningen](iot-edge-runtime.md).

Felsöka installations- och konfigurationsfel med [vanliga problem och lösningar för Azure IoT Edge](troubleshoot.md)
