---
title: Konfigurera miljö-Machine Learning på Azure IoT Edge | Microsoft Docs
description: Förbered din miljö för utveckling och distribution av moduler för maskin inlärning i gränsen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1db94e683a0dfb3b60b12bc5ac205c766d405d0a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299829"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Självstudier: Konfigurera en miljö för maskin inlärning på IoT Edge

> [!NOTE]
> Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt rekommenderar vi att du börjar med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa möjliga resultat.

Den här artikeln från slut punkt till slut punkt Azure Machine Learning i IoT Edge själv studie kursen hjälper dig att förbereda din miljö för utveckling och distribution. Börja med att konfigurera en utvecklings dator med alla verktyg du behöver. Skapa sedan nödvändiga moln resurser i Azure.

## <a name="set-up-a-development-machine"></a>Konfigurera en utvecklings dator

Det här steget utförs vanligt vis av en molnbaserad utvecklare. En del program vara kan också vara till hjälp för en data expert.

Under den här artikeln utför vi olika uppgifter för utvecklare, inklusive kodning, kompilering, konfiguration och distribution av IoT Edge moduler och IoT-enheter. För enkel användning skapade vi ett PowerShell-skript som skapar en virtuell Azure-dator med många av de krav som redan har kon figurer ATS. Den virtuella dator som vi skapar måste kunna hantera [kapslad virtualisering](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), vilket innebär att vi väljer en [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1) dator storlek.

Den virtuella utvecklings datorn kommer att konfigureras med:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop för Windows](https://www.docker.com/products/docker-desktop)
* [Git för Windows](https://gitforwindows.org/)
* [Git Credential Manager för Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code-tillägg](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Den virtuella Developer-datorn är inte absolut nödvändig – alla utvecklingsverktyg kan köras på en lokal dator. Vi rekommenderar dock starkt att du använder den virtuella datorn för att säkerställa ett fält som spelas upp.

Det tar cirka 30 minuter att skapa och konfigurera den virtuella datorn.

### <a name="get-the-script"></a>Hämta skriptet

Klona eller hämta PowerShell-skriptet från [Machine Learning och IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) exempel lagrings plats.

### <a name="create-an-azure-virtual-machine"></a>Skapa en virtuell Azure-dator

DevVM-katalogen innehåller de filer som behövs för att skapa en virtuell Azure-dator för att kunna slutföra den här kursen.

1. Öppna PowerShell som administratör och navigera till den katalog där du laddade ned koden. Vi kommer att referera till rot katalogen för din källa som `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Kör följande kommando för att tillåta körning av skript. Välj **Ja till alla** när du uppmanas till det.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Kör Create-AzureDevVM. ps1 från den här katalogen.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * När du uppmanas anger du följande information:
      * **ID för Azure-prenumeration**: Ditt prenumerations-ID, som du hittar i Azure Portal
      * **Resurs grupps namn**: Namnet på en ny eller befintlig resurs grupp i Azure
      * **Plats**: Välj en Azure-plats där den virtuella datorn ska skapas. Till exempel westus2 eller europanorra. Mer information finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Ange ett minnes minnes namn för det administratörs konto som du vill skapa och använda på den virtuella datorn.
      * **AdminPassword**: Ange ett lösen ord för administratörs kontot på den virtuella datorn.

    * Om du inte har Azure PowerShell installerat, kommer skriptet att installera [Azure PowerShell AZ-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Du uppmanas att logga in i Azure.

    * Skriptet bekräftar informationen för att skapa den virtuella datorn. Tryck `y` på `Enter` eller för att fortsätta.

Skriptet körs i flera minuter eftersom det utför följande steg:

* Skapa resurs gruppen om den inte finns
* Distribuera den virtuella datorn
* Aktivera Hyper-V på den virtuella datorn
* Installera program varu behov för utveckling och klona exempel lagrings platsen
* Starta om den virtuella datorn
* Skapa en RDP-fil på Skriv bordet för att ansluta till den virtuella datorn

### <a name="set-auto-shutdown-schedule"></a>Ange schema för automatisk avstängning

För att hjälpa dig att minska kostnaderna har den virtuella datorn skapats med ett schema för automatisk avstängning som är inställt på 1900 PST. Du kan behöva uppdatera den här tids inställningen beroende på din plats och ditt schema. Så här uppdaterar du schemat för avstängning:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Navigera till den virtuella datorn i resurs gruppen som du angav i föregående avsnitt.

3. Välj **Automatisk avstängning** i sido navigerings fönstret.

4. Ange en ny avslutnings tid i **schemalagd avstängning** eller ändra **tids zonen** och klicka sedan på **Spara**.

### <a name="connect-and-configure-development-machine"></a>Anslut och konfigurera utvecklings datorn

Nu när vi har skapat en virtuell dator måste vi slutföra installationen av den program vara som krävs för att slutföra självstudien.

#### <a name="start-a-remote-desktop-session"></a>Starta en fjärrskrivbordssession

1. Skriptet för att skapa virtuella datorer skapade en RDP-fil på Skriv bordet.

2. Dubbelklicka på filen med namnet  **\<Azure VM name\>. RDP**.

3. Du kommer att visas i en dialog ruta som säger att den fjärranslutna anslutningens utgivare är okänd. Klicka på kryss rutan **fråga mig inte igen om anslutningar till den här datorn** och välj sedan **Anslut**.

4. När du uppmanas till det anger du den AdminPassword som du använde när du körde skriptet för att konfigurera den virtuella datorn och klickar på **OK**.

5. Du uppmanas att godkänna certifikatet för den virtuella datorn. Välj **fråga mig inte igen för anslutningar till den här datorn** och välj **Ja**.

#### <a name="install-visual-studio-code-extensions"></a>Installera Visual Studio Code-tillägg

Nu när du har anslutit till utvecklings datorn lägger du till några användbara tillägg i Visual Studio Code för att förenkla utvecklings upplevelsen.

1. I ett PowerShell-fönster navigerar du till **C\\:\\\\source IoTEdgeAndMlSample DevVM**.

2. Tillåt att skript körs på den virtuella datorn genom att skriva.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Kör skriptet.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. Skriptet kommer att köras under några minuter vid installation av VS Code-tillägg:

    * Azure IoT-verktyg
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Konfigurera IoT Hub och lagring

De här stegen utförs vanligt vis av en molnbaserad utvecklare.

Azure IoT Hub är hjärtat i alla IoT-program. Den hanterar säker kommunikation mellan IoT-enheter och molnet. Det är den huvudsakliga koordinations punkten för driften av IoT Edge Machine Learning-lösning.

* IoT Hub använder vägar för att dirigera inkommande data från IoT-enheter till andra underordnade tjänster. Vi drar nytta av IoT Hub vägar för att skicka enhets data till Azure Storage där det kan användas av Azure Machine Learning för att träna vår återstående användbara livs cykel (RUL) klassificerare.

* Senare i självstudien kommer vi att använda IoT Hub för att konfigurera och hantera vår Azure IoT Edge-enhet.

I det här avsnittet ska du använda ett skript för att skapa en Azure IoT-hubb och ett Azure Storage-konto. Sedan konfigurerar du en väg som vidarebefordrar data som tas emot av hubben till en Azure Storage Blob behållare med hjälp av Azure Portal. De här stegen tar cirka 10 minuter att slutföra.

### <a name="create-cloud-resources"></a>Skapa moln resurser

1. Öppna ett PowerShell-fönster på din utvecklings dator.

1. Ändra till IoTHub-katalogen.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Kör skriptet för att skapa. Använd samma värden för prenumerations-ID, plats och resurs grupp som du gjorde när du skapade den virtuella utvecklings datorn.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Du uppmanas att logga in i Azure.
    * Skriptet bekräftar informationen för att skapa hubben och lagrings kontot. Tryck `y` på `Enter` eller för att fortsätta.

Det tar ungefär två minuter att köra skriptet. När du är klar matas skriptet ut namnet på hubben och lagrings kontot.

### <a name="review-route-to-storage-in-iot-hub"></a>Granska routning till lagring i IoT Hub

Som en del av att skapa IoT-hubben skapade även skriptet som vi körde i föregående avsnitt en anpassad slut punkt och en väg. IoT Hub vägar består av ett frågeuttryck och en slut punkt. Om ett meddelande matchar uttrycket skickas data längs vägen till den associerade slut punkten. Slut punkter kan vara Event Hubs, Service Bus köer och ämnen. I det här fallet är slut punkten en BLOB-behållare i ett lagrings konto. Vi använder Azure Portal för att granska den väg som skapats av vårt skript.

1. Öppna [Azure-portalen](https://portal.azure.com).

1. Välj alla tjänster i den vänstra navigerings rutan, skriv IoT i rutan Sök och välj **IoT Hub**.

1. Välj IoT Hub som skapades i föregående steg.

1. I IoT Hub sido navigering väljer du **meddelanderoutning**.

1. Sidan meddelanderoutning har två flikar, **vägar** och **anpassade slut punkter**. Välj fliken **anpassade slut punkter** .

1. Under **Blob Storage**väljer du **turbofanDeviceStorage**.

1. Observera att den här slut punkten pekar på en BLOB-behållare med namnet **devicedata** i det lagrings konto som skapades i det sista steget, som heter **\<iotedgeandml unikt suffix\>** .

1. Observera också att **BLOB-filens format** har ändrats från standardformat till att i stället placera partitionen som det sista elementet i namnet. Vi hittar det här formatet är enklare för de fil åtgärder som vi ska göra med Azure Notebooks senare i självstudien.

1. Stäng bladet slut punkts information om du vill gå tillbaka **till sidan meddelanderoutning** .

1. Välj fliken **vägar** .

1. Välj den väg som heter **turbofanDeviceDataToStorage**.

1. Observera att vägens slut punkt är den anpassade **turbofanDeviceStorage** -slutpunkten.

1. Titta på cirkulations frågan, som är inställd på **Sant**. Det innebär att alla meddelanden för telemetri kommer att matcha den här vägen och därmed skickas alla meddelanden till **turbofanDeviceStorage** -slutpunkten.

1. Stäng flödes informationen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi skapat en IoT Hub och konfigurerat en väg till ett Azure Storage-konto. I nästa artikel kommer vi att skicka data från en uppsättning simulerade enheter genom IoT Hub till lagrings kontot. Senare i självstudien, när vi har konfigurerat vår IoT Edge enhet och moduler, kommer vi att gå tillbaka till vägar och se lite mer på cirkulations frågan.

Mer information om de steg som beskrivs i den här delen av Machine Learning i IoT Edge själv studie kursen finns i:

* [Grunderna i Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Konfigurera meddelanderoutning med IoT Hub](../iot-hub/tutorial-routing.md)
* [Skapa en IoT-hubb med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md)

Fortsätt till nästa artikel för att skapa en simulerad enhet som ska övervakas.

> [!div class="nextstepaction"]
> [Generera enhets data](tutorial-machine-learning-edge-03-generate-data.md)
