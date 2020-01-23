---
title: Installera Azure IoT Edge i Windows | Microsoft Docs
description: Azure IoT Edge installations anvisningar för Windows 10, Windows Server och Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.openlocfilehash: 38e688528d7445b16141d9f1ecc0318faf07e140
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510013"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installera Azure IoT Edge runtime i Windows

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet.

Mer information om IoT Edge runtime finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller anvisningar för att installera Azure IoT Edge runtime på Windows x64-systemet (AMD/Intel) med hjälp av Windows-behållare.

> [!NOTE]
> Ett känt problem med Windows-operativsystemet förhindrar över gången till ström spar läge och vilo läge när IoT Edge moduler (process isolerade Windows Nano Server-behållare) körs. Det här problemet påverkar batteriets livs längd på enheten.
>
> Som en lösning använder du kommandot `Stop-Service iotedge` för att stoppa eventuella IoT Edge moduler som körs innan du använder dessa energi tillstånd.

Användning av Linux-behållare i Windows-system är inte en rekommenderad eller produktions konfiguration som stöds för Azure IoT Edge. Det kan dock användas för utveckling och testning. Läs mer i [använda IoT Edge i Windows för att köra Linux-behållare](how-to-install-iot-edge-windows-with-linux.md).

För information om vad som ingår i den senaste versionen av IoT Edge, se [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Krav

Använd det här avsnittet för att se om din Windows-enhet har stöd för IoT Edge och förbereda den för en behållar motor före installationen.

### <a name="supported-windows-versions"></a>Windows-versioner som stöds

I utvecklings-och test scenarier kan Azure IoT Edge med Windows-behållare installeras på alla versioner av Windows 10 eller Windows Server 2019 (build 17763) som stöder funktionen containers. Information om vilka operativ system som för närvarande stöds för produktions scenarier finns i [Azure IoT Edge system som stöds](support.md#operating-systems).

IoT core-enheter måste innehålla den valfria funktionen IoT Core – Windows-behållare som stöder IoT Edge Runtime. Använd följande kommando i en [fjärran sluten PowerShell-session](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) för att kontrol lera att Windows-behållare stöds på din enhet:

```powershell
Get-Service vmcompute
```

Om tjänsten finns får du ett lyckat svar med tjänstens status som **körs**. Om vmcompute-tjänsten inte hittas uppfyller enheten inte kraven för IoT Edge. Kontakta din maskin varu leverantör för att be om stöd för den här funktionen.

### <a name="prepare-for-a-container-engine"></a>Förbereda för en behållar motor

Azure IoT Edge förlitar sig på en [OCI-kompatibel](https://www.opencontainers.org/) container motor. För produktions scenarier använder du Moby-motorn som ingår i installations skriptet för att köra Windows-behållare på din Windows-enhet.

## <a name="install-iot-edge-on-a-new-device"></a>Installera IoT Edge på en ny enhet

>[!NOTE]
>Azure IoT Edge-programvarupaket omfattas licensvillkoren finns i paket (i katalogen licens). Läs igenom licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

Ett PowerShell-skript laddar ned och installerar Azure IoT Edge Security daemon. Security daemon startar sedan den första av två körnings moduler, IoT Edge agent, som möjliggör fjärrdistributioner av andra moduler.

>[!TIP]
>För IoT core-enheter rekommenderar vi att du kör installations kommandona med en RemotePowerShell-session. Mer information finns i [använda PowerShell för Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

När du installerar IoT Edge runtime för första gången på en enhet måste du etablera enheten med en identitet från en IoT-hubb. En enskild IoT Edge enhet kan allokeras manuellt med hjälp av en enhets anslutnings sträng från IoT Hub. Du kan också använda enhets etablerings tjänsten (DPS) för att etablera enheter automatiskt, vilket är användbart när du har många enheter att konfigurera. Beroende på föredrar etablering, väljer du lämplig installationsskriptet.

I följande avsnitt beskrivs vanliga användnings fall och parametrar för installations skriptet för IoT Edge på en ny enhet.

### <a name="option-1-install-and-manually-provision"></a>Alternativ 1: Installera och etablera manuellt

I det här första alternativet anger du en **enhets anslutnings sträng** som genereras av IoT Hub att etablera enheten.

Det här exemplet visar en manuell installation med Windows-behållare:

1. Registrera en ny IoT Edge enhet och hämta **anslutnings strängen för enheten**om du inte redan gjort det. Kopiera anslutnings strängen och Använd den senare i det här avsnittet. Du kan utföra det här steget med följande verktyg:

   * [Azure-portalen](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio-kod](how-to-register-device.md#register-with-visual-studio-code)

2. Kör PowerShell som administratör.

   >[!NOTE]
   >Använd en AMD64-session av PowerShell för att installera IoT Edge, inte PowerShell (x86). Om du inte är säker på vilken typ av session du använder kör du följande kommando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Kommandot **Deploy-IoTEdge** kontrollerar att Windows-datorn finns på en version som stöds, aktiverar funktionen containers och laddar sedan ned Moby runtime och IoT Edge Runtime. Kommandot använder som standard Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. I det här läget kan IoT core-enheter startas om automatiskt. Andra Windows 10-eller Windows Server-enheter kan bli ombedd att starta om. Om så är fallet startar du om enheten nu. När enheten är klar kör du PowerShell som administratör igen.

5. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge runtime på din dator. Kommandot är standardvärdet för manuell etablering med Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. När du uppmanas till det anger du den enhets anslutnings sträng som du hämtade i steg 1. Enhets anslutnings strängen kopplar den fysiska enheten med ett enhets-ID i IoT Hub.

   Enhets anslutnings strängen har följande format och ska inte innehålla citat tecken: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Använd stegen i [kontrol lera att installationen har slutförts](#verify-successful-installation) för att kontrol lera status för IoT Edge på enheten.

När du installerar och etablerar en enhet manuellt kan du använda ytterligare parametrar för att ändra installationen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Peka installations programmet till en offline-katalog
* Deklarera en bestämd agent behållar avbildning och ange autentiseringsuppgifter om den finns i ett privat register

Om du vill ha mer information om dessa installations alternativ kan du gå vidare till Lär dig mer om [alla installations parametrar](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Alternativ 2: Installera och etablera automatiskt

I det här andra alternativet etablerar du enheten med hjälp av IoT Hub Device Provisioning Service. Ange **omfattnings-ID: t** från en enhets etablerings tjänst instans tillsammans med annan information som är speciell för den önskade [mekanismen för attestering](../iot-dps/concepts-security.md#attestation-mechanism):

* [Skapa och etablera en simulerad IoT Edge enhet med en virtuell TPM i Windows](how-to-auto-provision-simulated-device-windows.md)
* [Skapa och etablera en IoT Edge enhet med hjälp av symmetrisk nyckel attestering](how-to-auto-provision-symmetric-keys.md)

När du installerar och etablerar en enhet automatiskt kan du använda ytterligare parametrar för att ändra installationen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Peka installations programmet till en offline-katalog
* Deklarera en bestämd agent behållar avbildning och ange autentiseringsuppgifter om den finns i ett privat register

Om du vill ha mer information om dessa installations alternativ kan du fortsätta att läsa den här artikeln eller gå vidare till Lär dig om [alla installations parametrar](#all-installation-parameters).

## <a name="offline-installation"></a>Offline-installation

Under installationen hämtas två filer:

* Microsoft Azure IoT Edge CAB, som innehåller IoT Edge Security daemon (iotedged), Moby container Engine och Moby CLI.
* MSI C++ för Visual Redistributable Package (VC Runtime)

Du kan ladda ned en eller båda filerna i förväg till enheten och sedan peka installations skriptet i katalogen som innehåller filerna. Installations programmet kontrollerar katalogen först och hämtar sedan endast komponenter som inte hittas. Om alla filer är tillgängliga offline kan du installera utan Internet anslutning. Du kan också använda den här funktionen för att installera en speciell version av komponenterna.  

De senaste IoT Edge-installationsfilerna tillsammans med tidigare versioner finns i [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases).

Om du vill installera med offline-komponenter använder du parametern `-OfflineInstallationPath` som en del av kommandot Deploy-IoTEdge och anger den absoluta sökvägen till fil katalogen. Exempel:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Du kan också använda parametern för offline-installation av sökvägen med kommandot Update-IoTEdge, som introducerades senare i den här artikeln.

## <a name="verify-successful-installation"></a>Verifiera installationen

Kontrollera status för IoT Edge-tjänsten. Den ska visas som körs.  

```powershell
Get-Service iotedge
```

Granska loggarna för tjänsten från de senaste 5 minuterna. Om du precis har installerat IoT Edge runtime kan du se en lista över fel från tiden mellan körningen av **Deploy-IoTEdge** och **Initialize-IoTEdge**. De här felen förväntas när tjänsten försöker starta innan den konfigureras.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Kör en automatisk kontroll av de vanligaste konfigurations-och nätverks felen.

```powershell
iotedge check
```

Lista med moduler. När en ny installation har slutförts är den enda modul som du bör se **edgeAgent**. När du har [distribuerat IoT Edge moduler](how-to-deploy-modules-portal.md) för första gången, kommer den andra systemmodulen, **edgeHub**, att starta även på enheten.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Hantera-modul behållare

Den IoT Edge tjänsten kräver att en behållar motor körs på enheten. När du distribuerar en modul till en enhet använder IoT Edge runtime container Engine för att hämta behållar avbildningen från ett register i molnet. Med tjänsten IoT Edge kan du interagera med dina moduler och hämta loggar, men ibland kanske du vill använda behållar motorn för att interagera med själva behållaren.

Mer information om modulblad finns i [förstå Azure IoT Edge moduler](iot-edge-modules.md).

Om du kör Windows-behållare på Windows IoT Edge-enheten, ingår IoT Edge-installationen som Moby container Engine. Moby-motorn baserades på samma standarder som Docker och har utformats för att köras parallellt på samma dator som Docker Desktop. Av den anledningen måste du, om du vill rikta behållarna som hanteras av Moby-motorn, specifikt rikta motorn i stället för Docker.

Om du till exempel vill visa alla Docker-avbildningar använder du följande kommando:

```powershell
docker images
```

Om du vill visa alla Moby-avbildningar ändrar du samma kommando med en pekare till Moby-motorn:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Motorns URI visas i utdata från installations skriptet, eller så hittar du den i avsnittet container runtime-inställningar för filen config. yaml.

![moby_runtime-URI i config. yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Mer information om kommandon som du kan använda för att interagera med behållare och avbildningar som körs på enheten finns i [Docker kommando rads gränssnitt](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Uppdatera en befintlig installation

Om du redan har installerat IoT Edge runtime på en enhet innan och etablerade den med en identitet från IoT Hub kan du uppdatera körningen utan att behöva ange enhets informationen på annat sätt.

Mer information finns i [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).

Det här exemplet visar en installation som pekar på en befintlig konfigurations fil och använder Windows-behållare:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

När du uppdaterar IoT Edge kan du använda ytterligare parametrar för att ändra uppdateringen, inklusive:

* Dirigera trafik för att gå igenom en proxyserver eller
* Peka installations programmet till en offline-katalog
* Starta om utan att ange en uppstarts ruta om det behövs

Du kan inte deklarera en IoT Edge agent behållar avbildning med skript parametrar eftersom informationen redan har angetts i konfigurations filen från den tidigare installationen. Om du vill ändra agent behållar avbildningen gör du det i filen config. yaml.

Om du vill ha mer information om de här uppdaterings alternativen kan du använda kommandot `Get-Help Update-IoTEdge -full` eller referera till [alla installations parametrar](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge installationen från Windows-enheten använder du följande kommando från ett administrations PowerShell-fönster. Det här kommandot tar bort IoT Edge runtime, tillsammans med den befintliga konfigurationen och Moby-motorns data.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Kommandot Uninstall-IoTEdge fungerar inte på Windows IoT Core. Om du vill ta bort IoT Edge från Windows IoT core-enheter måste du distribuera om Windows IoT Core-avbildningen.

Om du vill ha mer information om avinstallations alternativ använder du kommandot `Get-Help Uninstall-IoTEdge -full`.

## <a name="all-installation-parameters"></a>Alla installations parametrar

I föregående avsnitt introducerades vanliga installations scenarier med exempel på hur du använder parametrar för att ändra installations skriptet. Det här avsnittet innehåller referens tabeller för de gemensamma parametrarna som används för att installera, uppdatera eller avinstallera IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Kommandot Deploy-IoTEdge hämtar och distribuerar IoT Edge Security daemon och dess beroenden. Distributions kommandot accepterar dessa vanliga parametrar, bland annat. För den fullständiga listan använder du kommandot `Get-Help Deploy-IoTEdge -full`.  

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Container** | **Windows** eller **Linux** | Om inget behållar operativ system anges är Windows standardvärdet.<br><br>För Windows-behållare använder IoT Edge Moby container Engine som ingår i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **Proxy** | Proxy-URL | Ta med den här parametern om enheten måste gå igenom en proxyserver för att komma åt Internet. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Katalogsökväg | Om den här parametern tas med, kontrollerar installations programmet katalogen för de IoT Edge CAB-och VC runtime-MSI-filer som krävs för installationen. Filer som inte hittas i katalogen laddas ned. Om båda filerna finns i katalogen kan du installera IoT Edge utan Internet anslutning. Du kan också använda den här parametern för att använda en speciell version. |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | ingen | Med den här flaggan kan distributions skriptet starta om datorn utan att behöva ange om det behövs. |

### <a name="initialize-iotedge"></a>Initiera-IoTEdge

Kommandot Initialize-IoTEdge konfigurerar IoT Edge med enhetens anslutnings sträng och drift information. Mycket av den information som genereras av det här kommandot lagras sedan i iotedge\config.yaml-filen. Initierings kommandot accepterar dessa vanliga parametrar, bland annat. För den fullständiga listan använder du kommandot `Get-Help Initialize-IoTEdge -full`.

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Manuell** | Inget | **Växlings parameter**. Om ingen etablerings typ anges, är manuell standardvärdet.<br><br>Deklarerar att du kommer att ange en enhets anslutnings sträng för att etablera enheten manuellt |
| **–** | Inget | **Växlings parameter**. Om ingen etablerings typ anges, är manuell standardvärdet.<br><br>Förklarar att du kommer att tillhandahålla ett omfångs-ID för enhets etablerings tjänsten (DPS) och din enhets registrerings-ID för att etablera genom DPS.  |
| **DeviceConnectionString** | En anslutnings sträng från en IoT Edge enhet som är registrerad i en IoT Hub, med enkla citat tecken | **Krävs** för manuell installation. Om du inte anger någon anslutnings sträng i skript parametrarna uppmanas du att ange en under installationen. |
| **ScopeId** | Ett scope-ID från en instans av enhets etablerings tjänsten som är kopplad till din IoT Hub. | **Krävs** för DPS-installation. Om du inte anger ett omfångs-ID i skript parametrarna uppmanas du att ange ett under installationen. |
| **RegistrationId** | Ett registrerings-ID som genereras av din enhet | **Krävs** för DPS-installation om du använder TPM eller symmetrisk nyckel attestering. |
| **SymmetricKey** | Den symmetriska nyckel som används för att etablera IoT Edge enhets identitet när DPS används | **Krävs** för DPS-installation om du använder symmetrisk nyckel attestering. |
| **Container** | **Windows** eller **Linux** | Om inget behållar operativ system anges är Windows standardvärdet.<br><br>För Windows-behållare använder IoT Edge Moby container Engine som ingår i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **AgentImage** | IoT Edge agent avbildnings-URI | Som standard använder en ny IoT Edge-installation den senaste rullande taggen för IoT Edge agent-avbildningen. Använd den här parametern för att ange en speciell tagg för avbildnings versionen eller för att ange en egen agent avbildning. Mer information finns i [förstå IoT Edge Taggar](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Användarnamn** | Användar namn för container registret | Använd bara den här parametern om du anger parametern-AgentImage till en behållare i ett privat register. Ange ett användar namn med åtkomst till registret. |
| **Lösenord** | Säker lösen ords sträng | Använd bara den här parametern om du anger parametern-AgentImage till en behållare i ett privat register. Ange lösen ordet för att få åtkomst till registret. |

### <a name="update-iotedge"></a>Uppdatera – IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Container** | **Windows** eller **Linux** | Om inget behållar-OS anges är Windows standardvärdet. För Windows-behållare kommer en behållar motor att inkluderas i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **Proxy** | Proxy-URL | Ta med den här parametern om enheten måste gå igenom en proxyserver för att komma åt Internet. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Katalogsökväg | Om den här parametern tas med, kontrollerar installations programmet katalogen för de IoT Edge CAB-och VC runtime-MSI-filer som krävs för installationen. Filer som inte hittas i katalogen laddas ned. Om båda filerna finns i katalogen kan du installera IoT Edge utan Internet anslutning. Du kan också använda den här parametern för att använda en speciell version. |
| **RestartIfNeeded** | ingen | Med den här flaggan kan distributions skriptet starta om datorn utan att behöva ange om det behövs. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Inför** | ingen | Den här flaggan tvingar avinstallationen om det tidigare försöket att avinstallera misslyckades.
| **RestartIfNeeded** | ingen | Den här flaggan gör att avinstallations skriptet kan starta om datorn utan att begära det om det behövs. |

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att installera IoT Edge korrekt, kan du kolla in [fel söknings](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).
