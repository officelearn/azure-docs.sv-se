---
title: Installera Azure IoT Edge på Windows | Microsoft Docs
description: Installationsinstruktioner för Azure IoT Edge på Windows 10, Windows Server och Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 27478de68cde9a097dcc160a4553839aef9a018c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902813"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installera Azure IoT Edge-körningen på Windows

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet. 

Läs mer om IoT Edge-körningen i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln visar hur du installerar Azure IoT Edge-körningen på din Windows x64 (AMD/Intel) system. Windows support förhandsvisas just nu.

>[!NOTE]
Med hjälp av Linux-behållare på Windows-System är inte en rekommenderad eller stöds produktionskonfigurationen för Azure IoT Edge. Det kan dock användas för utveckling och testning.

## <a name="prerequisites"></a>Förutsättningar

Använd det här avsnittet för att granska om din Windows-enhet kan ha stöd för IoT Edge och förbereda den för en motor för behållaren före installationen. 

### <a name="supported-windows-versions"></a>Windows-versioner som stöds

Azure IoT Edge stöder olika versioner av Windows, beroende på om du kör Windows-behållare eller Linux-behållare. 

Den senaste versionen av Azure IoT Edge med Windows-behållare kan köras på följande versioner av Windows:
* Windows 10 eller IoT Core med uppdateringen från oktober 2018 (version 17763)
* Windows Server 2019 (build 17763)

Den senaste versionen av Azure IoT Edge med Linux-behållare kan köras på följande versioner av Windows: 
* Windows 10 Anniversary update (version 14393) eller senare
* Windows Server 2016 eller senare

Mer information om vilka operativsystem stöds för närvarande finns i [support för Azure IoT Edge](support.md#operating-systems). 

Läs mer om vad som ingår i den senaste versionen av IoT Edge, [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

### <a name="prepare-for-a-container-engine"></a>Förbereda för en motor för behållare 

Azure IoT Edge förlitar sig på en [OCI-kompatibla](https://www.opencontainers.org/) container-motorn. I produktionsscenarier använder du Moby-motor som ingår i installationsskriptet till att köra Windows-behållare på din Windows-enhet. Du kan köra Linux-behållare på din Windows-enhet för utveckling och testning, men du måste installera och konfigurera en motor för behållaren innan du installerar IoT Edge. Båda scenarierna finns i följande avsnitt för förutsättningar för att förbereda din enhet. 

#### <a name="moby-engine-for-windows-containers"></a>Moby motor för Windows-behållare

För Windows-enheter som kör IoT Edge i produktionsscenarier är Moby endast stöds officiellt container-motorn. Installationsskriptet installerar automatiskt Moby-motorn på din enhet innan du installerar IoT Edge. Förbered din enhet genom att aktivera funktionen behållare. 

1. Sök efter i startfältet **aktivera Windows-funktioner på eller av** och öppna programmet på Kontrollpanelen.
2. Hitta och välja **behållare**.
3. Välj **OK**. 

#### <a name="docker-for-linux-containers"></a>Docker för Linux-behållare

Om du använder Windows för att utveckla och testa behållare för Linux enheter, kan du använda [Docker för Windows](https://www.docker.com/docker-windows) som din behållare. Docker kan konfigureras att [använder Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). Du måste installera Docker och konfigurera den innan du installerar IoT Edge. Linux-behållare stöds inte på Windows-enheter i produktionen. 

Om din IoT Edge-enhet är en Windows-dator, kontrollerar du att den uppfyller de [systemkrav](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) för Hyper-V. Om det är en virtuell dator aktiverar du [kapslad virtualisering](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) och allokerar minst 2 GB minne.

## <a name="install-iot-edge-on-a-new-device"></a>Installera IoT Edge på en ny enhet

>[!NOTE]
>Azure IoT Edge-programvarupaket omfattas licensvillkoren finns i paket (i katalogen licens). Läs igenom licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

Ett PowerShell-skript hämtar och installerar Azure IoT Edge security daemon. Daemonen security startar sedan först av två moduler för körning, IoT Edge-agenten, vilket gör det möjligt för Fjärrdistribution av andra moduler. 

När du installerar IoT Edge-körningen för första gången på en enhet kan behöva du etablera enheten med en identitet från en IoT-hubb. En enda IoT Edge-enhet kan etableras manuellt med hjälp av en sträng för anslutningar av enhet som tillhandahålls av IoT Hub. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter du ställer in. Beroende på föredrar etablering, väljer du lämplig installationsskriptet. 

I följande avsnitt beskrivs vanliga användningsområden och parametrar för skriptet för IoT Edge-installation på en ny enhet. 

### <a name="option-1-install-and-manually-provision"></a>Alternativ 1: Installera och etablera manuellt

I den här första alternativet anger du en anslutningssträng för enheten som genererats av IoT Hub för att etablera enheten. 

Följ stegen i [registrera en ny Azure IoT Edge-enhet](how-to-register-device-portal.md) att registrera din enhet och hämta enhetens anslutningssträng. 

Det här exemplet visar en manuell installation med Windows-behållare:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

När du installerar och etablera en enhet manuellt kan använda du ytterligare parametrar för att ändra installationen, inklusive:
* Dirigera trafik att gå via en proxyserver
* Peka installationsprogrammet på en offline-katalog
* Deklarera en behållaravbildning för en specifik agent och ange autentiseringsuppgifter om det är i ett privat register
* Hoppa över Moby CLI-installationen

Fortsätt att läsa den här artikeln för mer information om dessa installationsalternativ, eller hoppa över mer information om [alla installationsparametrar](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Alternativ 2: Installera och automatiskt etablera

I den här andra alternativet etablerar du enheten med IoT Hub Device Provisioning-tjänsten. Ange den **Scopeid** från en instans av Device Provisioning-tjänsten och **registrerings-ID** från din enhet.

Följ stegen i [skapa och etablera en simulerad TPM-Edge-enhet på Windows](how-to-auto-provision-simulated-device-windows.md) att konfigurera Device Provisioning-tjänsten och hämta dess **Scopeid**, simulera en TPM-enhet och hämta dess  **Registrerings-ID**, skapa en enskild registrering. När enheten är registrerad i IoT Hub, fortsätter du med installationen.  

   >[!TIP]
   >Lämna fönstret med TPM-simulatorn öppen under installationen och testning. 

I följande exempel visas en automatisk installation med Windows-behållare:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

När du installerar och etablera en enhet manuellt kan använda du ytterligare parametrar för att ändra installationen, inklusive:
* Dirigera trafik att gå via en proxyserver
* Peka installationsprogrammet på en offline-katalog
* Deklarera en behållaravbildning för en specifik agent och ange autentiseringsuppgifter om det är i ett privat register
* Hoppa över Moby CLI-installationen

Fortsätt att läsa den här artikeln för mer information om dessa installationsalternativ, eller hoppa över mer information om [alla installationsparametrar](#all-installation-parameters).

## <a name="update-an-existing-installation"></a>Uppdatera en befintlig installation

Om du har redan installerat IoT Edge-körningen på en enhet innan och etablerat den med en identitet från IoT Hub, kan du använda en förenklad installationsskriptet. Flaggan `-ExistingConfig` deklarerar att det redan finns en IoT Edge-konfigurationsfilen på enheten. Konfigurationsfilen innehåller information om identitets-och certifikat- och enhetsinställningar. Du kan använda det här installationsalternativet om din enhet etablerades ursprungligen manuellt eller automatiskt. 

Mer information finns i [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).

Det här exemplet visar en installation som pekar på en befintlig konfigurationsfil och använder Windows-behållare: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

När du installerar IoT Edge från en befintlig konfigurationsfil, du kan använda ytterligare parametrar för att ändra installationen, inklusive:
* Dirigera trafik att gå via en proxyserver
* Peka installationsprogrammet på en offline-katalog, eller 
* Hoppa över Moby CLI-installationen. 

Du deklarera inte en behållaravbildning för IoT Edge-agenten med Skriptparametrar, eftersom denna information har redan angetts i konfigurationsfilen från den tidigare installationen. Om du vill ändra behållaravbildningen agent, kan du göra det i filen config.yaml. 

Fortsätt att läsa den här artikeln för mer information om dessa installationsalternativ, eller hoppa över mer information om [alla installationsparametrar](#all-installation-parameters).

## <a name="offline-installation"></a>Offline-installation

Fyra filerna laddas ned under installationen: 
* IoT Edge security daemon (iotedgd) zip 
* Moby motorn zip
* Moby CLI zip
* Visual C++ redistributable package (VC runtime) msi

Du kan hämta en eller alla av de här filerna förväg till enheten och sedan peka installationsskriptet på den katalog som innehåller filerna. Installationsprogrammet kontrollerar katalogen först och sedan hämtas bara de komponenter som inte hittades. Om alla fyra filerna är tillgängliga offline, kan du installera utan internet-anslutning. Du kan också använda den här funktionen för att åsidosätta online-versioner av en eller flera komponenter.  

Senaste installationsfilerna tillsammans med tidigare versioner, se [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases)

Om du vill installera med offline-komponenter på `-OfflineInstallationPath` parametern och ange den absoluta sökvägen till filen katalogen. Exempel:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>Alla installationsparametrar

I föregående avsnitt introducerade Vanliga installationsscenarier med exempel på hur du använder parametrar för att ändra installationsskriptet. Det här avsnittet innehåller en tabell över tillåtna parametrar som du kan använda för att installera IoT Edge. Mer information, kör `get-help Install-SecurityDaemon -full` i ett PowerShell-fönster. 

Din installationskommandot kan använda dessa gemensamma parametrar för att installera IoT Edge med en befintlig konfiguration: 

| Parameter | Godkända värden | Kommentarer |
| --------- | --------------- | -------- |
| **Manuell** | Ingen | **Växla parametern**. Varje installation måste deklareras antingen manuell, DPS, eller existingconfig.<br><br>Deklarerar att tillhandahålla en enhetens anslutningssträng för att etablera enheten manuellt |
| **DPS** | Ingen | **Växla parametern**. Varje installation måste deklareras antingen manuell, DPS, eller existingconfig.<br><br>Deklarerar att tillhandahålla en Device Provisioning Service (DPS) för scope-ID och din enhets registrerings-ID för att etablera via DPS.  |
| **ExistingConfig** | Ingen | **Växla parametern**. Varje installation måste deklareras antingen manuell, DPS, eller existingconfig.<br><br>Deklarerar att config.yaml finns redan en fil på enheten med dess Etableringsinformation. |
| **DeviceConnectionString** | En anslutningssträng från en IoT Edge-enhet som registrerats i en IoT-hubb, med enkla citattecken | **Krävs** för manuell installation. Om du inte anger en anslutningssträng i skriptparametrarna, uppmanas du under installationen. |
| **ScopeId** | En scope-ID från en instans av Device Provisioning-tjänsten som är associerade med din IoT-hubb. | **Krävs** för DPS-installation. Om du inte anger en scope-ID i skriptparametrarna, uppmanas du under installationen. |
| **RegistrationId** | En registrerings-ID som genereras av din enhet | **Krävs** för DPS-installation. Om du inte anger en registrerings-ID i skriptparametrarna, uppmanas du under installationen. |
| **ContainerOs** | **Windows** eller **Linux** | Om någon behållare som OS anges är standardvärdet i Linux. För Windows-behållare inkluderas en motor för behållaren i installationen. För Linux-behållare som du behöver installera en motor för behållaren innan du påbörjar installationen. Linux-behållare som körs på Windows är ett användbart utvecklingsscenario, men stöds inte i produktion. |
| **Proxy** | URL för proxyserver | Använder den här parametern om din enhet måste gå via en proxyserver för att nå internet. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Hash-tabell parametrar och värden | Flera webb-begäranden som görs under installationen. Använd det här fältet för att ange parametrar för dessa begäranden. Den här parametern är användbar för att konfigurera autentiseringsuppgifter för proxy-servrar. Mer information finns i [konfigurera en IoT Edge-enhet kan kommunicera via en proxyserver](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Katalogsökväg | Om den här parametern ingår kontrollerar installationsprogrammet katalogen för iotedged zip, Moby motorn zip, Moby CLI zip och VC Runtime MSI-filerna som krävs för installationen. Om alla fyra filerna finns i katalogen, kan du installera IoT Edge när offline. Du kan också använda den här parametern för att åsidosätta onlineversionen av en viss komponent. |
| **AgentImage** | URI för IoT Edge-agenten avbildning | Som standard använder en ny IoT Edge-installation senaste löpande taggen för IoT Edge-agenten avbildningen. Använd den här parametern för att ange en specifik tagg för versionsnumret för avbildningen eller ange en egen bild för agenten. Mer information finns i [förstå IoT Edge-taggar](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Användarnamn** | Container registreringsanvändarnamn | Använd den här parametern endast om du anger parametern - AgentImage till en behållare i ett privat register. Ange ett användarnamn med åtkomst till registret. |
| **Lösenord** | Skydda lösenord | Använd den här parametern endast om du anger parametern - AgentImage till en behållare i ett privat register. Ange lösenord för att komma åt registret. | 
| **SkipMobyCli** | Ingen | Gäller endast om - ContainerOS är inställt på Windows. Installera inte Moby CLI (docker.exe) till $MobyInstallDirectory. |

## <a name="verify-successful-installation"></a>Verifiera installationen

Du kan kontrollera status för IoT Edge-tjänsten genom att: 

```powershell
Get-Service iotedge
```

Undersök tjänstloggar från de senaste 5 minuterna med:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Och listan körs moduler med:

```powershell
iotedge list
```

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge-installationen från din Windows-enhet använder du följande kommando från en administrativ PowerShell-fönster. Det här kommandot tar bort IoT Edge-körningen, tillsammans med din befintliga konfiguration och Moby engine-data. 

```PowerShell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

Om du planerar att installera om IoT Edge på din enhet, hoppar över den `-DeleteConfig` och `-DeleteMobyDataRoot` parametrarna så att du kan installera om daemonen security senare med befintlig konfigurationsinformation. 

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att installera IoT Edge korrekt kan du kolla den [felsökning](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).
