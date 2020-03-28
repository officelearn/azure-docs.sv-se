---
title: 'Självstudiekurs: Konfigurera miljö – Maskininlärning på Azure IoT Edge'
description: 'Självstudiekurs: Förbered din miljö för utveckling och distribution av moduler för maskininlärning vid gränsen.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c776d4d6c3973e7c222c9c9adf3e5105f6c84f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296818"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Självstudiekurs: Konfigurera en miljö för maskininlärning på IoT Edge

> [!NOTE]
> Den här artikeln är en del av en serie för en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt, uppmuntrar vi dig att börja med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

Den här artikeln hjälper dig att förbereda din miljö för utveckling och distribution. Ställ först in en utvecklingsmaskin med alla verktyg du behöver. Skapa sedan nödvändiga molnresurser i Azure.

## <a name="set-up-the-development-vm"></a>Ställ in utvecklings-VM

Det här steget utförs vanligtvis av en molnutvecklare. En del av programvaran kan också vara till hjälp för en datavetare.

Vi har skapat ett PowerShell-skript som skapar en virtuell Azure-dator med många av de förutsättningar som redan har konfigurerats. Den virtuella datorn som vi skapar måste kunna hantera [kapslad virtualisering,](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)vilket är anledningen till att vi valde en [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) maskinstorlek.

Utvecklings-VM kommer att ställas in med:

* Windows 10
* [Chocolaty](https://chocolatey.org/)
* [Docker Desktop för Windows](https://www.docker.com/products/docker-desktop)
* [Git för Windows](https://gitforwindows.org/)
* [Git-autentiseringshanteraren för Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3 (Svenska)](https://www.python.org/)
* [Visual Studio-kod](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS-kodtillägg](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [Powershell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Utvecklaren VM är inte absolut nödvändigt - alla utvecklingsverktyg kan köras på en lokal dator. Vi rekommenderar dock starkt att du använder den virtuella datorn för att säkerställa lika villkor.

Det tar cirka 30 minuter att skapa och konfigurera den virtuella datorn.

1. Klona eller hämta [exempeldatabasen Machine Learning och IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) till den lokala datorn.

1. Öppna PowerShell som administratör och navigera till katalogen **\IoTEdgeAndMlSample\DevVM** som finns under rotkatalogen där du hämtade koden. Vi kommer att hänvisa till rotkatalogen för din källa som `srcdir`.

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   DevVM-katalogen innehåller de filer som behövs för att skapa en virtuell Azure-dator som är lämplig för att slutföra den här självstudien.

1. Kör följande kommando för att tillåta körning av skript. Välj **Ja till alla** när du uppmanas att göra det.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Kör Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Ange följande information när du uppmanas:

    * **Azure-prenumerations-ID:** Ditt prenumerations-ID, som finns i [Azure-prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i portalen.
    * **Namn på resursgrupp:** Namnet på en ny eller befintlig resursgrupp i Azure.
    * **Plats**: Välj en Azure-plats där den virtuella datorn ska skapas. Till exempel "Västra US 2" eller "Nordeuropa". Mer information finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Användarnamn**: Ange ett minnesvärt namn för administratörskontot för den virtuella datorn.
    * **Lösenord**: Ange ett lösenord för administratörskontot för den virtuella datorn.

   Skriptet körs i flera minuter när följande körs:

    1. Installerar [Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Uppmanar dig att logga in på Azure.
    1. Bekräftar informationen för att skapa den virtuella datorn. Tryck **på y** eller **Retur** för att fortsätta.
    1. Skapar resursgruppen om den inte finns.
    1. Distribuerar den virtuella datorn.
    1. Aktiverar Hyper-V på den virtuella datorn.
    1. Installerar programvara som behöver utvecklas och klona exempeldatabasen.
    1. Startar om den virtuella datorn.
    1. Skapar en RDP-fil på skrivbordet för anslutning till den virtuella datorn.

   Om du uppmanas att ange namnet på den virtuella datorn för att starta om den kan du kopiera dess namn från skriptutdata. Utdata visar också sökvägen till RDP-filen för anslutning till den virtuella datorn.

### <a name="set-auto-shutdown-schedule"></a>Ange schema för automatisk avstängning

För att hjälpa dig att minska kostnaderna har utvecklingsdassen skapats med ett automatiskt avstängningsschema som är inställt på 1900 PST. Du kan behöva uppdatera den här inställningen beroende på var du befinner dig och schemat. Så här uppdaterar du avstängningsschemat:

1. I Azure-portalen navigerar du till den virtuella datorn som skriptet skapade.

1. Välj **Automatisk avstängning**under **Åtgärder**på menyn till vänster.

1. Justera zonen **Schemalagd avstängning** och **Tidszon** efter behov och välj **Spara**.

## <a name="connect-to-the-development-vm"></a>Anslut till utvecklings-VM

Nu när vi har skapat en virtuell dator måste vi slutföra installationen av den programvara som behövs för att slutföra handledningen.

1. Dubbelklicka på rdp-filen som skriptet skapade på skrivbordet.

1. Du kommer att presenteras med en dialogruta som säger att utgivaren av fjärranslutningen är okänd. Detta är acceptabelt, så välj **Anslut**.

1. Ange administratörslösenordet som du angav för att skapa den virtuella datorn och klicka på **OK**.

1. Du uppmanas att acceptera certifikatet för den virtuella datorn. Välj **Ja**.

## <a name="install-visual-studio-code-extensions"></a>Installera tillägg för Visual Studio-kod

Nu när du har anslutit till utvecklingsmaskinen lägger du till några användbara tillägg i Visual Studio Code för att underlätta utvecklingsupplevelsen.

1. Anslut till utvecklingsdassken, öppna ett PowerShell-fönster och navigera till katalogen **C:\source\IoTEdgeAndMlSample\DevVM.** Den här katalogen skapades av skriptet som skapade den virtuella datorn.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Kör följande kommando för att tillåta körning av skript. Välj **Ja till alla** när du uppmanas att göra det.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Kör skriptet för Visual Studio-kodtillägg.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Skriptet körs i några minuter med att installera VS-kodtillägg:

    * Azure IoT-verktyg
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Konfigurera IoT Hub och lagring

Dessa steg utförs vanligtvis av en molnutvecklare.

Azure IoT Hub är hjärtat i alla IoT-program eftersom det hanterar säker kommunikation mellan IoT-enheter och molnet. Det är den viktigaste samordningspunkten för driften av IoT Edge maskininlärningslösning.

* IoT Hub använder vägar för att dirigera inkommande data från IoT-enheter till andra underordnade tjänster. Vi kommer att dra nytta av IoT Hub-vägar för att skicka enhetsdata till Azure Storage. I Azure Storage förbrukas enhetsdata av Azure Machine Learning för att träna vår återstående RUL-klassificerare (Useful Life).

* Senare i självstudiekursen använder vi IoT Hub för att konfigurera och hantera vår Azure IoT Edge-enhet.

I det här avsnittet använder du ett skript för att skapa en Azure IoT-hubb och ett Azure Storage-konto. I Azure-portalen konfigurerar du sedan en väg som vidarebefordrar data som tas emot av navet till en Azure Storage-behållare. Dessa steg tar cirka 10 minuter att slutföra.

1. Anslut till den virtuella utvecklingsdatorn, öppna ett PowerShell-fönster och navigera till **IoTHub-katalogen.**

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Kör skapandeskriptet. Använd samma värden för prenumerations-ID, plats och resursgrupp som du gjorde när du skapade utvecklingsdass.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Du uppmanas att logga in i Azure.
    * Skriptet bekräftar informationen för att skapa ditt Hub- och Storage-konto. Tryck **på y** eller **Retur** för att fortsätta.

Det tar cirka två minuter att köra skriptet. När du är klar matar skriptet ut namnet på IoT-hubben och lagringskontot.

## <a name="review-route-to-storage-in-iot-hub"></a>Granska väg till lagring i IoT Hub

Som en del av skapandet av IoT-hubben skapade skriptet som vi körde i föregående avsnitt också en anpassad slutpunkt och en väg. IoT Hub-vägar består av ett frågeuttryck och en slutpunkt. Om ett meddelande matchar uttrycket skickas data längs vägen till den associerade slutpunkten. Slutpunkter kan vara eventhubbar, servicebussköer och ämnen. I det här fallet är slutpunkten en blob-behållare i ett lagringskonto. Låt oss använda Azure-portalen för att granska den väg som skapas av vårt skript.

1. Öppna [Azure-portalen](https://portal.azure.com) och gå till resursgruppen som du använder för den här självstudien.

1. I listan över resurser väljer du den IoT Hub som skriptet skapade. Det kommer att ha ett namn `IotEdgeAndMlHub-jrujej6de6i7w`som slutar med slumpmässiga tecken som .

1. Välj **Meddelanderoutning**under **Meddelanden**på menyn till den vänstra rutan .

1. Välj fliken **Anpassade slutpunkter** på sidan **Meddelanderoutning.**

1. Expandera avsnittet **Lagring:**

   ![Kontrollera turbofanDeviceStorage är i den anpassade slutpunkter listan](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Vi ser **turbofanDeviceStorage** är i den anpassade slutpunkter listan. Observera följande egenskaper för den här slutpunkten:

   * Den pekar på den blob-lagringsbehållare som du skapade med namnet `devicedata` som anges av **behållarnamnet**.
   * Dess **filnamnsformat** har partition som det sista elementet i namnet. Vi tycker att det här formatet är bekvämare för de filåtgärder vi kommer att göra med Azure Notebooks senare i självstudien.
   * Dess **status** bör vara frisk.

1. Välj fliken **Vägar**.

1. Välj rutten **turbofanDeviceDataToStorage**.

1. På sidan **Ruttinformation** observerar du att ruttens slutpunkt är **ändpunkten turbofanDeviceStorage.**

   ![Granska information om rutten turbofanDeviceDataToStorage](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Titta på **routningsfrågan**, som är inställd på **true**. Den här inställningen innebär att alla enhetstelemetrimeddelanden matchar den här vägen. och därför kommer alla meddelanden att skickas till **turbofanDeviceStorage** slutpunkten.

1. Eftersom inga ändringar gjordes, bara stänga den här sidan.

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade vi en IoT Hub och konfigurerade en väg till ett Azure Storage-konto. Därefter skickar vi data från en uppsättning simulerade enheter via IoT Hub till lagringskontot. Senare i handledningen, efter att vi har konfigurerat vår IoT Edge-enhet och moduler, kommer vi att se över rutter och titta lite mer på routningsfrågan.

Mer information om stegen i den här delen av machine learning på IoT Edge-självstudiekursen finns i:

* [Grunderna i Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Konfigurera meddelandedirigering med IoT Hub](../iot-hub/tutorial-routing.md)
* [Skapa en IoT-hubb med Azure-portalen](../iot-hub/iot-hub-create-through-portal.md)

Fortsätt till nästa artikel för att skapa en simulerad enhet som ska övervakas.

> [!div class="nextstepaction"]
> [Generera enhetsdata](tutorial-machine-learning-edge-03-generate-data.md)
