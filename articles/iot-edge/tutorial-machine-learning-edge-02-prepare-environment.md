---
title: Konfigurera en miljö - Machine Learning på Azure IoT Edge | Microsoft Docs
description: Förbered din miljö för utveckling och distribution av moduler för machine learning till gränsen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f36b706213639c5bea383ec20ffc7f6d7ed01021
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155704"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Självstudier: Konfigurera en miljö för machine learning på IoT Edge

> [!NOTE]
> Den här artikeln ingår i en serie med en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har hamnat på den här artikeln direkt, rekommenderar vi att du börja med den [först artikel](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

Den här artikeln från slutpunkt till slutpunkt Azure Machine Learning på IoT Edge-självstudien hjälper dig att förbereda din miljö för utveckling och distribution. Först ställer du in en utvecklingsdator med de verktyg du behöver. Skapa sedan de nödvändiga molnresurser i Azure.

## <a name="set-up-a-development-machine"></a>Konfigurera en utvecklingsdator

Det här steget utförs normalt av en utvecklare i molnet. Några av programvaran kan också vara till hjälp för inom data Science.

Under loppet av den här artikeln utföra vi olika uppgifter för utvecklare, inklusive kodning, kompilera, konfigurera och distribuera IoT Edge-moduler och IoT-enheter. För enkelhets skull skapade vi ett PowerShell-skript som skapar en Azure virtuell dator med många av kraven som redan har konfigurerats. Den virtuella datorn som vi skapar behöver för att kunna hantera [kapslad virtualisering](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), vilket är anledningen till att vi valde en DS8V3 storlek.

Utvecklingen VM ska ställas in med:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker-skrivbord för Windows](https://www.docker.com/products/docker-desktop)
* [Git för Windows](https://gitforwindows.org/)
* [Git Credential Manager för Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code-tilläggen](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Utvecklaren VM är inte absolut nödvändigt – alla utvecklingsverktyg kan köras på en lokal dator. Vi rekommenderar dock med den virtuella datorn för att se till att möjligheterna.

Det tar cirka 30 minuter att skapa och konfigurera den virtuella datorn.

### <a name="get-the-script"></a>Hämta skriptet

Klona eller ladda ned PowerShell-skriptet från den [Maskininlärning och IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) exempellagringsplatsen.

### <a name="create-an-azure-virtual-machine"></a>Skapa en Azure-dator

Katalogen DevVM innehåller de filer som behövs för att skapa en Azure virtuell dator som är lämpliga för den här kursen.

1. Öppna Powershell som administratör och navigera till katalogen där du hämtade koden. Vi kommer att referera till rotkatalogen för din källa som `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Kör följande kommando för att tillåta körning av skript. Välj **Ja till alla** när du tillfrågas.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Kör skapa AzureDevVM.ps1 från den här katalogen.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * När du uppmanas, anger du följande information:
      * **Azure-prenumerations-ID**: Ditt prenumerations-ID som finns i Azure portal
      * **Namn på resursgrupp**: Namnet på en ny eller befintlig resursgrupp i Azure
      * **Plats**: Välj en Azure-plats där den virtuella datorn kommer att skapas. Till exempel usavästra2 eller europanorra. Mer information finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Ange en egen namn för det administratörskonto som du vill skapa och använda på den virtuella datorn.
      * **AdminPassword**: Ange ett lösenord för administratörskonto på den virtuella datorn.

    * Om du inte har Azure PowerShell installerad skriptet installerar [Az för Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Du uppmanas att logga in i Azure.

    * Skriptet bekräftar information för att skapa den virtuella datorn. Tryck på `y` eller `Enter` att fortsätta.

Skriptet körs i flera minuter som den utför följande steg:

* Skapa resursgruppen om det inte finns
* Distribuera den virtuella datorn
* Aktivera Hyper-V på den virtuella datorn
* Installera programvara behöver för utveckling och klona exempellagringsplatsen
* Starta om den virtuella datorn
* Skapa en RDP-fil på skrivbordet för att ansluta till den virtuella datorn

### <a name="set-auto-shutdown-schedule"></a>Automatisk avstängning schema

Om du vill minska kostnaden, har den virtuella datorn skapats med ett schema för automatisk avstängning som har angetts till 1900 PST. Du kan behöva uppdatera den här tidpunkten beroende på din plats och schema. Så här uppdaterar schemat avstängning:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Navigera till den virtuella datorn i resursgruppen som du angav i föregående avsnitt.

3. Välj **automatisk avstängning** på Navigatören sida.

4. Ange en ny avstängningstid i **schemalagd avstängning** eller ändra den **tidszon** klickar **spara**.

### <a name="connect-and-configure-development-machine"></a>Anslut och konfigurera utvecklingsdator

Nu när vi har skapat en virtuell dator som vi behöver för att slutföra installationen av programvara som krävs för att slutföra självstudien.

#### <a name="start-a-remote-desktop-session"></a>Starta en fjärrskrivbordssession

1. Skriptet som skapar virtuella datorn skapas en RDP-fil på skrivbordet.

2. Dubbelklicka på filen med namnet  **\<Azure VM-namnet\>.rdp**.

3. Du kommer att visas en dialogruta som talar om att utgivaren av fjärranslutningen är okänd. Klicka på den **fråga inte igen för anslutningar till den här datorn** kryssrutan därefter **Connect**.

4. När du uppmanas, anger AdminPassword som du använde när du kör skriptet för att konfigurera den virtuella datorn och klicka på **OK**.

5. Du uppmanas att godkänna certifikatet för den virtuella datorn. Välj **fråga inte igen för anslutningar till den här datorn** och välj **Ja**.

#### <a name="install-visual-studio-code-extensions"></a>Installera Visual Studio Code-tillägg

Nu när du har anslutit till utvecklingsdator kan du lägga till vissa användbara tillägg i Visual Studio Code för att göra utvecklingen enklare upplevelse.

1. I ett PowerShell-fönster, går du till **C:\\källa\\IoTEdgeAndMlSample\\DevVM**.

2. Tillåt skript som ska köras på den virtuella datorn genom att skriva.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Kör skriptet.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. Skriptet körs under ett par minuter installera VS code-tillägg:

    * Azure IoT-verktyg
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Konfigurera IoT Hub och lagring

De här stegen är vanligtvis utförs av en utvecklare i molnet.

Azure IoT Hub är hjärtat i alla IoT-program. Den hanterar säker kommunikation mellan IoT-enheter och molnet. Det är det viktigaste samordning låga för driften av IoT Edge-machine learning-lösning.

* IoT Hub använder vägar för att dirigera inkommande data från IoT-enheter till andra nedströms tjänster. Vi kan dra nytta av IoT Hub vägar för att skicka enhetens data till Azure Storage där den kan användas av Azure Machine Learning för att träna våra återstående livslängd (RUL)-klassificerare.

* Senare i självstudien använder vi IoT Hub att konfigurera och hantera vår Azure IoT Edge-enhet.

I det här avsnittet ska använda du ett skript för att skapa Azure IoT hub och Azure Storage-kontot. Sedan kan konfigurera du en väg som vidarebefordrar data som tas emot av hubben till en Azure Storage Blob-behållare med Azure portal. De här stegen tar cirka 10 minuter för att slutföra.

### <a name="create-cloud-resources"></a>Skapa resurser i molnet

1. Öppna ett PowerShell-fönster på din utvecklingsdator.

1. Ändra till katalogen IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Kör Skapandeskriptet. Använd samma värden för prenumerations-ID, plats och resursgrupp grupp som du gjorde när du skapar en virtuell utvecklingsdator.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Du uppmanas att logga in i Azure.
    * Skriptet bekräftar information för att skapa ditt NAV- och Storage-konto. Tryck på `y` eller `Enter` att fortsätta.

Skriptet tar ungefär två minuter för att köra. När du är klar skriptet matar ut namnet på hubben och storage-konto.

### <a name="review-route-to-storage-in-iot-hub"></a>Granska väg till lagringen i IoT Hub

Som en del av IoT-hubb skapas, det skript som vi körde i föregående avsnitt också skapat en anpassad slutpunkt och en väg. IoT Hub vägar består av ett frågeuttryck och en slutpunkt. Om ett meddelande matchar uttrycket, skickas data längs vägen till den associerade slutpunkten. Slutpunkter kan vara Event Hubs, Service Bus-köer och ämnen. I det här fallet är slutpunkten en Blob-behållare i ett lagringskonto. Vi använder Azure-portalen för att granska den väg som skapats av våra skript.

1. Öppna [Azure-portalen](http://portal.azure.com).

1. Välj alla tjänster från den vänstra navigeringen skriver IoT i sökrutan och väljer **IoT Hub**.

1. Välj IoT-hubben har skapats i föregående steg.

1. I IoT Hub sida Navigatören, väljer **meddelanderoutning**.

1. Meddelandet routning sidan har två flikar **vägar** och **anpassade slutpunkter**. Välj den **anpassade slutpunkter** fliken.

1. Under **Blob-lagring**väljer **turbofanDeviceStorage**.

1. Observera att den här slutpunkten som pekar på en blobbehållare kallas **devicedata** i storage-konto som skapats i det sista steget, som heter **iotedgeandml\<unikt suffix\>** .

1. Tänk också på den **format på blobfilens namn** har ändrats från standardformatet ska placeras i stället partitionen som det sista elementet i namnet. Vi tycker att det här formatet är bekvämare för filåtgärder som vi kommer att göra med Azure-anteckningsböcker senare under kursen.

1. Stäng slutpunktsbladet för att återgå till den **meddelanderoutning** sidan.

1. Välj den **vägar** fliken.

1. Välj den väg med namnet **turbofanDeviceDataToStorage**.

1. Observera att flödets slutpunkten är den **turbofanDeviceStorage** anpassad slutpunkt.

1. Titta på routning frågan, vilket är inställd på **SANT**. Det innebär att alla meddelanden från enheten telemetri kommer att matcha den här vägen och därför alla meddelanden skickas till den **turbofanDeviceStorage** slutpunkt.

1. Stäng flödesinformation.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi skapas en IoT-hubb och konfigureras en väg till ett Azure Storage-konto. I nästa artikel skickar data från en uppsättning simulerade enheter via IoT Hub till lagringskontot. Senare i självstudien när vi har konfigurerat vår IoT Edge-enhet och moduler vi gå tillbaka till vägar och titta lite närmare på routning frågan.

Mer information om de steg som beskrivs i den här delen av Machine Learning på IoT Edge-självstudie finns:

* [Grunderna i Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Konfigurera meddelanderoutning med IoT Hub](../iot-hub/tutorial-routing.md)
* [Skapa en IoT hub med Azure portal](../iot-hub/iot-hub-create-through-portal.md)

Fortsätt till nästa artikel om du skapar en simulerad enhet att övervaka.

> [!div class="nextstepaction"]
> [Generera enhetsdata](tutorial-machine-learning-edge-03-generate-data.md)
