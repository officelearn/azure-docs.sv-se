---
title: Snabbstart för att skapa en Azure IoT Edge-enhet i Windows | Microsoft Docs
description: I den här snabbstarten får du lära dig att skapa en IoT Edge-enhet och sedan fjärrdistribuera färdig kod från Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/19/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 8b446e3cfd3efc7d6f4c125747630cd3241fa804
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573934"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Snabbstart: Distribuera din första IoT Edge-modul från Azure Portal till en Windows.enhet – förhandsversion

Använd Azure IoT Edge-molnsgränssnittet när du ska fjärrdistribuera färdig kod till en IoT-enhet i den här snabbstarten. Om du vill utföra den här uppgiften, först skapa och konfigurera en Windows-dator att fungera som en IoT Edge-enhet, kan sedan du distribuera en modul till den.

I den här snabbstarten lär du dig att:

1. Skapa en IoT Hub.
2. Registrera en IoT Edge-enhet till din IoT Hub.
3. Installera och starta IoT Edge-körningen på enheten.
4. Fjärrdistribuera en modul till en IoT Edge-enhet och skicka telemetri till IoT Hub.

![Diagram – Snabbstart av arkitektur för enhet och moln](./media/quickstart/install-edge-full.png)

Modulen som du distribuerar i den här snabbstarten är en simulerad sensor som genererar temperatur-, fuktighets- och lufttrycksdata. De andra självstudierna i Azure IoT Edge bygger vidare på det arbete som du gör här, genom att distribuera moduler som analyserar simulerade data för verksamhetsinsyn.

> [!NOTE]
> IoT Edge-körning i Windows finns i [den öppna förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kommer använda Azure CLI för att slutföra många av stegen i den här snabbstarten och Azure IoT har ett tillägg för att aktivera ytterligare funktioner.

Lägg till Azure IoT-tillägget till Cloud Shell-instansen.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Nödvändiga komponenter

Molnresurser:

* En resursgrupp som du använder för att hantera alla resurser i den här snabbstarten.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-enhet:

* En Windows virtuell dator att fungera som IoT Edge-enhet. Du kan skapa den här virtuella datorn med följande kommando, ersätta *{password}* med ett säkert lösenord:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Det kan ta några minuter att skapa och starta den nya virtuella datorn. Du kan sedan hämta en RDP-fil som används när du ansluter till den virtuella datorn:

  1. Gå till din nya Windows virtuell dator i Azure-portalen.
  1. Välj **Anslut**.
  1. På den **RDP** fliken **ladda ned RDP-filen**.

  Öppna den här filen med anslutning till fjärrskrivbord kan ansluta till din Windows-dator med databasadministratörens namn och lösenord du angav med den `az vm create` kommando.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta snabbstarten genom att skapa en IoT-hubb med Azure CLI.

![Diagram – Skapa en IoT-hubb i molnet](./media/quickstart/create-iot-hub.png)

Den kostnadsfria nivån för IoT Hub fungerar för den här snabbstarten. Om du har använt IoT Hub tidigare och redan har skapat en kostnadsfri hubb kan du använda den. Varje prenumeration kan bara ha en kostnadsfri IoT Hub.

Följande kod skapar en kostnadsfri **F1**-hubb i resursgruppen **IoTEdgeResources**. Ersätt *{hub_name}* med ett unikt namn för din IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Om du får ett felmeddelande eftersom det redan finns en kostnadsfri hubb i din prenumeration ändrar du SKU till **S1**. Om du får ett felmeddelande om att IoT Hub-namnet inte är tillgängligt innebär det att någon annan redan har en hubb med det namnet. Prova med ett nytt namn.

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med den IoT Hub som du nyss skapade.
![Diagram – Registrera en enhet med en IoT Hub-identitet](./media/quickstart/register-device.png)

Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT Hub. Enhetsidentiteten finns i molnet, och du använder en unik enhetsanslutningssträng för att associera en fysisk enhet med en enhetsidentitet.

Eftersom IoT Edge-enheter fungerar och kan hanteras på annat sätt än typiska IoT-enheter deklarerar du den här identiteten till att höra till en IoT Edge-enhet med flaggan `--edge-enabled`.

1. Ange följande kommando i Azure Cloud Shell för att skapa en enhet med namnet **myEdgeDevice** i din hubb.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   Om du får ett felmeddelande om iothubowner-principnycklar kontrollerar du att CloudShell kör den senaste versionen av tillägget azure-cli-iot-ext.

2. Hämta anslutningssträngen för din enhet som länkar den fysiska enheten med dess identitet i IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Kopiera värdet för `connectionString`-nyckeln från JSON-utdata och spara det. Det här värdet är enhetens anslutningssträng. Du ska använda den här anslutningssträngen för att konfigurera IoT Edge-körningen i nästa avsnitt.

   ![Hämta anslutningssträngen från CLI-utdata](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera Azure IoT Edge-körningen på IoT Edge-enheten och konfigurera den med en enhetsanslutningssträng.
![Diagram – Starta körningen på enheten](./media/quickstart/start-runtime.png)

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. **IoT Edge-säkerhetsdaemon** startas varje gång en IoT Edge-enhet startar. Enheten startas genom att IoT Edge-agenten startas. **IoT Edge-agenten** hanterar distribution och övervakning av moduler på IoT Edge-enheten, inklusive IoT Edge-hubben. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten samt mellan enheten och IoT Hub.

Installationsskriptet innehåller också en container-motor som kallas Moby som hanterar containeravbildningar på din IoT Edge-enhet.

Under körningsinstallationen tillfrågas du om en enhetsanslutningssträng. Använd den sträng som du hämtade från Azure CLI. Den här strängen associerar den fysiska enheten med IoT Edge-enhetsidentiteten i Azure.

### <a name="connect-to-your-iot-edge-device"></a>Ansluta till din IoT Edge-enhet

Stegen i det här avsnittet alla äga rum på din IoT Edge-enhet, så att du vill ansluta till den virtuella datorn nu via fjärrskrivbord.

### <a name="prepare-your-device-for-containers"></a>Förbered enheten för behållare

Installationsskriptet installerar automatiskt Moby-motorn på din enhet innan du installerar IoT Edge. Förbered din enhet genom att aktivera funktionen behållare.

1. Sök efter i startfältet **aktivera Windows-funktioner på eller av** och öppna programmet på Kontrollpanelen.
1. Hitta och välja **behållare**.
1. Välj **OK**.

Det är klart när, du måste starta om Windows för att ändringarna träder i kraft, men du kan göra det från fjärrskrivbordssessionen i stället för att starta om den virtuella datorn från Azure-portalen.

### <a name="download-and-install-the-iot-edge-service"></a>Ladda ned och installera IoT Edge-tjänsten

Använd PowerShell för att ladda ned och installera IoT Edge-körningen. Använd den enhetsanslutningssträng som du hämtade från IoT Hub för att konfigurera din enhet.

1. Kör PowerShell som administratör på din IoT Edge-enhet.

2. Ladda ned och installera IoT Edge-tjänsten på din enhet.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

3. När du tillfrågas om en **DeviceConnectionString** (enhetsanslutningssträng) anger den sträng som du kopierade i föregående avsnitt. Använd inte citattecknen runt anslutningssträngen.

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
    sort-object @{Expression="TimeCreated";Descending=$false} |
    format-table -autosize -wrap
   ```

3. Visa alla moduler som körs på din IoT Edge-enhet. Eftersom det är första gången du startar tjänsten, bör du bara kunna se den **edgeAgent**-modul som körs. Modulen edgeAgent körs som standard och hjälper dig att installera och starta ytterligare moduler som du distribuerar till enheten.

   ```powershell
   iotedge list
   ```

   ![Visa en modul på din enhet](./media/quickstart/iotedge-list-1.png)

Det kan ta några minuter att slutföra installationen och IoT Edge-agenten modulen ska börja.

Din IoT Edge-enhet har nu konfigurerats. Den är redo att köra molndistribuerade moduler.

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som skickar telemetridata till IoT Hub.
![Diagram – Distribuera en modul från ett moln till en enhet](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I den här snabbstarten registrerade du en IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure-portalen för att distribuera en IoT Edge-modul som ska köras på enheten utan att några ändringar behövs göras i själva enheten.

I det här fallet skapar modulen som du distribuerade exempeldata som du kan använda för testning. Modulen för en simulerad temperatursensor genererar miljödata som du kan använda för testning senare. Den simulerade sensorn övervakar både en dator och miljön runt datorn. Den här sensorn kan till exempel finnas i ett serverrum, på fabriksgolvet eller på en vindturbin. Meddelandet innehåller rumstemperatur och fuktighet, maskintemperatur och tryck samt en tidsstämpel. I IoT Edge-självstudierna används de data som skapas av den här modulen som testdata för analys.

Kontrollera att modulen distribuerades från molnet som körs på IoT Edge-enheten.

```powershell
iotedge list
```

   ![Visa tre moduler på enheten](./media/quickstart/iotedge-list-2.png)

Visa meddelanden som skickas från modulen temperatursensor till molnet.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >IoT Edge-kommandon är skiftlägeskänsliga när det gäller modulnamnen.

   ![Visa data från modulen](./media/quickstart/iotedge-logs.png)

Du kan även se när meddelandena tas emot av din IoT-hubb med hjälp av [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (tidigare Azure IoT Toolkit-tillägget).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med IoT Edge-självstudierna kan du använda enheten du registrerade och konfigurerade i den här snabbstarten. I annat fall kan du ta bort Azure-resurser som du skapade för att undvika avgifter.

Om du skapade den virtuella datorn och IoT-hubben i en ny resursgrupp kan du ta bort den gruppen och alla associerade resurser. Kontrollera att det inte finns något du vill behålla i innehållet i resursgruppen. Om du inte vill ta bort hela gruppen kan du ta bort enskilda resurser i stället.

Ta bort gruppen **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en IoT Edge-enhet och använde molngränssnittet i Azure IoT Edge för att distribuera kod till enheten. Nu har du en testenhet som genererar rådata för sin miljö.

Nästa steg är att ställa in din lokala utvecklingsmiljö så att du kan börja skapa IoT Edge-moduler som kör din affärslogik. 

> [!div class="nextstepaction"]
> [Börja utveckla IoT Edge-moduler för Windows-enheter](tutorial-develop-for-windows.md)
