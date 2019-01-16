---
title: Snabbstart för att skapa en Azure IoT Edge-enhet i Windows | Microsoft Docs
description: I den här snabbstarten får du lära dig att skapa en IoT Edge-enhet och sedan fjärrdistribuera färdig kod från Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/31/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: e0ad51bd2370cd8b7569d76e5d91b606928eea6d
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189362"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Snabbstart: Distribuera din första IoT Edge-modul från Azure Portal till en Windows.enhet – förhandsversion

Använd Azure IoT Edge-molnsgränssnittet när du ska fjärrdistribuera färdig kod till en IoT-enhet i den här snabbstarten. Utför den här uppgiften genom att först använda din Windows-enhet för att simulera en IoT Edge-enhet, varefter du kan distribuera en modul till den.

I den här snabbstarten lär du dig att:

1. Skapa en IoT Hub.
2. Registrera en IoT Edge-enhet till din IoT Hub.
3. Installera och starta IoT Edge-körningen på enheten.
4. Fjärrdistribuera en modul till en IoT Edge-enhet och skicka telemetri till IoT Hub.

![Diagram – Snabbstart av arkitektur för enhet och moln](./media/quickstart/install-edge-full.png)

Modulen som du distribuerar i den här snabbstarten är en simulerad sensor som genererar temperatur-, fuktighets- och lufttrycksdata. De andra självstudierna i Azure IoT Edge bygger vidare på det arbete som du gör här, genom att distribuera moduler som analyserar simulerade data för verksamhetsinsyn.

>[!NOTE]
>IoT Edge-körning i Windows finns i [den öppna förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
   az group create --name IoTEdgeResources --location westus
   ```

IoT Edge-enhet:

* En Windows-dator eller en virtuell dator som fungerar som din IoT Edge-enhet. Använd en version av Windows som stöds:
  * Windows 10 eller IoT Core med uppdateringen från oktober 2018 (version 17763)
  * Windows Server 2019
* Aktivera virtualisering så att enheten kan vara värd för containers
   * Om det är en Windows-dator kan du aktivera container-funktionen. I startfältet går du till **Aktivera Windows-funktioner till på eller av** och markera kryssrutan bredvid **Containers**.
   * Om det är en virtuell dator aktiverar du [kapslad virtualisering](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) och allokerar minst 2 GB minne.

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

3. Kopiera anslutningssträngen från JSON-utdata och spara den. Du behöver det här värdet för att konfigurera IoT Edge-körningen i nästa avsnitt.

   ![Hämta anslutningssträngen från CLI-utdata](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera Azure IoT Edge-körningen på IoT Edge-enheten och konfigurera den med en enhetsanslutningssträng.
![Diagram – Starta körningen på enheten](./media/quickstart/start-runtime.png)

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. **IoT Edge-säkerhetsdaemon** startas varje gång en IoT Edge-enhet startar. Enheten startas genom att IoT Edge-agenten startas. **IoT Edge-agenten** hanterar distribution och övervakning av moduler på IoT Edge-enheten, inklusive IoT Edge-hubben. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten samt mellan enheten och IoT Hub.

Installationsskriptet innehåller också en container-motor som kallas Moby som hanterar containeravbildningar på din IoT Edge-enhet. 

Under körningsinstallationen tillfrågas du om en enhetsanslutningssträng. Använd den sträng som du hämtade från Azure CLI. Den här strängen associerar den fysiska enheten med IoT Edge-enhetsidentiteten i Azure.

Anvisningarna i det här avsnittet konfigurerar IoT Edge-körningen med Windows-containrar. Om du vill använda Linux-containrar, se [installera Azure IoT Edge-körningen på Windows](how-to-install-iot-edge-windows-with-linux.md) för krav och anvisningar för installation.

### <a name="connect-to-your-iot-edge-device"></a>Ansluta till din IoT Edge-enhet

Alla steg i det här avsnittet sker på din IoT Edge-enhet. Om du använder en virtuell dator eller sekundär maskinvara bör du ansluta till denna dator nu via SSH eller fjärrskrivbord. Om du använder din egen dator som IoT Edge-enhet kan du fortsätta till nästa avsnitt. 

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

Din IoT Edge-enhet har nu konfigurerats. Den är redo att köra molndistribuerade moduler.

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som skickar telemetridata till IoT Hub.
![Diagram – Distribuera en modul från ett moln till en enhet](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I den här snabbstarten registrerade du en IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure-portalen för att distribuera en IoT Edge-modul som ska köras på enheten utan att några ändringar behövs göras i själva enheten. 

I det här fallet skapar den modul som du skickade exempeldata som du kan använda för testning. Modulen för en simulerad temperatursensor genererar miljödata som du kan använda för testning senare. Den simulerade sensorn övervakar både en dator och miljön runt datorn. Den här sensorn kan till exempel finnas i ett serverrum, på fabriksgolvet eller på en vindturbin. Meddelandet innehåller omgivande temperatur och fuktighet, datorns temperatur och tryck samt en tidsstämpel. I IoT Edge-självstudierna används de data som skapas av den här modulen som testdata för analys.

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

Om du vill fortsätta med IoT Edge-självstudierna kan du använda enheten du registrerade och konfigurerade i den här snabbstarten. I annat fall kan du ta bort de Azure-resurser som du skapade och ta bort IoT Edge-körningen från enheten.

### <a name="delete-azure-resources"></a>Ta bort Azure-resurser

Om du skapade den virtuella datorn och IoT-hubben i en ny resursgrupp kan du ta bort den gruppen och alla associerade resurser. Kontrollera att det inte finns något du vill behålla i innehållet i resursgruppen. Om du inte vill ta bort hela gruppen kan du ta bort enskilda resurser i stället.

Ta bort gruppen **IoTEdgeResources**.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources
   ```

### <a name="remove-the-iot-edge-runtime"></a>Ta bort IoT Edge-körningen

Om du vill ta bort installationerna från din enhet kan du använda följande kommandon.  

Ta bort IoT Edge-körningen. Om du tänker installera om IoT Edge, hoppar du över parametrarna `-DeleteConfig` och `-DeleteMobyDataRoot` så att du kan installera om med samma konfiguration som du precis har konfigurerat.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
   ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en IoT Edge-enhet och använde molngränssnittet i Azure IoT Edge för att distribuera kod till enheten. Nu har du en testenhet som genererar rådata för sin miljö.

Du kan nu fortsätta med någon av de andra självstudierna om du vill lära dig mer om hur Azure IoT Edge kan förvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Filtrera sensordata med hjälp av en Azure-funktion](tutorial-deploy-function.md)
