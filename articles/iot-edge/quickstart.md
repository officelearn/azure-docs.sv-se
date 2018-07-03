---
title: Snabbstart Azure IoT Edge + Windows | Microsoft Docs
description: Testa Azure IoT Edge genom att köra analys på en simulerad Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/24/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: df22040de398810fd9250ef46da2f95b6915c4a9
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030666"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Snabbstart: Distribuera din första IoT Edge-modul från Azure Portal till en Windows.enhet – förhandsgranskning

Använd Azure IoT Edge-molnsgränssnittet när du ska fjärrdistribuera färdig kod till en IoT-enhet i den här snabbstarten. Utför den här uppgiften genom att först använda din Windows-enhet för att simulera en IoT Edge-enhet, varefter du kan distribuera en modul till den.

I den här snabbstarten lär du dig att:

1. Skapa en IoT Hub.
2. Registrera en IoT Edge-enhet till din IoT Hub.
3. Installera och starta IoT Edge-körningen på enheten.
4. Fjärrdistribuera en modul till en IoT Edge-enhet och skicka telemetri till IoT Hub.

![Självstudiens arkitektur][2]

Modulen som du distribuerar i den här snabbstarten är en simulerad sensor som genererar temperatur-, fuktighets- och lufttrycksdata. De andra självstudierna i Azure IoT Edge bygger vidare på det arbete som du gör här, genom att distribuera moduler som analyserar simulerade data för verksamhetsinsyn. 

>[!NOTE]
>IoT Edge-körning i Windows finns i [den öppna förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto][lnk-account] innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Denna snabbstart gör din Windows-dator eller virtuella dator till en IoT Edge-enhet. Om du kör Windows på en virtuell dator aktiverar du [kapslad virtualisering][lnk-nested] och allokerar minst 2 GB minne. 

Förbered följande på den dator som du använder för en IoT Edge-enhet:

1. Kontrollera att du använder en Windows-version som stöds:
   * Windows 10 eller senare
   * Windows Server 2016 eller senare
2. Installera [Docker för Windows][lnk-docker] och kontrollera att den körs.
3. Konfigurera Docker för att använda [Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kommer använda Azure CLI för att slutföra många av stegen i den här snabbstarten och Azure IoT har ett tillägg för att aktivera ytterligare funktioner. 

Lägg till Azure IoT-tillägget till Cloud Shell-instansen.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta snabbstarten genom att skapa din IoT Hub i Azure Portal.
![Skapa IoT Hub][3]

Den kostnadsfria nivån för IoT Hub fungerar för den här snabbstarten. Om du har använt IoT Hub tidigare och redan har skapat en kostnadsfri hubb kan du använda den. Varje prenumeration kan bara ha en kostnadsfri IoT Hub. 

1. Skapa en resursgrupp i Azure Cloud Shell. I följande exempel skapas en resursgrupp med namnet **TestResources** i regionen **västra USA**. Genom att lägga alla resurser för snabbstarten och självstudierna i en grupp kan du hantera dem tillsammans. 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. Skapa en IoT Hub i din nya resursgrupp. Följande kod skapar en kostnadsfri **F1**-hubb i resursgruppen **TestResources**. Ersätt *{hub_name}* med ett unikt namn för din IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med den IoT Hub som du nyss skapade.
![Registrera en enhet][4]

Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT Hub. Eftersom IoT Edge-enheterna fungerar och kan hanteras annorlunda än vanliga IoT-enheter, kan du ange att detta ska vara en IoT Edge-enhet från början. 

1. Ange följande kommando i Azure Cloud Shell för att skapa en enhet med namnet **myEdgeDevice** i din hubb.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. Hämta anslutningssträngen för din enhet som länkar den fysiska enheten med sin identitet i IoT Hub. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. Kopiera anslutningssträngen och spara den. Du behöver det här värdet för att konfigurera IoT Edge-körningen i nästa avsnitt. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera och starta Azure IoT Edge-körningen på din IoT Edge-enhet. 
![Registrera en enhet][5]

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. **IoT Edges säkerhetsdaemon** startas varje gång en Edge-enhet startar. Enheten startas genom att IoT Edge-agenten startas. **IoT Edge-agenten** underlättar distribution och övervakning av moduler på IoT Edge-enheten, inklusive IoT Edge-hubb. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub. 

>[!NOTE]
>Installationsstegen i det här avsnittet görs manuellt under tiden ett installationsskript utvecklas. 

Anvisningarna i det här avsnittet konfigurerar IoT Edge-körningen med Linux-behållare. Om du vill använda Windows-behållare, se informationen i avsnittet om att [installera Azure IoT Edge-körning i Windows för användning med Windows-behållare](how-to-install-iot-edge-windows-with-windows.md).

### <a name="download-and-install-the-iot-edge-service"></a>Ladda ned och installera IoT Edge-tjänsten

1. Kör PowerShell som administratör på din IoT Edge-enhet.

2. Ladda ned IoT Edge service pack.

  ```powershell
  Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
  Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
  Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
  rmdir C:\ProgramData\iotedge\iotedged-windows
  $env:Path += ";C:\ProgramData\iotedge"
  SETX /M PATH "$env:Path"
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

1. Öppna konfigurationsfilen för IoT Edge som finns på `C:\ProgramData\iotedge\config.yaml`. Filen är skyddad, så kör en textredigerare som t.ex. Notepad som en administratör och öppna filen i redigeraren. 

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

6. Leta upp avsnittet för **anslutningsinställningar** i filen `config.yaml`. Uppdatera värdena för **management_uri** och **workload_uri** med de IP-adresser och portar som du öppnade i föregående avsnitt. 

   ```yaml
   connect: 
     management_uri: "http://<ip_address>:15580"
     workload_uri: "http://<ip_address>:15581"
   ```

7. Leta upp avsnittet med **inställningar för lyssnande** och lägg till samma värden för **management_uri** och **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<ip_address>:15580"
     workload_uri: "http://<ip_address:15581"
   ```

8. Leta upp avsnittet med **körningsinställningar för Moby-behållare** och kontrollera att värdet för **nätverk** är satt till `nat`.

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

Du kan använda den simulerade enheten som du konfigurerade i denna snabbstart för att testa IoT Edge-självstudier. Om du vill stoppa modulen tempSensor från att skicka data till din IoT Hub, använder du följande kommando för att stoppa IoT Edge-tjänsten och ta bort de behållare som har skapats på enheten. Kom ihåg att starta tjänsten när du vill använda din dator som en IoT Edge-enhet igen. 

   ```powershell
   Stop-Service iotedge -NoWait
   docker rm -f $(docker ps -aq)
   ```

Du kan använda följande kommando för att ta bort resursgruppen som du skapade och alla resurser som är kopplade till den när du inte längre behöver Azure-resurserna som du skapade:

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en ny IoT Edge-enhet och du använde molngränssnittet i Azure IoT Edge för att distribuera kod på enheten. Nu har du en testenhet som genererar rådata för sin miljö. 

Du kan nu fortsätta med någon av de andra självstudierna om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Filtrera sensordata med hjälp av en Azure-funktion](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png


<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
