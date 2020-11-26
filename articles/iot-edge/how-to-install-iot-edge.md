---
title: Installera Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge Installationsinstruktioner på Windows-eller Linux-enheter
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: a7794bcdfa4f82698fdc5875bc94dcf52b70166e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185104"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Installera eller avinstallera Azure IoT Edge runtime

Azure IoT Edge runtime är vad som förvandlar en enhet till en IoT Edge enhet. Körningen kan distribueras på enheter så små som Raspberry Pi eller lika stora som en industriell Server. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogiken till den från molnet. Mer information finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Det finns två steg för att konfigurera en IoT Edge enhet. Det första steget är att installera körningen och dess beroenden som omfattas av den här artikeln. Det andra steget är att ansluta enheten till sin identitet i molnet och konfigurera autentisering med IoT Hub. De här stegen finns i nästa artiklar.

Den här artikeln beskriver stegen för att installera Azure IoT Edge runtime på Linux-eller Windows-enheter. För Windows-enheter har du ytterligare möjlighet att använda Linux-behållare eller Windows-behållare. För närvarande rekommenderas Windows-behållare i Windows för produktions scenarier. Linux-behållare i Windows är användbara för utvecklings-och testnings scenarier, särskilt om du utvecklar på en Windows-dator för att distribuera till Linux-enheter.

## <a name="prerequisites"></a>Förutsättningar

Den senaste informationen om vilka operativ system som för närvarande stöds för produktions scenarier finns i [Azure IoT Edge system som stöds](support.md#operating-systems)

# <a name="linux"></a>[Linux](#tab/linux)

Ha en x64-, ARM32-eller ARM64 Linux-enhet. Microsoft tillhandahåller installations paket för Ubuntu Server 16,04, Ubuntu Server 18,04 och Raspbian utsträckt operativ system.

>[!NOTE]
>Stöd för ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Förbered din enhet för att få åtkomst till Microsoft-installations paketen.

1. Installera den lagrings plats konfiguration som matchar enhetens operativ system.

   * **Ubuntu Server 16,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS-storlek**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Kopiera den genererade listan till katalogen sources. list. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Installera den offentliga nyckeln för Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Windows-version

IoT Edge med Windows-behållare kräver Windows version 1809/build 17762, vilket är den senaste versionen av [Windows långtids support](/windows/release-information/). För utvecklings-och test scenarier fungerar alla SKU: er (Pro, Enterprise, Server osv.) som stöder behållar funktionen. Se dock till att granska listan över system som stöds innan du går vidare till produktion.

IoT Edge med Linux-behållare kan köras på alla versioner av Windows som uppfyller [kraven för Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

### <a name="container-engine-requirements"></a>Krav för container motor

Azure IoT Edge förlitar sig på en [OCI-kompatibel](https://www.opencontainers.org/) container motor. Kontrol lera att enheten har stöd för behållare.

Om du installerar IoT Edge på en virtuell dator aktiverar du kapslad virtualisering och allokerar minst 2 GB minne. För Hyper-V kan virtuella datorer i generation 2 ha kapslad virtualisering aktiverat som standard. För VMware finns det en växling för att aktivera funktionen på den virtuella datorn.

Om du installerar IoT Edge på en IoT Core-enhet använder du följande kommando i en [fjärran sluten PowerShell-session](/windows/iot-core/connect-your-device/powershell) för att kontrol lera om Windows-behållare stöds på din enhet:

```powershell
Get-Service vmcompute
```

---

Azure IoT Edge program varu paket omfattas av licens villkoren som finns i varje paket ( `usr/share/doc/{package-name}` eller i `LICENSE` katalogen). Läs licens villkoren innan du använder ett paket. Din installation och användning av ett paket utgör ditt godkännande av dessa villkor. Om du inte godkänner licens villkoren ska du inte använda det paketet.

## <a name="install-a-container-engine"></a>Installera en behållar motor

Azure IoT Edge förlitar sig på en OCI-kompatibel container Runtime. För produktions scenarier rekommenderar vi att du använder den Moby-baserade motorn. Moby-motorn är den enda behållar motorn som stöds officiellt med Azure IoT Edge. Docker CE/EE-behållar avbildningar är kompatibla med Moby-körningsmiljön.

# <a name="linux"></a>[Linux](#tab/linux)

Uppdatera paket listor på enheten.

   ```bash
   sudo apt-get update
   ```

Installera Moby-motorn.

   ```bash
   sudo apt-get install moby-engine
   ```

Om du får fel när du installerar Moby container Engine, verifiera din Linux-kernel för kompatibilitet med Moby. Vissa inbäddade enhets tillverkare levererar enhets avbildningar som innehåller anpassade Linux-Kernels utan de funktioner som krävs för container Engine-kompatibilitet. Kör följande kommando, som använder skriptet för att [kontrol](https://github.com/moby/moby/blob/master/contrib/check-config.sh) lera konfigurationen som tillhandahålls av Moby, för att kontrol lera kernel-konfigurationen:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

I utdata från skriptet kontrollerar du att alla objekt under `Generally Necessary` och `Network Drivers` är aktiverade. Om du saknar funktioner kan du aktivera dem genom att återskapa din kernel från källa och välja de associerade modulerna som ska ingå i den aktuella kernel. config. På samma sätt kan du, om du använder en kernel-konfigurations generator som `defconfig` eller `menuconfig` , hitta och aktivera respektive funktioner och återskapa din kernel enligt detta. När du har distribuerat den nyligen ändrade kärnan kör du check config-skriptet igen för att kontrol lera att alla nödvändiga funktioner har Aktiver ATS.

# <a name="windows"></a>[Windows](#tab/windows)

För produktions scenarier använder du den Moby-baserade motorn som ingår i installations skriptet. Det finns inga ytterligare steg för att installera motorn.

För IoT Edge med Linux-behållare måste du ange en egen container Runtime. Installera [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) på enheten och konfigurera den så att den [använder Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) innan du fortsätter.

---

## <a name="install-the-iot-edge-security-daemon"></a>Installera IoT Edge Security daemon

IoT Edge Security daemon tillhandahåller och upprätthåller säkerhets standarder på den IoT Edge enheten. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge Runtime.

Stegen i det här avsnittet representerar den typiska processen för att installera den senaste versionen på en enhet som har Internet anslutning. Om du behöver installera en speciell version, t. ex. en för hands version, eller om du behöver installera offline, följer du stegen för [offline-eller detaljerad versions installation](#offline-or-specific-version-installation) i nästa avsnitt.

# <a name="linux"></a>[Linux](#tab/linux)

Uppdatera paket listor på enheten.

   ```bash
   sudo apt-get update
   ```

Kontrol lera vilka versioner av IoT Edge som är tillgängliga.

   ```bash
   apt list -a iotedge
   ```

Om du vill installera den senaste versionen av Security daemon använder du följande kommando som också installerar den senaste versionen av **libiothsm-STD-** paketet:

   ```bash
   sudo apt-get install iotedge
   ```

Om du vill installera en version av säkerhets-daemonen anger du versionen från apt lista utdata. Ange också samma version för **libiothsm-STD-** paketet, vilket annars skulle installera den senaste versionen. Följande kommando installerar till exempel den senaste versionen av 1.0.8-versionen:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Om den version som du vill installera inte finns med i listan följer du stegen för [offline-eller detaljerad versions installation](#offline-or-specific-version-installation) i nästa avsnitt. I avsnittet visas hur du riktar in dig på en tidigare version av IoT Edge Security daemon eller Release Candidate-versioner.

# <a name="windows"></a>[Windows](#tab/windows)

>[!TIP]
>För IoT core-enheter rekommenderar vi att du kör installations kommandona med en fjärran sluten PowerShell-session. Mer information finns i [använda PowerShell för Windows IoT](/windows/iot-core/connect-your-device/powershell).

1. Kör PowerShell som administratör.

   Använd en AMD64-session av PowerShell, inte PowerShell (x86). Om du är osäker på vilken typ av session du använder kör du följande kommando:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Kör kommandot [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , som utför följande uppgifter:

   * Kontrollerar att Windows-datorn finns på en version som stöds.
   * Aktiverar funktionen containers.
   * Laddar ned Moby-motorn och IoT Edge Runtime.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge`Kommandot använder som standard Windows-behållare. Om du vill använda Linux-behållare lägger du till `ContainerOs` parametern:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. I det här läget kan IoT core-enheter startas om automatiskt. Windows 10-eller Windows Server-enheter kan bli ombedd att starta om. Om så är fallet startar du om enheten nu.

När du installerar IoT Edge på en enhet kan du använda ytterligare parametrar för att ändra processen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Peka installations programmet till en lokal katalog för offline-installation.

Mer information om dessa ytterligare parametrar finns i [PowerShell-skript för IoT Edge i Windows](reference-windows-scripts.md).

---

Nu när behållar motorn och IoT Edge runtime är installerade på enheten är du redo för nästa steg, vilket är att registrera enheten med IoT Hub och konfigurera enheten med sin moln identitet och autentiseringsinformation.

Välj nästa artikel baserat på vilken autentiseringstyp du vill använda:

* [Symmetrisk nyckel autentisering](how-to-manual-provision-symmetric-key.md) är snabbare för att komma igång.
* [X. 509 certifikatautentisering](how-to-manual-provision-x509.md) är säkrare för produktions scenarier.

## <a name="offline-or-specific-version-installation"></a>Installation av offline eller en speciell version

Stegen i det här avsnittet gäller scenarier som inte omfattas av standard installations stegen. Detta kan vara:

* Installera IoT Edge offline
* Installera en version av Release Candidate
* På Windows installerar du en annan version än den senaste

# <a name="linux"></a>[Linux](#tab/linux)

Använd stegen i det här avsnittet om du vill installera en viss version av Azure IoT Edge runtime som inte är tillgänglig via `apt-get install` . Listan med Microsoft-paket innehåller bara en begränsad uppsättning av de senaste versionerna och deras under versioner, så dessa steg är för alla som vill installera en äldre version eller en version av en version av versionen.

Med hjälp av ett spiral kommando kan du rikta in komponentfilerna direkt från IoT Edge GitHub-lagringsplatsen.

<!-- TODO: Offline installation? -->

1. Gå till [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases)och leta upp den version som du vill använda som mål.

2. Expandera avsnittet **till gångar** för den versionen.

3. Varje utgåva bör ha nya filer för IoT Edge Security daemon och hsmlib. Använd följande kommandon för att uppdatera dessa komponenter.

   1. Hitta filen **libiothsm-STD** som matchar IoT Edge enhetens arkitektur. Högerklicka på fil länken och Kopiera länk adressen.

   2. Använd den kopierade länken i följande kommando för att installera den versionen av hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Hitta den **iotedge** -fil som matchar din IoT Edge enhets arkitektur. Högerklicka på fil länken och Kopiera länk adressen.

   4. Använd den kopierade länken i följande kommando för att installera den versionen av IoT Edge Security daemon.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

Under installationen hämtas tre filer:

* Ett PowerShell-skript som innehåller installations anvisningarna
* Microsoft Azure IoT Edge CAB, som innehåller IoT Edge Security daemon (iotedged), Moby container Engine och Moby CLI
* Installations program för Visual C++ Redistributable Package (VC Runtime)

Om enheten ska vara offline under installationen, eller om du vill installera en version av IoT Edge, kan du hämta filerna i förväg till enheten. När det är dags att installera, kan du peka installations skriptet i katalogen som innehåller de hämtade filerna. Installations programmet kontrollerar först katalogen och hämtar bara komponenter som inte hittas. Om alla filer är tillgängliga offline kan du installera utan Internet anslutning.

1. De senaste IoT Edge-installationsfilerna tillsammans med tidigare versioner finns i [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases).

2. Leta upp den version som du vill installera och ladda ned följande filer från avsnittet **till gångar** i viktig information på din IoT-enhet:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab från att lansera 1.0.9 eller senare, eller Microsoft-Azure-IoTEdge.cab från att lansera 1.0.8 och äldre.

   Microsoft-Azure-IotEdge-arm32.cab är även tillgängligt från och med 1.0.9 endast för testning. IoT Edge stöds för närvarande inte på Windows ARM32-enheter.

   Det är viktigt att använda PowerShell-skriptet från samma version som. cab-filen som du använder eftersom funktionen ändras till att stödja funktionerna i varje version.

3. Om CAB-filen som du laddade ned har ett arkitektur-suffix, byter du namn på filen till bara **Microsoft-Azure-IoTEdge.cab**.

4. Du kan också hämta ett installations program för Visual C++ Redistributable. PowerShell-skriptet använder till exempel den här versionen: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Spara installations programmet i samma mapp på din IoT-enhet som IoT Edge-filer.

5. Om du vill installera med offline-komponenter kan du [punkt källa](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) den lokala kopian av PowerShell-skriptet. 

6. Kör kommandot [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) med `-OfflineInstallationPath` parametern. Ange den absoluta sökvägen till fil katalogen. Exempel:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Distributions kommandot använder de komponenter som finns i den lokala fil katalogen. Om CAB-filen eller Visual C++-installationsprogrammet saknas försöker den hämta dem.

---

Nu när behållar motorn och IoT Edge runtime är installerade på enheten är du redo för nästa steg, vilket är att [autentisera en IoT Edge-enhet i IoT Hub](how-to-manual-provision-symmetric-key.md).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge installationen från enheten använder du följande kommandon.

# <a name="linux"></a>[Linux](#tab/linux)

Ta bort IoT Edge-körningen.

```bash
sudo apt-get remove --purge iotedge
```

När IoT Edge runtime tas bort, stoppas alla behållare som den skapade, men de finns kvar på enheten. Visa alla behållare för att se vilka som är kvar.

```bash
sudo docker ps -a
```

Ta bort behållarna från enheten, inklusive de två körnings behållarna.

```bash
sudo docker rm -f <container name>
```

Slutligen tar du bort behållar körningen från enheten.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

# <a name="windows"></a>[Windows](#tab/windows)

Om du vill ta bort IoT Edge installationen från Windows-enheten använder du kommandot [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) från ett administrations PowerShell-fönster. Det här kommandot tar bort IoT Edge runtime, tillsammans med den befintliga konfigurationen och Moby-motorns data.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

`Uninstall-IoTEdge`Kommandot fungerar inte på Windows IoT Core. Om du vill ta bort IoT Edge måste du distribuera om din Windows IoT Core-avbildning.

Om du vill ha mer information om avinstallations alternativ använder du kommandot `Get-Help Uninstall-IoTEdge -full` .

---

## <a name="next-steps"></a>Nästa steg

När du har installerat IoT Edge runtime konfigurerar du enheten för att ansluta till IoT Hub. I följande artiklar går vi igenom hur du registrerar en ny enhet i molnet och sedan förser enheten med identitets-och autentiseringsinformation.

Välj nästa artikel baserat på vilken typ av autentisering du vill använda:

* **Symmetrisk nyckel**: både IoT Hub och IoT Edges enheten har en kopia av en säker nyckel. När enheten ansluter till IoT Hub kontrollerar de att nycklarna matchar. Den här autentiseringsmetoden är snabbare för att komma igång, men inte lika säkert.

  [Konfigurera en Azure IoT Edge enhet med autentisering med symmetrisk nyckel](how-to-manual-provision-symmetric-key.md)

* **X. 509-självsignerad**: den IoT Edge enheten har X. 509 identitets certifikat och IoT Hub tilldelas certifikatets tumavtryck. När enheten ansluter till IoT Hub jämför de certifikatet med tumavtrycket. Den här autentiseringsmetoden är säkrare och rekommenderas för produktions scenarier.

  [Konfigurera en Azure IoT Edge-enhet med X. 509 certifikatautentisering](how-to-manual-provision-x509.md)