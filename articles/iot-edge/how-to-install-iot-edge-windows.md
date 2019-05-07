---
title: Installera Azure IoT Edge på Windows | Microsoft Docs
description: Installationsinstruktioner för Azure IoT Edge på Windows 10, Windows Server och Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: e48ab075264423479e792848af522a890736a403
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152697"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installera Azure IoT Edge-körningen på Windows

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet. 

Läs mer om IoT Edge-körningen i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln visar hur du installerar Azure IoT Edge-körningen på din Windows x64 (AMD/Intel) system med hjälp av Windows-behållare.

> [!NOTE]
> Ett känt problem i Windows operativsystem förhindrar övergång vila och viloläge energinivåer när IoT Edge-moduler (isolerad processen Windows Nano Server-behållare) körs. Det här problemet påverkar batteritid på enheten.
>
> Som en lösning kan du använda kommandot `Stop-Service iotedge` att stoppa alla IoT Edge-moduler som körs innan du använder dessa energinivåer. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Med hjälp av Linux-behållare på Windows-System är inte en rekommenderad eller stöds produktionskonfigurationen för Azure IoT Edge. Det kan dock användas för utveckling och testning. Mer information finns i [användning IoT Edge på Windows att köra Linux-behållare](how-to-install-iot-edge-windows-with-linux.md).

Information om vad som ingår i den senaste versionen av IoT Edge finns i [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Nödvändiga komponenter

Använd det här avsnittet för att granska om din Windows-enhet kan ha stöd för IoT Edge och förbereda den för en motor för behållaren före installationen. 

### <a name="supported-windows-versions"></a>Windows-versioner som stöds

För utveckling och testning, kan Azure IoT Edge med Windows-behållare installeras på valfri version av Windows 10 eller Windows Server 2019 (build 17763) som har stöd för funktionen behållare. Information om vilka operativsystem som stöds för närvarande för produktionsscenarier finns i [Azure IoT Edge stöds system](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Förbereda för en motor för behållare 

Azure IoT Edge förlitar sig på en [OCI-kompatibla](https://www.opencontainers.org/) container-motorn. I produktionsscenarier använder du Moby-motor som ingår i installationsskriptet till att köra Windows-behållare på din Windows-enhet. 

## <a name="install-iot-edge-on-a-new-device"></a>Installera IoT Edge på en ny enhet

>[!NOTE]
>Azure IoT Edge-programvarupaket omfattas licensvillkoren finns i paket (i katalogen licens). Läs igenom licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

Ett PowerShell-skript hämtar och installerar Azure IoT Edge security daemon. Daemonen security startar sedan först av två moduler för körning, IoT Edge-agenten, vilket gör det möjligt för Fjärrdistribution av andra moduler. 

När du installerar IoT Edge-körningen för första gången på en enhet kan behöva du etablera enheten med en identitet från en IoT-hubb. En enda IoT Edge-enhet kan etableras manuellt med hjälp av en anslutningssträng för enheten som tillhandahålls av IoT Hub. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter du ställer in. Beroende på föredrar etablering, väljer du lämplig installationsskriptet. 

I följande avsnitt beskrivs vanliga användningsområden och parametrar för skriptet för IoT Edge-installation på en ny enhet. 

### <a name="option-1-install-and-manually-provision"></a>Alternativ 1: Installera och etablera manuellt

I den här första alternativet, anger du en **enhetsanslutningssträngen** genereras av IoT Hub för att etablera enheten. 

Det här exemplet visar en manuell installation med Windows-behållare:

1. Om du inte redan gjort registrera en ny IoT Edge-enhet och hämta den **enhetsanslutningssträngen**. Kopiera anslutningssträngen för att använda senare i det här avsnittet. Du kan slutföra det här steget med hjälp av följande verktyg:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Kör PowerShell som administratör.

3. Den **distribuera IoTEdge** kommandot kontrollerar att din Windows-dator på en version som stöds, aktiverar funktionen behållare och laddar sedan ned moby-runtime och IoT Edge-körningen. Kommandot som standard med hjälp av Windows-behållare. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. IoT Core-enheter kan nu startar om automatiskt. Andra Windows 10 eller Windows Server-enheter kan du uppmanas att starta om. I så fall startar du om enheten nu. När enheten är redo, Kör PowerShell som administratör igen.

5. Den **initiera IoTEdge** kommando konfigurerar IoT Edge-körningen på din dator. Kommandot som standard till manuell etableras med Windows-behållare. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. När du uppmanas, anger du enhetens anslutningssträng som du hämtade i steg 1. Enhetens anslutningssträng associerar den fysiska enheten med ett enhets-ID i IoT Hub. 

   Enhetens anslutningssträng tar följande format och får inte innehålla citattecken: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Följ stegen i [verifiera lyckad installation](#verify-successful-installation) att kontrollera status för IoT Edge på din enhet. 

När du installerar och etablera en enhet manuellt kan använda du ytterligare parametrar för att ändra installationen, inklusive:
* Dirigera trafik att gå via en proxyserver
* Peka installationsprogrammet på en offline-katalog
* Deklarera en behållaravbildning för en specifik agent och ange autentiseringsuppgifter om det är i ett privat register

Mer information om dessa installationsalternativ, gå vidare om du vill veta om [alla installationsparametrar](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Alternativ 2: Installera och automatiskt etablera

I den här andra alternativet etablerar du enheten med IoT Hub Device Provisioning-tjänsten. Ange den **Scopeid** från en instans av Device Provisioning-tjänsten och **registrerings-ID** från din enhet.

I följande exempel visar en automatisk installation med Windows-behållare:

1. Följ stegen i [skapa och etablera en simulerad TPM-Edge-enhet på Windows](how-to-auto-provision-simulated-device-windows.md) att konfigurera Device Provisioning-tjänsten och hämta dess **Scopeid**, simulera en TPM-enhet och hämta dess  **Registrerings-ID**, skapa en enskild registrering. När enheten är registrerad i IoT hub, fortsätter du med de här installationsstegen.  

   >[!TIP]
   >Lämna fönstret med TPM-simulatorn öppen under installationen och testning. 

2. Kör PowerShell som administratör.

3. Den **distribuera IoTEdge** kommandot kontrollerar att din Windows-dator på en version som stöds, aktiverar funktionen behållare och laddar sedan ned moby-runtime och IoT Edge-körningen. Kommandot som standard med hjälp av Windows-behållare. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. IoT Core-enheter kan nu startar om automatiskt. Andra Windows 10 eller Windows Server-enheter kan du uppmanas att starta om. I så fall startar du om enheten nu. När enheten är redo, Kör PowerShell som administratör igen.

6. Den **initiera IoTEdge** kommando konfigurerar IoT Edge-körningen på din dator. Kommandot som standard till manuell etableras med Windows-behållare. Använd den `-Dps` flagga för att använda Device Provisioning-tjänsten i stället för manuell etablering.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. När du uppmanas, anger du scope-ID från din Enhetsetableringstjänst och registrerings-ID från din enhet som du bör ha hämtade i steg 1.

8. Följ stegen i [verifiera lyckad installation](#verify-successful-installation) att kontrollera status för IoT Edge på din enhet. 

När du installerar och etablera en enhet manuellt kan använda du ytterligare parametrar för att ändra installationen, inklusive:
* Dirigera trafik att gå via en proxyserver
* Peka installationsprogrammet på en offline-katalog
* Deklarera en behållaravbildning för en specifik agent och ange autentiseringsuppgifter om det är i ett privat register

Fortsätt att läsa den här artikeln för mer information om dessa installationsalternativ, eller hoppa över mer information om [alla installationsparametrar](#all-installation-parameters).

## <a name="offline-installation"></a>Offline-installation

Under installationen hämtas två filer: 
* Microsoft Azure IoT Edge CAB-fil, som innehåller IoT Edge security daemon (iotedged), Moby container-motorn och Moby CLI.
* Visual C++ redistributable package (VC runtime) msi

Du kan hämta en eller båda av de här filerna förväg till enheten och sedan peka installationsskriptet på den katalog som innehåller filerna. Installationsprogrammet kontrollerar katalogen först och sedan hämtas bara de komponenter som inte hittades. Om alla filer som är tillgängliga offline, kan du installera utan internet-anslutning. Du kan också använda den här funktionen för att installera en specifik version av komponenterna.  

De senaste IoT Edge-installationsfilerna tillsammans med tidigare versioner, se [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

Om du vill installera med offline-komponenter på `-OfflineInstallationPath` parametern som en del av distribuera-IoTEdge kommandot och ange den absoluta sökvägen till filen katalogen. Exempel:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Du kan också använda parametern path för offline-installation med kommandot Update IoTEdge introducerades senare i den här artikeln. 

## <a name="verify-successful-installation"></a>Verifiera installationen

Kontrollera status för IoT Edge-tjänsten. Det bör anges som körs.  

```powershell
Get-Service iotedge
```

Granska loggarna för tjänsten från de senaste 5 minuterna. Om du har installera IoT Edge-körningen, kan du se en lista över fel från tiden mellan körs **distribuera IoTEdge** och **initiera IoTEdge**. Dessa fel förväntas som tjänsten försöker starta före håller på att konfigureras. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista med moduler. När du har en ny installation endast modulen bör du se körs är **edgeAgent**. När du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md), ser du andra. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Hantera modulen behållare

IoT Edge-tjänsten kräver en motor för behållare som körs på din enhet. När du distribuerar en modul till en enhet använder IoT Edge-körningen container-motor för att hämta behållaravbildningen från ett register i molnet. IoT Edge-tjänsten kan du interagera med dina moduler och hämtar loggar, men ibland vill du kanske du använder container-motorn för att interagera med själva behållaren. 

Mer information om modulen begrepp finns i [förstå Azure IoT Edge-moduler](iot-edge-modules.md). 

Om du använder Windows-behållare på din Windows IoT Edge-enhet med IoT Edge-installationen Moby container-motorn. Motorn för Moby baserades på samma standarder som Docker, och har utformats för att köras parallellt på samma dator som Docker Desktop. Om du vill mål-behållare som hanteras av motorn Moby av den anledningen måste du specifikt riktade mot vilken motorn i stället för Docker. 

Till exempel om du vill visa alla Docker-avbildningar, använder du följande kommando:

```powershell
docker images
```

Om du vill visa alla Moby avbildningar, ändrar du samma kommando med en pekare till Moby-motorn: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Motorn URI: N visas i utdata från skriptet för installation, eller du kan hitta den i avsnittet behållare runtime inställningar för filen config.yaml. 

![moby_runtime URI: n i config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Mer information om kommandon som du kan använda för att interagera med behållare och avbildningar som körs på din enhet finns i [Docker command-line gränssnitt](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Uppdatera en befintlig installation

Om du har redan installerat IoT Edge-körningen på en enhet innan och etablerat den med en identitet från IoT Hub, kan du uppdatera körningen utan att behöva ange din enhetsinformation igen. 

Mer information finns i [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).

Det här exemplet visar en installation som pekar på en befintlig konfigurationsfil och använder Windows-behållare: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

När du uppdaterar IoT Edge, kan du använda ytterligare parametrar för att ändra uppdateringen, inklusive:
* Dirigera trafik att gå via en proxyserver eller
* Peka installationsprogrammet på en offline-katalog 
* Starta om utan att användaren tillfrågas om det behövs

Du deklarera inte en behållaravbildning för IoT Edge-agenten med Skriptparametrar eftersom denna information har redan angetts i konfigurationsfilen från den tidigare installationen. Om du vill ändra behållaravbildningen agent, kan du göra det i filen config.yaml. 

Mer information om dessa uppdatera alternativ, Använd kommandot `Get-Help Update-IoTEdge -full` eller hänvisa till [alla installationsparametrar](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge-installationen från din Windows-enhet använder du följande kommando från en administrativ PowerShell-fönster. Det här kommandot tar bort IoT Edge-körningen, tillsammans med din befintliga konfiguration och Moby engine-data. 

```powershell
Uninstall-IoTEdge
```

Avinstallera IoTEdge kommando fungerar inte på Windows IoT Core. Om du vill ta bort IoT Edge från Windows IoT Core-enheter, måste du distribuera om din Windows IoT Core-avbildning. 

Mer information om avinstallationsalternativ kommandot `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Alla installationsparametrar

I föregående avsnitt introducerade Vanliga installationsscenarier med exempel på hur du använder parametrar för att ändra installationsskriptet. Det här avsnittet innehåller referenstabeller av de gemensamma parametrarna som används för att installera, uppdatera eller avinstallera IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Distribuera IoTEdge kommandot laddar ned och distribuerar daemonen IoT Edge säkerhet och dess beroenden. Distributionskommandot accepterar dessa vanliga parametrar, bland annat. Använd kommandot för en fullständig lista `Get-Help Deploy-IoTEdge -full`.  

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** eller **Linux** | Om inget operativsystem är behållaren anges är standardvärdet i Windows.<br><br>För Windows-behållare använder IoT Edge moby behållare motor inkluderad i installationen. För Linux-behållare som du behöver installera en motor för behållaren innan du påbörjar installationen. |
| **Proxy** | URL för proxyserver | Använder den här parametern om din enhet måste gå via en proxyserver för att nå internet. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Katalogsökväg | Om den här parametern ingår kontrollerar installationsprogrammet katalogen för IoT Edge CAB-filen och VC Runtime MSI-filerna som krävs för installationen. Alla filer som inte finns i katalogen laddas ned. Om båda filerna finns i katalogen, kan du installera IoT Edge utan Internetanslutning. Du kan också använda den här parametern för att använda en specifik version. |
| **InvokeWebRequestParameters** | Hash-tabell parametrar och värden | Flera webb-begäranden som görs under installationen. Använd det här fältet för att ange parametrar för dessa begäranden. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxy-servrar. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | inga | Den här flaggan kan distributionsskriptet att starta om datorn utan att fråga, om det behövs. |

### <a name="initialize-iotedge"></a>Initiera IoTEdge

Initiera IoTEdge-kommando konfigurerar IoT Edge med enhetens anslutningssträng och information. Mycket av den information som genereras av det här kommandot lagras sedan i filen iotedge\config.yaml. Kommandot initieringen accepterar dessa vanliga parametrar, bland annat. För en fullständig lista, använder du vanliga `Get-Help Initialize-IoTEdge -full`. 

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Manuell** | Ingen | **Växla parametern**. Om ingen etablering typ har angetts, manuell är standardvärdet.<br><br>Deklarerar att tillhandahålla en enhetens anslutningssträng för att etablera enheten manuellt |
| **DPS** | Ingen | **Växla parametern**. Om ingen etablering typ har angetts, manuell är standardvärdet.<br><br>Deklarerar att tillhandahålla en Device Provisioning Service (DPS) för scope-ID och din enhets registrerings-ID för att etablera via DPS.  |
| **DeviceConnectionString** | En anslutningssträng från en IoT Edge-enhet som registrerats i en IoT-hubb, med enkla citattecken | **Krävs** för manuell installation. Om du inte anger en anslutningssträng i skriptparametrarna, uppmanas du under installationen. |
| **ScopeId** | En scope-ID från en instans av Device Provisioning-tjänsten som är associerade med din IoT-hubb. | **Krävs** för DPS-installation. Om du inte anger en scope-ID i skriptparametrarna, uppmanas du under installationen. |
| **RegistrationId** | En registrerings-ID som genereras av din enhet | **Krävs** för DPS-installation. Om du inte anger en registrerings-ID i skriptparametrarna, uppmanas du under installationen. |
| **ContainerOs** | **Windows** eller **Linux** | Om inget operativsystem är behållaren anges är standardvärdet i Windows.<br><br>För Windows-behållare använder IoT Edge moby behållare motor inkluderad i installationen. För Linux-behållare som du behöver installera en motor för behållaren innan du påbörjar installationen. |
| **InvokeWebRequestParameters** | Hash-tabell parametrar och värden | Flera webb-begäranden som görs under installationen. Använd det här fältet för att ange parametrar för dessa begäranden. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxy-servrar. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **AgentImage** | URI för IoT Edge-agenten avbildning | Som standard använder en ny IoT Edge-installation senaste löpande taggen för IoT Edge-agenten avbildningen. Använd den här parametern för att ange en specifik tagg för versionsnumret för avbildningen eller ange en egen bild för agenten. Mer information finns i [förstå IoT Edge-taggar](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Användarnamn** | Container registreringsanvändarnamn | Använd den här parametern endast om du anger parametern - AgentImage till en behållare i ett privat register. Ange ett användarnamn med åtkomst till registret. |
| **Lösenord** | Skydda lösenord | Använd den här parametern endast om du anger parametern - AgentImage till en behållare i ett privat register. Ange lösenord för att komma åt registret. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** eller **Linux** | Om någon behållare som OS anges är standardvärdet i Windows. För Windows-behållare inkluderas en motor för behållaren i installationen. För Linux-behållare som du behöver installera en motor för behållaren innan du påbörjar installationen. |
| **Proxy** | URL för proxyserver | Använder den här parametern om din enhet måste gå via en proxyserver för att nå internet. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hash-tabell parametrar och värden | Flera webb-begäranden som görs under installationen. Använd det här fältet för att ange parametrar för dessa begäranden. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxy-servrar. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Katalogsökväg | Om den här parametern ingår kontrollerar installationsprogrammet katalogen för IoT Edge CAB-filen och VC Runtime MSI-filerna som krävs för installationen. Alla filer som inte finns i katalogen laddas ned. Om båda filerna finns i katalogen, kan du installera IoT Edge utan Internetanslutning. Du kan också använda den här parametern för att använda en specifik version. |
| **RestartIfNeeded** | inga | Den här flaggan kan distributionsskriptet att starta om datorn utan att fråga, om det behövs. |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Force** | inga | Den här flaggan tvingar en avinstallation om tidigare försök att avinstallera lyckades. 
| **RestartIfNeeded** | inga | Den här flaggan kan avinstallationsskriptet att starta om datorn utan att fråga, om det behövs. |


## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att installera IoT Edge korrekt kan du kolla den [felsökning](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).
