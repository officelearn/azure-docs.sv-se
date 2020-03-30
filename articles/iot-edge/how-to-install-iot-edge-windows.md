---
title: Installera Azure IoT Edge i Windows | Microsoft-dokument
description: Installationsinstruktioner för Azure IoT Edge på Windows 10, Windows Server och Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: kgremban
ms.openlocfilehash: 80ce962ac6977fcce2455c8e2ef29af448a44075
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133148"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installera Azure IoT Edge-körningen i Windows

Azure IoT Edge-körningen är det som förvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter så små som en Raspberry Pi eller så stor som en industriell server. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogik till den från molnet.

Mer information om IoT Edge-körningen finns [i Förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

I den här artikeln visas stegen för att installera Azure IoT Edge-körningen på ditt Windows x64 -system (AMD/Intel) med Windows-behållare.

> [!NOTE]
> Ett känt Windows-operativsystemproblem förhindrar övergång till viloläge och viloläge när IoT Edge-moduler (processostaterade Windows Nano Server-behållare) körs. Det här problemet påverkar batteriets livslängd på enheten.
>
> Som en lösning använder du `Stop-Service iotedge` kommandot för att stoppa alla IoT Edge-moduler som körs innan du använder dessa energitillstånd.

Att använda Linux-behållare på Windows-system är inte en rekommenderad eller stöds produktionskonfiguration för Azure IoT Edge. Det kan dock användas för utveckling och testning. Mer information finns i [Använda IoT Edge i Windows för att köra Linux-behållare](how-to-install-iot-edge-windows-with-linux.md).

Information om vad som ingår i den senaste versionen av IoT Edge finns i [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Krav

Använd det här avsnittet om du vill granska om din Windows-enhet kan stödja IoT Edge och förbereda den för en behållarmotor före installationen.

### <a name="supported-windows-versions"></a>Windows-versioner som stöds

IoT Edge för Windows kräver Windows version 1809/build 17763, som är den senaste [Windows långsiktig support build](https://docs.microsoft.com/windows/release-information/). Windows SKU-stöd finns i vad som stöds baserat på om du förbereder dig för produktionsscenarier eller utvecklings- och testscenarier:

* **Produktion**: Mer information om vilka operativsystem som för närvarande stöds för produktionsscenarier finns i [Azure IoT Edge-stödda system](support.md#operating-systems).
* **Utveckling och test:** För utvecklings- och testscenarier kan Azure IoT Edge med Windows-behållare installeras på alla versioner av Windows 10 eller Windows Server 2019 som stöder behållarfunktionen.

IoT Core-enheter måste innehålla valfri funktion för IoT Core Windows Containers för att stödja IoT Edge-körningen. Använd följande kommando i en [fjärr-PowerShell-session](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) för att kontrollera att Windows-behållare stöds på enheten:

```powershell
Get-Service vmcompute
```

Om tjänsten finns bör du få ett lyckat svar med tjänststatusen som anges som **kör**. Om `vmcompute` tjänsten inte hittas uppfyller enheten inte kraven för IoT Edge. Kontakta maskinvaruleverantören för att fråga om support för den här funktionen.

### <a name="prepare-for-a-container-engine"></a>Förbered för en containermotor

Azure IoT Edge är beroende av en [OCI-kompatibel](https://www.opencontainers.org/) behållarmotor. För produktionsscenarier använder du Moby-motorn som ingår i installationsskriptet för att köra Windows-behållare på din Windows-enhet.

## <a name="install-iot-edge-on-a-new-device"></a>Installera IoT Edge på en ny enhet

>[!NOTE]
>Azure IoT Edge-programpaket omfattas av licensvillkoren i paketen (i LICENSE-katalogen). Läs licensvillkoren innan du använder paketet. Din installation och användning av paketet utgör ditt godkännande av dessa villkor. Om du inte godkänner licensvillkoren ska du inte använda paketet.

Ett PowerShell-skript hämtar och installerar säkerhetsdemonen Azure IoT Edge. Säkerhetsdemonen startar sedan den första av två runtime-moduler, IoT Edge-agenten, som möjliggör fjärrdistribution av andra moduler.

>[!TIP]
>För IoT Core-enheter rekommenderar vi att du kör installationskommandona med hjälp av en RemotePowerShell-session. Mer information finns i [Använda PowerShell för Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

När du installerar IoT Edge-körningen för första gången på en enhet måste du etablera enheten med en identitet från en IoT-hubb. En enda IoT Edge-enhet kan etableras manuellt med hjälp av en enhetsanslutningssträng som tillhandahålls av IoT Hub. Du kan också använda DPS (Device Provisioning Service) för att automatiskt etablera enheter, vilket är användbart när du har många enheter att konfigurera. Välj lämpligt installationsskript beroende på vilket etableringsval du vill.

I följande avsnitt beskrivs de vanliga användningsfallen och parametrarna för IoT Edge-installationsskriptet på en ny enhet.

### <a name="option-1-install-and-manually-provision"></a>Alternativ 1: Installera och manuellt etablera

I det här första alternativet anger du en **enhetsanslutningssträng** som genereras av IoT Hub för att etablera enheten.

Det här exemplet visar en manuell installation med Windows-behållare:

1. Om du inte redan har gjort det registrerar du en ny IoT Edge-enhet och hämtar **enhetsanslutningssträngen**. Kopiera anslutningssträngen som ska användas senare i det här avsnittet. Du kan slutföra det här steget med hjälp av följande verktyg:

   * [Azure-portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio-kod](how-to-register-device.md#register-with-visual-studio-code)

2. Kör PowerShell som administratör.

   >[!NOTE]
   >Använd en AMD64-session med PowerShell för att installera IoT Edge, inte PowerShell (x86). Om du är osäker på vilken sessionstyp du använder kör du följande kommando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Kommandot **Deploy-IoTEdge** kontrollerar att din Windows-dator finns i en version som stöds, aktiverar behållarfunktionen och hämtar sedan den moby-körningen och IoT Edge-körningen. Kommandot använder som standard Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Nu kan IoT Core-enheter startas om automatiskt. Andra Windows 10- eller Windows Server-enheter kan uppmana dig att starta om. Starta om enheten nu om så är fallet. När enheten är klar kör du PowerShell som administratör igen.

5. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge-körningen på datorn. Kommandot är som standard manuell etablering med Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Ange den enhetsanslutningssträng som du hämtade i steg 1 när du uppmanas att göra det. Enhetsanslutningssträngen associerar den fysiska enheten med ett enhets-ID i IoT Hub.

   Enhetsanslutningssträngen har följande format och bör inte innehålla citattecken:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Följ stegen i [Verifiera lyckad installation](#verify-successful-installation) för att kontrollera status för IoT Edge på enheten.

När du installerar och etablerar en enhet manuellt kan du använda ytterligare parametrar för att ändra installationen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Peka installationsprogrammet på en offlinekatalog
* Deklarera en specifik agentbehållareavbildning och ange autentiseringsuppgifter om den finns i ett privat register

Om du vill ha mer information om de här installationsalternativen kan du läsa om [alla installationsparametrar](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Alternativ 2: Installera och automatiskt etablera

I det här andra alternativet etablerar du enheten med hjälp av IoT Hub Device Provisioning Service. Ange **scope-ID** från en enhetsetableringstjänstinstans tillsammans med all annan information som är specifik för den önskade [attestationmekanismen:](../iot-dps/concepts-security.md#attestation-mechanism)

* [Skapa och etablera en simulerad IoT Edge-enhet med en virtuell TPM i Windows](how-to-auto-provision-simulated-device-windows.md)
* [Skapa och etablera en simulerad IoT Edge-enhet med X.509-certifikat](how-to-auto-provision-x509-certs.md)
* [Skapa och etablera en IoT Edge-enhet med symmetrisk nyckeltysstik](how-to-auto-provision-symmetric-keys.md)

När du installerar och etablerar en enhet automatiskt kan du använda ytterligare parametrar för att ändra installationen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Peka installationsprogrammet på en offlinekatalog
* Deklarera en specifik agentbehållareavbildning och ange autentiseringsuppgifter om den finns i ett privat register

Om du vill ha mer information om de här installationsalternativen kan du fortsätta läsa den här artikeln eller hoppa över om du vill veta mer om [alla installationsparametrar](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Offline- eller specifik versionsinstallation

Under installationen hämtas två filer:

* Microsoft Azure IoT Edge-hytten, som innehåller säkerhetsdemonen IoT Edge (iotedged), Moby-behållarmotorn och Moby CLI.
* Visuellt C++-redistreastribuerat paket (VC-körning) MSI

Om enheten kommer att vara offline under installationen, eller om du vill installera en viss version av IoT Edge, kan du ladda ned en eller båda av dessa filer i förväg till enheten. När det är dags att installera pekar du installationsskriptet på katalogen som innehåller de nedladdade filerna. Installationsprogrammet kontrollerar katalogen först och hämtar sedan bara komponenter som inte hittas. Om alla filer är tillgängliga offline kan du installera utan internetanslutning.

De senaste installationsfilerna för IoT Edge tillsammans med tidigare versioner finns i [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

Om du vill installera `-OfflineInstallationPath` med offlinekomponenter använder du parametern som en del av kommandot Deploy-IoTEdge och anger den absoluta sökvägen till filkatalogen. Exempel:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>Parametern `-OfflineInstallationPath` söker efter en fil med namnet **Microsoft-Azure-IoTEdge.cab** i katalogen som tillhandahålls. Från och med IoT Edge version 1.0.9-rc4 finns det två .cab-filer tillgängliga att använda, en för AMD64-enheter och en för ARM32. Hämta rätt fil för enheten och byt sedan namn på filen för att ta bort arkitektursuffixet.

Kommandot `Deploy-IoTEdge` installerar IoT Edge-komponenterna och sedan måste `Initialize-IoTEdge` du fortsätta till kommandot för att etablera enheten med dess IoT Hub-enhets-ID och anslutning. Kör kommandot direkt och tillhandahåller en anslutningssträng från IoT Hub, eller använd någon av länkarna i föregående avsnitt för att lära dig hur du automatiskt etablerar enheter med enhetsetableringstjänsten.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

Du kan också använda parametern offlineinstallationssökväg med kommandot Update-IoTEdge.

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Kontrollera status för IoT Edge-tjänsten. Det bör listas som igång.  

```powershell
Get-Service iotedge
```

Undersök serviceloggar från de senaste 5 minuterna. Om du precis har installerat IoT Edge-körningen kan du se en lista över fel från tiden mellan att köra **Deploy-IoTEdge** och **Initialize-IoTEdge**. Dessa fel förväntas, eftersom tjänsten försöker starta innan den konfigureras.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Kör en automatisk kontroll efter de vanligaste konfigurations- och nätverksfelen.

```powershell
iotedge check
```

Lista löpmoduler. Efter en ny installation är den enda modulen du bör se igång **edgeAgent**. När du [har distribuerat IoT Edge-moduler](how-to-deploy-modules-portal.md) för första gången startar även den andra systemmodulen **EdgeHub**på enheten.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Hantera modulbehållare

IoT Edge-tjänsten kräver en behållarmotor som körs på enheten. När du distribuerar en modul till en enhet använder IoT Edge-körningen behållarmotorn för att hämta behållaravbildningen från ett register i molnet. IoT Edge-tjänsten gör att du kan interagera med dina moduler och hämta loggar, men ibland kanske du vill använda behållarmotorn för att interagera med själva behållaren.

Mer information om modulbegrepp finns i [Förstå Azure IoT Edge-moduler](iot-edge-modules.md).

Om du kör Windows-behållare på din Windows IoT Edge-enhet inkluderade IoT Edge-installationen Moby-behållarmotorn. Moby-motorn baserades på samma standarder som Docker, och utformades för att köras parallellt på samma maskin som Docker Desktop. Av den anledningen, om du vill rikta behållare som hanteras av Moby-motorn, måste du specifikt rikta den motorn istället för Docker.

Om du till exempel vill visa alla Docker-bilder använder du följande kommando:

```powershell
docker images
```

Om du vill visa alla Moby-bilder ändrar du samma kommando med en pekare till Moby-motorn:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Motor-URI visas i utdata från installationsskriptet, eller så hittar du den i avsnittet inställningar för behållarkörning för filen config.yaml.

![moby_runtime uri i config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Mer information om kommandon som du kan använda för att interagera med behållare och bilder som körs på enheten finns i [Dockers kommandoradsgränssnitt](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge-installationen från Windows-enheten använder du följande kommando från ett administrativt PowerShell-fönster. Det här kommandot tar bort IoT Edge-körningen, tillsammans med din befintliga konfiguration och Moby-motordata.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Kommandot Avinstallera-IoTEdge fungerar inte i Windows IoT Core. Om du vill ta bort IoT Edge från Windows IoT Core-enheter måste du distribuera om windows IoT Core-avbildningen.

Om du vill ha mer information `Get-Help Uninstall-IoTEdge -full`om avinstallationsalternativ använder du kommandot .

## <a name="verify-installation-script"></a>Verifiera installationsskript

Installationskommandona i den här artikeln använder cmdleten Invoke-WebRequest `aka.ms/iotedge-win`för att begära installationsskriptet från . Den här länken`IoTEdgeSecurityDaemon.ps1` pekar på skriptet från den senaste [IoT Edge-versionen](https://github.com/Azure/azure-iotedge/releases). Du kan också hämta skriptet, eller en version av skriptet från en viss version, för att köra installationskommandona på IoT Edge-enheten.

Det angivna skriptet är signerat för att öka säkerheten. Du kan verifiera signaturen genom att hämta skriptet till enheten och sedan köra följande PowerShell-kommando:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Utdatastatusen är **Giltig** om signaturen verifieras.

## <a name="all-installation-parameters"></a>Alla installationsparametrar

I föregående avsnitt introducerades vanliga installationsscenarier med exempel på hur du använder parametrar för att ändra installationsskriptet. Det här avsnittet innehåller referenstabeller över de vanliga parametrar som används för att installera, uppdatera eller avinstallera IoT Edge.

### <a name="deploy-iotedge"></a>Distribuera-IoTEdge

Kommandot Deploy-IoTEdge hämtar och distribuerar IoT Edge Security Daemon och dess beroenden. Distributionskommandot accepterar bland annat dessa vanliga parametrar. Använd kommandot `Get-Help Deploy-IoTEdge -full`.  

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** eller **Linux** | Om inget behållaroperativsystem har angetts är Windows standardvärdet.<br><br>För Windows-behållare använder IoT Edge den moby-behållarmotor som ingår i installationen. För Linux-behållare måste du installera en behållarmotor innan du startar installationen. |
| **Proxy** | Proxy URL | Inkludera den här parametern om enheten behöver gå igenom en proxyserver för att nå internet. Mer information finns i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Sökväg till katalog | Om den här parametern ingår kommer installationsprogrammet att kontrollera katalogen i listan för IoT Edge-hytten och VC Runtime MSI-filer som krävs för installation. Alla filer som inte finns i katalogen hämtas. Om båda filerna finns i katalogen kan du installera IoT Edge utan internetanslutning. Du kan också använda den här parametern för att använda en viss version. |
| **InvokeWebRequestParameters** | Hashtable av parametrar och värden | Under installationen görs flera webbförfrågningar. Använd det här fältet om du vill ange parametrar för dessa webbbegäranden. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | ingen | Med den här flaggan kan distributionsskriptet starta om datorn utan att fråga om det behövs. |

### <a name="initialize-iotedge"></a>Initiera-IoTEdge

Kommandot Initialize-IoTEdge konfigurerar IoT Edge med enhetens anslutningssträng och driftinformation. Mycket av den information som genereras av det här kommandot lagras sedan i filen iotedge\config.yaml. Initieringskommandot accepterar bland annat dessa vanliga parametrar. Använd kommandot `Get-Help Initialize-IoTEdge -full`.

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Manuell** | Inget | **Växla parameter**. Om ingen etableringstyp har angetts är manuell standardvärdet.<br><br>Deklarerar att du tillhandahåller en enhetsanslutningssträng för att etablera enheten manuellt |
| **Dps** | Inget | **Växla parameter**. Om ingen etableringstyp har angetts är manuell standardvärdet.<br><br>Försäkrar att du kommer att tillhandahålla ett DPS-scope-ID (Device Provisioning Service) och enhetens registrerings-ID för att etablera via DPS.  |
| **DeviceConnectionString** | En anslutningssträng från en IoT Edge-enhet som registrerats i en IoT Hub, med enstaka citattecken | **Krävs** för manuell etablering. Om du inte anger någon anslutningssträng i skriptparametrarna uppmanas du att ange en. |
| **Scopeid** | Ett scope-ID från en instans av enhetsetableringstjänsten som är associerad med din IoT Hub. | **Krävs** för DPS-etablering. Om du inte anger ett scope-ID i skriptparametrarna uppmanas du att ange ett. |
| **RegistrationId (RegistreringId)** | Ett registrerings-ID som genereras av din enhet | **Krävs** för DPS-etablering om du använder TPM eller symmetrisk nyckelintyg. **Valfritt** om du använder X.509-certifikatintyg. |
| **X509IdentityCertificate** | URI-sökvägen till X.509-enhetsidentitetscertifikatet på enheten. | **Krävs** för DPS-etablering om du använder X.509-certifikatintyg. |
| **X509IdentityPrivateKey** | URI-sökvägen till X.509-enhetsidentitetscertifikatnyckeln på enheten. | **Krävs** för DPS-etablering om du använder X.509-certifikatintyg. |
| **Symmetrisk nyckel** | Den symmetriska nyckel som används för att etablera IoT Edge-enhetens identitet när DPS används | **Krävs** för DPS-etablering om du använder symmetrisk nyckeltysstation. |
| **ContainerOs** | **Windows** eller **Linux** | Om inget behållaroperativsystem har angetts är Windows standardvärdet.<br><br>För Windows-behållare använder IoT Edge den moby-behållarmotor som ingår i installationen. För Linux-behållare måste du installera en behållarmotor innan du startar installationen. |
| **InvokeWebRequestParameters** | Hashtable av parametrar och värden | Under installationen görs flera webbförfrågningar. Använd det här fältet om du vill ange parametrar för dessa webbbegäranden. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **AgentImage (agentBild)** | IoT Edge-agentbild URI | Som standard använder en ny IoT Edge-installation den senaste rullande taggen för IoT Edge-agentavbildningen. Använd den här parametern om du vill ange en specifik tagg för avbildningsversionen eller för att tillhandahålla en egen agentavbildning. Mer information finns i [Förstå IoT Edge-taggar](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Användarnamn** | Användarnamn för behållarregistret | Använd den här parametern endast om du ställer in parametern -AgentImage till en behållare i ett privat register. Ge ett användarnamn åtkomst till registret. |
| **Lösenord** | Sträng för säkert lösenord | Använd den här parametern endast om du ställer in parametern -AgentImage till en behållare i ett privat register. Ange lösenordet för att komma åt registret. |

### <a name="update-iotedge"></a>Uppdatering-IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** eller **Linux** | Om inget behållaropererat operativsystem anges är Windows standardvärdet. För Windows-behållare inkluderas en behållarmotor i installationen. För Linux-behållare måste du installera en behållarmotor innan du startar installationen. |
| **Proxy** | Proxy URL | Inkludera den här parametern om enheten behöver gå igenom en proxyserver för att nå internet. Mer information finns i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hashtable av parametrar och värden | Under installationen görs flera webbförfrågningar. Använd det här fältet om du vill ange parametrar för dessa webbbegäranden. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Sökväg till katalog | Om den här parametern ingår kommer installationsprogrammet att kontrollera katalogen i listan för IoT Edge-hytten och VC Runtime MSI-filer som krävs för installation. Alla filer som inte finns i katalogen hämtas. Om båda filerna finns i katalogen kan du installera IoT Edge utan internetanslutning. Du kan också använda den här parametern för att använda en viss version. |
| **RestartIfNeeded** | ingen | Med den här flaggan kan distributionsskriptet starta om datorn utan att fråga om det behövs. |

### <a name="uninstall-iotedge"></a>Avinstallera-IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Kraft** | ingen | Den här flaggan tvingar avinstallationen om det tidigare försöket att avinstallera misslyckades.
| **RestartIfNeeded** | ingen | Med den här flaggan kan avinstallationsskriptet starta om datorn utan att fråga om det behövs. |

## <a name="next-steps"></a>Nästa steg

Nu när du har etablerat en IoT Edge-enhet med körningen installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att installera IoT Edge på rätt sätt kan du läsa [felsökningssidan.](troubleshoot.md)

Information om hur du uppdaterar en befintlig installation till den senaste versionen av IoT Edge finns i [Uppdatera säkerhetsdemonen och körningen i IoT Edge](how-to-update-iot-edge.md).
