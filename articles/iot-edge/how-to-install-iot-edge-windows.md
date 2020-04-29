---
title: Installera Azure IoT Edge i Windows | Microsoft Docs
description: Azure IoT Edge installations anvisningar för Windows 10, Windows Server och Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: 61b382f1c286209a12d0be39a81e6817806d3251
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113463"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installera Azure IoT Edge-körningen i Windows

Azure IoT Edge runtime är vad som förvandlar en enhet till en IoT Edge enhet. Körningen kan distribueras på enheter så små som Raspberry Pi eller lika stora som en industriell Server. När en enhet har kon figurer ATS med IoT Edge runtime kan du börja distribuera affärs logiken till den från molnet.

Mer information om IoT Edge runtime finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller anvisningar för att installera Azure IoT Edge runtime på Windows x64-systemet (AMD/Intel) med hjälp av Windows-behållare.

> [!NOTE]
> Ett känt problem med Windows-operativsystemet förhindrar över gången till ström spar läge och vilo läge när IoT Edge moduler (process isolerade Windows Nano Server-behållare) körs. Det här problemet påverkar batteriets livs längd på enheten.
>
> Som en lösning kan du använda kommandot `Stop-Service iotedge` för att stoppa alla IoT Edge moduler som körs innan du använder dessa energi tillstånd.

Användning av Linux-behållare i Windows-system är inte en rekommenderad eller produktions konfiguration som stöds för Azure IoT Edge. Det kan dock användas för utvecklings-och testnings ändamål. Läs mer i [använda IoT Edge i Windows för att köra Linux-behållare](how-to-install-iot-edge-windows-with-linux.md).

För information om vad som ingår i den senaste versionen av IoT Edge, se [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Krav

Använd det här avsnittet för att se om din Windows-enhet har stöd för IoT Edge och förbereda den för en behållar motor före installationen.

### <a name="supported-windows-versions"></a>Windows-versioner som stöds

IoT Edge för Windows kräver Windows version 1809/build 17763, vilket är den senaste versionen av [Windows långtids support](https://docs.microsoft.com/windows/release-information/). För Windows SKU-stöd, se vad som stöds baserat på om du förbereder för produktions scenarier eller utvecklings-och test scenarier:

* **Produktion**: den senaste informationen om vilka operativ system som för närvarande stöds för produktions scenarier finns i [Azure IoT Edge system som stöds](support.md#operating-systems).
* **Utveckling och testning**: för utvecklings-och test scenarier kan Azure IoT Edge med Windows-behållare installeras på alla versioner av Windows 10 eller Windows Server 2019 som har stöd för funktionen containers.

IoT core-enheter måste innehålla den valfria funktionen IoT Core Windows-behållare som stöder IoT Edge Runtime. Använd följande kommando i en [fjärran sluten PowerShell-session](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) för att kontrol lera att Windows-behållare stöds på din enhet:

```powershell
Get-Service vmcompute
```

Om tjänsten finns får du ett lyckat svar med tjänstens status som **körs**. Om `vmcompute` tjänsten inte hittas uppfyller inte din enhet kraven för IoT Edge. Kontakta din maskin varu leverantör för att be om stöd för den här funktionen.

### <a name="prepare-for-a-container-engine"></a>Förbereda för en behållar motor

Azure IoT Edge förlitar sig på en [OCI-kompatibel](https://www.opencontainers.org/) container motor. För produktions scenarier använder du Moby-motorn som ingår i installations skriptet för att köra Windows-behållare på din Windows-enhet.

## <a name="install-iot-edge-on-a-new-device"></a>Installera IoT Edge på en ny enhet

>[!NOTE]
>Azure IoT Edge program varu paket omfattas av licens villkoren som finns i paketen (i licens katalogen). Läs licens villkoren innan du använder paketet. Din installation och användning av paketet utgör ditt godkännande av dessa villkor. Om du inte accepterar licens villkoren ska du inte använda paketet.

Ett PowerShell-skript laddar ned och installerar Azure IoT Edge Security daemon. Security daemon startar sedan den första av två körnings moduler, IoT Edge agent, som möjliggör fjärrdistributioner av andra moduler.

>[!TIP]
>För IoT core-enheter rekommenderar vi att du kör installations kommandona med en RemotePowerShell-session. Mer information finns i [använda PowerShell för Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

När du installerar IoT Edge runtime för första gången på en enhet måste du etablera enheten med en identitet från en IoT-hubb. En enskild IoT Edge enhet kan allokeras manuellt med hjälp av en enhets anslutnings sträng från IoT Hub. Du kan också använda enhets etablerings tjänsten (DPS) för att etablera enheter automatiskt, vilket är användbart när du har många enheter att konfigurera. Välj lämpligt installations skript beroende på ditt etablerings alternativ.

I följande avsnitt beskrivs vanliga användnings fall och parametrar för installations skriptet för IoT Edge på en ny enhet.

### <a name="option-1-install-and-manually-provision"></a>Alternativ 1: installera och etablera manuellt

I det här första alternativet anger du en **enhets anslutnings sträng** som genereras av IoT Hub att etablera enheten.

Det här exemplet visar en manuell installation med Windows-behållare:

1. Registrera en ny IoT Edge enhet och hämta **anslutnings strängen för enheten**om du inte redan gjort det. Kopiera anslutnings strängen och Använd den senare i det här avsnittet. Du kan utföra det här steget med följande verktyg:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio-koden](how-to-register-device.md#register-with-visual-studio-code)

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

   Enhets anslutnings strängen har följande format och ska inte innehålla citat tecken:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Använd stegen i [kontrol lera att installationen har slutförts](#verify-successful-installation) för att kontrol lera status för IoT Edge på enheten.

När du installerar och etablerar en enhet manuellt kan du använda ytterligare parametrar för att ändra installationen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Peka installations programmet till en offline-katalog
* Deklarera en bestämd agent behållar avbildning och ange autentiseringsuppgifter om den finns i ett privat register

Om du vill ha mer information om dessa installations alternativ kan du gå vidare till Lär dig mer om [alla installations parametrar](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Alternativ 2: installera och etablera automatiskt

I det här andra alternativet etablerar du enheten med hjälp av IoT Hub Device Provisioning Service. Ange **omfattnings-ID: t** från en enhets etablerings tjänst instans tillsammans med annan information som är speciell för den önskade [mekanismen för attestering](../iot-dps/concepts-security.md#attestation-mechanism):

* [Skapa och etablera en simulerad IoT Edge enhet med en virtuell TPM i Windows](how-to-auto-provision-simulated-device-windows.md)
* [Skapa och etablera en simulerad IoT Edge enhet med X. 509-certifikat](how-to-auto-provision-x509-certs.md)
* [Skapa och etablera en IoT Edge enhet med hjälp av symmetrisk nyckel attestering](how-to-auto-provision-symmetric-keys.md)

När du installerar och etablerar en enhet automatiskt kan du använda ytterligare parametrar för att ändra installationen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Peka installations programmet till en offline-katalog
* Deklarera en bestämd agent behållar avbildning och ange autentiseringsuppgifter om den finns i ett privat register

Om du vill ha mer information om dessa installations alternativ kan du fortsätta att läsa den här artikeln eller gå vidare till Lär dig om [alla installations parametrar](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Installation av offline eller en speciell version

Under installationen hämtas tre filer:

* Ett PowerShell-skript som innehåller installations anvisningarna
* Microsoft Azure IoT Edge CAB, som innehåller IoT Edge Security daemon (iotedged), Moby container Engine och Moby CLI
* Installations program för Visual C++ Redistributable Package (VC Runtime)

Om enheten ska vara offline under installationen, eller om du vill installera en version av IoT Edge, kan du hämta filerna i förväg till enheten. När det är dags att installera, kan du peka installations skriptet i katalogen som innehåller de hämtade filerna. Installations programmet kontrollerar först katalogen och hämtar bara komponenter som inte hittas. Om alla filer är tillgängliga offline kan du installera utan Internet anslutning.

Du kan också använda parametern offline-installation Sök väg för att uppdatera IoT Edge. Mer information finns i [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).

1. De senaste IoT Edge-installationsfilerna tillsammans med tidigare versioner finns i [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases).

2. Leta upp den version som du vill installera och ladda ned följande filer från avsnittet **till gångar** i viktig information på din IoT-enhet:

   * IoTEdgeSecurityDaemon. ps1
   * Microsoft-Azure-IoTEdge-amd64. cab från släpper 1.0.9 eller senare, eller Microsoft-Azure-IoTEdge. cab från 1.0.8 och äldre versioner.

   Microsoft-Azure-IotEdge-arm32. cab är också bara tillgänglig från och med 1.0.9 i test syfte. IoT Edge stöds för närvarande inte på Windows ARM32-enheter.

   Det är viktigt att använda PowerShell-skriptet från samma version som. cab-filen som du använder eftersom funktionen ändras till att stödja funktionerna i varje version.

3. Om CAB-filen som du laddade ned har ett arkitektur-suffix, byter du namn på filen till bara **Microsoft-Azure-IoTEdge. cab**.

4. Du kan också hämta ett installations program för Visual C++ Redistributable. PowerShell-skriptet använder till exempel den här versionen: [vc_redist. x64. exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Spara installations programmet i samma mapp på din IoT-enhet som IoT Edge-filer.

5. Om du vill installera med offline-komponenter kan du [punkt källa](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) den lokala kopian av PowerShell-skriptet. Använd sedan `-OfflineInstallationPath` parametern som en del av `Deploy-IoTEdge` kommandot och ange den absoluta sökvägen till fil katalogen. Exempel:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Distributions kommandot använder de komponenter som finns i den lokala fil katalogen. Om CAB-filen eller Visual C++-installationsprogrammet saknas försöker den hämta dem.

6. Kör `Initialize-IoTEdge` kommandot för att etablera din enhet med en identitet i IoT Hub. Ange antingen en enhets anslutnings sträng för manuell etablering eller Välj en av de metoder som beskrivs i avsnittet föregående [automatiskt etablering](#option-2-install-and-automatically-provision) .

   Om enheten har startats om efter att `Deploy-IoTEdge`du har kört dot-källan, kör du `Initialize-IoTEdge`PowerShell-skriptet igen innan du kör.

Om du vill ha mer information om installations alternativet offline kan du gå vidare till Lär dig om [alla installations parametrar](#all-installation-parameters).

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Kontrollera status för IoT Edge-tjänsten. Den ska visas som körs.  

```powershell
Get-Service iotedge
```

Undersök tjänst loggar från de senaste 5 minuterna. Om du precis har installerat IoT Edge runtime kan du se en lista över fel från tiden mellan körningen av **Deploy-IoTEdge** och **Initialize-IoTEdge**. De här felen förväntas när tjänsten försöker starta innan den konfigureras.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Kör en automatisk kontroll av de vanligaste konfigurations-och nätverks felen.

```powershell
iotedge check
```

Lista med moduler som körs. När en ny installation har slutförts är den enda modul som du bör se **edgeAgent**. När du har [distribuerat IoT Edge moduler](how-to-deploy-modules-portal.md) för första gången, kommer den andra systemmodulen, **edgeHub**, att starta även på enheten.

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

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge installationen från Windows-enheten använder du följande kommando från ett administrations PowerShell-fönster. Det här kommandot tar bort IoT Edge runtime, tillsammans med den befintliga konfigurationen och Moby-motorns data.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Kommandot Uninstall-IoTEdge fungerar inte på Windows IoT Core. Om du vill ta bort IoT Edge från Windows IoT core-enheter måste du distribuera om Windows IoT Core-avbildningen.

Om du vill ha mer information om avinstallations alternativ använder `Get-Help Uninstall-IoTEdge -full`du kommandot.

## <a name="verify-installation-script"></a>Verifiera installations skript

De installations kommandon som anges i den här artikeln använder cmdleten Invoke-WebRequest för att begära installations `aka.ms/iotedge-win`skriptet från. Den här länken pekar på`IoTEdgeSecurityDaemon.ps1` skriptet från den senaste [IoT Edge versionen](https://github.com/Azure/azure-iotedge/releases). Du kan också hämta skriptet eller en version av skriptet från en speciell version för att köra installations kommandona på din IoT Edge-enhet.

Det tillhandahållna skriptet är signerat för att öka säkerheten. Du kan kontrol lera signaturen genom att ladda ned skriptet till enheten och sedan köra följande PowerShell-kommando:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Status för utdata är **giltig** om signaturen verifieras.

## <a name="all-installation-parameters"></a>Alla installations parametrar

I föregående avsnitt introducerades vanliga installations scenarier med exempel på hur du använder parametrar för att ändra installations skriptet. Det här avsnittet innehåller referens tabeller för de gemensamma parametrarna som används för att installera, uppdatera eller avinstallera IoT Edge.

### <a name="deploy-iotedge"></a>Distribuera – IoTEdge

Kommandot Deploy-IoTEdge hämtar och distribuerar IoT Edge Security daemon och dess beroenden. Distributions kommandot accepterar dessa vanliga parametrar, bland annat. För den fullständiga listan använder du kommandot `Get-Help Deploy-IoTEdge -full`.  

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Container** | **Windows** eller **Linux** | Om inget behållar operativ system anges är Windows standardvärdet.<br><br>För Windows-behållare använder IoT Edge Moby container Engine som ingår i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **Proxy** | Proxy-URL | Ta med den här parametern om enheten måste gå igenom en proxyserver för att komma åt Internet. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Sökväg till katalog | Om den här parametern tas med, kontrollerar installations programmet katalogen för de IoT Edge CAB-och VC runtime-MSI-filer som krävs för installationen. Filer som inte hittas i katalogen laddas ned. Om båda filerna finns i katalogen kan du installera IoT Edge utan Internet anslutning. Du kan också använda den här parametern för att använda en speciell version. |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | ingen | Med den här flaggan kan distributions skriptet starta om datorn utan att behöva ange om det behövs. |

### <a name="initialize-iotedge"></a>Initiera-IoTEdge

Kommandot Initialize-IoTEdge konfigurerar IoT Edge med enhetens anslutnings sträng och drift information. Mycket av den information som genereras av det här kommandot lagras sedan i iotedge\config.yaml-filen. Initierings kommandot accepterar dessa vanliga parametrar, bland annat. För den fullständiga listan använder du kommandot `Get-Help Initialize-IoTEdge -full`.

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Manuell** | Inga | **Växlings parameter**. Om ingen etablerings typ anges, är manuell standardvärdet.<br><br>Deklarerar att du kommer att ange en enhets anslutnings sträng för att etablera enheten manuellt |
| **–** | Inga | **Växlings parameter**. Om ingen etablerings typ anges, är manuell standardvärdet.<br><br>Förklarar att du kommer att tillhandahålla ett omfångs-ID för enhets etablerings tjänsten (DPS) och din enhets registrerings-ID för att etablera genom DPS.  |
| **DeviceConnectionString** | En anslutnings sträng från en IoT Edge enhet som är registrerad i en IoT Hub, med enkla citat tecken | **Krävs** för manuell etablering. Om du inte anger någon anslutnings sträng i skript parametrarna uppmanas du att ange en. |
| **ScopeId** | Ett scope-ID från en instans av enhets etablerings tjänsten som är kopplad till din IoT Hub. | **Krävs** för DPS-etablering. Om du inte anger ett omfångs-ID i skript parametrarna uppmanas du att ange ett. |
| **RegistrationId** | Ett registrerings-ID som genereras av din enhet | **Krävs** för DPS-etablering om du använder TPM eller symmetrisk nyckel attestering. **Valfritt** om du använder 509-certifikat för certifikats attestering i X. |
| **X509IdentityCertificate** | URI-sökvägen till X. 509 enhetens identitets certifikat på enheten. | **Krävs** för DPS-etablering om du använder X. 509 certifikat attestering. |
| **X509IdentityPrivateKey** | URI-sökvägen till den X. 509 enhetens identitets certifikat nyckel på enheten. | **Krävs** för DPS-etablering om du använder X. 509 certifikat attestering. |
| **SymmetricKey** | Den symmetriska nyckel som används för att etablera IoT Edge enhets identitet när DPS används | **Krävs** för DPS-etablering om du använder symmetrisk nyckel attestering. |
| **Container** | **Windows** eller **Linux** | Om inget behållar operativ system anges är Windows standardvärdet.<br><br>För Windows-behållare använder IoT Edge Moby container Engine som ingår i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **AgentImage** | IoT Edge agent avbildnings-URI | Som standard använder en ny IoT Edge-installation den senaste rullande taggen för IoT Edge agent-avbildningen. Använd den här parametern för att ange en speciell tagg för avbildnings versionen eller för att ange en egen agent avbildning. Mer information finns i [förstå IoT Edge Taggar](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Användar** | Användar namn för container registret | Använd bara den här parametern om du anger parametern-AgentImage till en behållare i ett privat register. Ange ett användar namn med åtkomst till registret. |
| **Lösenord** | Säker lösen ords sträng | Använd bara den här parametern om du anger parametern-AgentImage till en behållare i ett privat register. Ange lösen ordet för att få åtkomst till registret. |

### <a name="update-iotedge"></a>Uppdatera – IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Container** | **Windows** eller **Linux** | Om inget behållar-OS anges är Windows standardvärdet. För Windows-behållare kommer en behållar motor att inkluderas i installationen. För Linux-behållare måste du installera en behållar motor innan du startar installationen. |
| **Proxy** | Proxy-URL | Ta med den här parametern om enheten måste gå igenom en proxyserver för att komma åt Internet. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hash-värde för parametrar och värden | Under installationen görs flera webb förfrågningar. Använd det här fältet om du vill ange parametrar för dessa webb förfrågningar. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxyservrar. Mer information finns i [Konfigurera en IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Sökväg till katalog | Om den här parametern tas med, kontrollerar installations programmet katalogen för de IoT Edge CAB-och VC runtime-MSI-filer som krävs för installationen. Filer som inte hittas i katalogen laddas ned. Om båda filerna finns i katalogen kan du installera IoT Edge utan Internet anslutning. Du kan också använda den här parametern för att använda en speciell version. |
| **RestartIfNeeded** | ingen | Med den här flaggan kan distributions skriptet starta om datorn utan att behöva ange om det behövs. |

### <a name="uninstall-iotedge"></a>Avinstallera-IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Inför** | ingen | Den här flaggan tvingar avinstallationen om det tidigare försöket att avinstallera misslyckades.
| **RestartIfNeeded** | ingen | Den här flaggan gör att avinstallations skriptet kan starta om datorn utan att begära det om det behövs. |

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge enhet som har installerats med körnings miljön kan du [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md).

Om du har problem med att installera IoT Edge korrekt, kan du kolla in [fel söknings](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).
