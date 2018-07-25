---
title: Simulera Azure IoT Edge i Windows | Microsoft Docs
description: Installera Azure IoT Edge-körningen på en simulerad enhet i Windows och distribuera din första modul
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 54a8b5f14cc2f9fb0ac887da8995623353e73ac9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115593"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Snabbstart: Distribuera din första IoT Edge-modul från Azure Portal till en Windows.enhet – förhandsgranskning

Med Azure IoT Edge kan du utföra analyser och databearbetning på dina enheter utan att behöva push-överföra alla data till molnet. I självstudierna för IoT Edge visar vi hur du distribuerar olika typer av moduler, men du måste först ha en enhet som ska testas. 

I den här snabbstarten lär du dig att:

1. Skapa en IoT Hub
2. Registrera en IoT Edge-enhet
3. Starta IoT Edge-körningen
4. Distribuera en modul

![Självstudiens arkitektur][2]

Modulen som du distribuerar i den här snabbstarten är en simulerad sensor som genererar temperatur-, fuktighets- och lufttrycksdata. De andra självstudierna i Azure IoT Edge bygger vidare på det arbete som du gör här, genom att distribuera moduler som analyserar simulerade data för verksamhetsinsyn. 

>[!NOTE]
>IoT Edge-körning i Windows finns i [den öppna förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto][lnk-account] innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här snabbstarten förutsätter att du använder en dator eller en virtuell dator som kör Windows när du simulerar en IoT-enhet. Om du kör Windows på en virtuell dator aktiverar du [kapslad virtualisering][lnk-nested] och allokerar minst 2 GB minne. 

Förbered följande på den dator som du använder för en IoT Edge-enhet:

1. Kontrollera att du använder en Windows-version som stöds:
   * Windows 10 eller senare
   * Windows Server 2016 eller senare
2. Installera [Docker för Windows][lnk-docker] och kontrollera att den körs.
3. Konfigurera Docker för att använda [Linux-containrar](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta snabbstarten genom att skapa en IoT-hubb i Azure-portalen.
![Skapa IoT Hub][3]

Skapa IoT-hubben i en resursgrupp som du kan använda för att underhålla och hantera alla resurser som du skapar i den här snabbstarten. Anropa något som är lätt att komma ihåg, som **IoTEdgeResources**. Genom att lägga alla resurser för snabbstarten och självstudierna i en grupp kan du hantera dem tillsammans och enkelt ta bort dem när du har testat färdigt. 

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med den IoT Hub som du nyss skapade.
![Registrera en enhet][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera och starta Azure IoT Edge-körningen på din IoT Edge-enhet. 
![Registrera en enhet][5]

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. **IoT Edge säkerhetsdaemon** startas varje gång en Edge-enhet startar. Enheten startas genom att IoT Edge-agenten startas. **IoT Edge-agenten** underlättar distribution och övervakning av moduler på IoT Edge-enheten, inklusive IoT Edge-hubb. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub. 

>[!NOTE]
>Installationsstegen i det här avsnittet görs manuellt under tiden ett installationsskript utvecklas. 

Anvisningarna i det här avsnittet konfigurerar IoT Edge-körningen med Linux-containrar. Om du vill använda Windows-containrar, se informationen i avsnittet om att [installera Azure IoT Edge-körning i Windows för användning med Windows-containrar](how-to-install-iot-edge-windows-with-windows.md).

### <a name="download-and-install-the-iot-edge-service"></a>Ladda ned och installera IoT Edge-tjänsten

1. Kör PowerShell som administratör på din IoT Edge-enhet.

2. Ladda ned IoT Edge service pack.

   ```powershell
   Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
   Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
   Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
   rmdir C:\ProgramData\iotedge\iotedged-windows
   $sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
   $path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
   Set-ItemProperty -Path $sysenv -Name Path -Value $path
   ```

3. Installera vcruntime.

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. Skapa och starta IoT Edge-tjänsten.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. Lägg till brandväggsundantag för de portar som används av IoT Edge-tjänsten.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. Skapa en ny fil med namnet **iotedge.reg** och öppna den i en textredigerare. 

7. Lägg till följande innehåll i filen och spara. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. Navigera till filen i Utforskaren och dubbelklicka på den för att importera ändringarna i Windows-registret. 

### <a name="configure-the-iot-edge-runtime"></a>Konfigurera IoT Edge-körningen 

Konfigurera körningen med anslutningssträngen för IoT Edge-enheten som du kopierade när du registrerat en ny enhet. Konfigurera sedan runtime-nätverket. 

1. Öppna konfigurationsfilen för IoT Edge som finns på `C:\ProgramData\iotedge\config.yaml`. Filen är skyddad, så kör en textredigerare som t.ex. Notepad som administratör och öppna filen i redigeraren. 

2. Leta upp avsnittet om **etablering** och uppdatera värdet för **device_connection_string** med strängen som du kopierade från informationen om din IoT Edge-enhet. 

3. Hämta värdnamnet för din IoT Edge-enhet i administratörsfönstret för PowerShell och kopiera utdata. 

   ```powershell
   hostname
   ```

4. Leta upp avsnittet för **värddatornamn för Edge-enheten** i konfigurationsfilen. Uppdatera värdet för **värddatornamn** med värdnamnet som du kopierade från PowerShell.

3. Hämta IP-adressen för din IoT Edge-enhet i administratörsfönstret för PowerShell. 

   ```powershell
   ipconfig
   ```

4. Kopiera värdet för **IPv4-adressen** i avsnittet **vEthernet (DockerNAT)** för utdata. 

5. Skapa en miljövariabel med namnet **IOTEDGE_HOST**, ersätt *\<ip_address\>* med IP-adressen för din IoT Edge-enhet. 

  ```powershell
  [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
  ```

  Bevara miljövariabeln mellan omstarter.

  ```powershell
  SETX /M IOTEDGE_HOST "http://<ip_address>:15580"
  ```

6. Leta upp avsnittet för **anslutningsinställningar** i filen `config.yaml`. Uppdatera värdena för **management_uri** och **workload_uri** med de IP-adresser och portar som du öppnade i föregående avsnitt. Ersätt **\<GATEWAY_ADDRESS\>** med DockerNAT-IP-adressen som du kopierade.

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. Leta upp avsnittet med **inställningar för lyssnande** och lägg till samma värden för **management_uri** och **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. Leta upp avsnittet med **körningsinställningar för Moby-containrar** och kontrollera att värdet för **nätverk** är satt till `nat`.

9. Spara konfigurationsfilen. 

10. Starta om IoT Edge-tjänsten i PowerShell.

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>Visa status för IoT Edge-körningen

Kontrollera att körningen har installerats och konfigurerats korrekt.

1. Kontrollera status för IoT Edge-tjänsten.

   ```powershell
   Get-Service iotedge
   ```

2. Hämta tjänstloggar om du behöver felsöka tjänsten. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false}
   ```

3. Visa alla moduler som körs på din IoT Edge-enhet. Eftersom det är första gången du startar tjänsten, bör du bara kunna se den **edgeAgent**-modul som körs. Modulen edgeAgent körs som standard och hjälper dig att installera och starta ytterligare moduler som du distribuerar till enheten. 

   ```powershell
   iotedge list
   ```

   ![Visa en modul på din enhet](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som ska skicka telemetridata till IoT Hub.
![Registrera en enhet][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I den här snabbstarten skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure Portal för att skicka en IoT Edge-modul som ska köras på enheten utan att några ändringar behövs göras i själva enheten. I det här fallet skapar modulen som du överförde de miljödata som du använder i självstudierna. 

Kontrollera att modulen distribuerades från molnet som körs på IoT Edge-enheten. 

```powershell
iotedge list
```

   ![Visa tre moduler på enheten](./media/quickstart/iotedge-list-2.png)

Visa meddelanden som skickas från modulen tempSensor till molnet. 

```powershell
iotedge logs tempSensor -f
```

  ![Visa data från modulen](./media/quickstart/iotedge-logs.png)

Du kan visa meddelanden som tas emot av din IoT Hub med [verktyget IoT Hub Explorer][lnk-iothub-explorer] eller [Azure IoT Toolkit-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med IoT Edge-självstudierna kan du använda enheten du registrerade och konfigurerade i den här snabbstarten. I annat fall kan du ta bort de Azure-resurser som du skapade och ta bort IoT Edge-körningen från enheten. 

### <a name="delete-azure-resources"></a>Ta bort Azure-resurser

Om du skapade den virtuella datorn och IoT-hubben i en ny resursgrupp kan du ta bort den gruppen och alla associerade resurser. Om det finns något i den resursgruppen som du vill behålla tar du bara bort de enskilda resurser som du vill rensa. 

Om du vill ta bort en resursgrupp följer du dessa steg: 

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.
2. I textrutan **Filtrera efter namn ...** , skriver du namnet på resursgruppen som innehåller din IoT Hub. 
3. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.
4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

### <a name="remove-the-iot-edge-runtime"></a>Ta bort IoT Edge-körningen

Om du planerar att använda IoT Edge-enheten för framtida testning men vill stoppa tempSensor-modulen från att skicka data till IoT-hubben använder du följande kommando för att stoppa IoT Edge-tjänsten. 

   ```powershell
   Stop-Service iotedge -NoWait
   ```

Du kan starta om tjänsten när du är redo att börja testa igen

   ```powershell
   Start-Service iotedge
   ```

Om du vill ta bort installationerna från din enhet kan du använda följande kommandon.  

Ta bort IoT Edge-körningen.

   ```powershell
   cmd /c sc delete iotedge
   rm -r c:\programdata\iotedge
   ```

När IoT Edge-körningen tas bort stoppas de containrar som den skapade, men de finns fortfarande kvar på enheten. Visa alla containrar.

   ```powershell
   docker ps -a
   ```

Ta bort de containrar som skapades på enheten av IoT Edge-körningen. Ändra namnet på containern tempSensor om du kallade den för något annat. 

   ```powershell
   docker rm -f tempSensor
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny IoT Edge-enhet och du använde molngränssnittet i Azure IoT Edge för att distribuera kod på enheten. Nu har du en testenhet som genererar rådata för sin miljö. 

Du kan nu fortsätta med någon av de andra självstudierna om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Filtrera sensordata med hjälp av en Azure-funktion](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
