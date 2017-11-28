---
title: 'IoT DevKit till molnet: ansluta IoT DevKit AZ3166 till Azure IoT Hub | Microsoft Docs'
description: "I den här kursen lär du dig hur du ställer in och ansluta IoT DevKit AZ3166 till Azure IoT Hub så att den kan skicka data till plattformen Azure-molnet."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: xshi
ms.openlocfilehash: deb362796966524ec2db5808623d2f92a1bf44e1
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Ansluta IoT DevKit AZ3166 till Azure IoT-hubb i molnet

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Du kan använda den [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) att utveckla och prototyp Sakernas Internet (IoT) lösningar som utnyttjar Microsoft Azure-tjänster. Det innehåller ett kort Arduino-kompatibel med omfattande kringutrustning sensorer, ett paket med öppen källkod board och en växande [projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Vad du gör
Anslut den [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) till en Azure IoT-hubb som du skapar, samla in data för temperatur- och fuktighetskonsekvens från sensorer och skicka data till IoT-hubben.

Har du en DevKit ännu? [Skaffa en](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du ansluter IoT DevKit till en trådlös åtkomstpunkt och förbereda din utvecklingsmiljö.
* Hur du skapar en IoT-hubb och registrera en enhet för IoT-DevKit MXChip.
* Hur du samlar in sensordata genom att köra ett exempelprogram på MXChip IoT DevKit.
* Hur du skickar dessa sensordata till din IoT-hubb.

## <a name="what-you-need"></a>Vad du behöver

* En MXChip IoT DevKit kort med Micro USB-kabel. [Hämta den nu](https://aka.ms/iot-devkit-purchase).
* En dator som kör Windows 10 eller macOS 10.10 +.
* En aktiv Azure-prenumeration. [Aktivera ett kostnadsfritt 30-dagars utvärderingsversion Microsoft Azure-konto](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Förbered maskinvaran

Koppla samman maskinvaran till din dator.

Den här maskinvaran behöver du:

* DevKit-kort
* Micro-USB-kabel

![Nödvändig maskinvara](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Att ansluta DevKit till datorn:

1. Ansluta USB slutet på datorn.
2. Ansluta Micro USB slutet till DevKit.
3. Grön Indikator för bekräftar anslutningen.

![Maskinvaruanslutningar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Konfigurera Wi-Fi

IoT-projekt förlitar sig på internet-anslutning. Använd följande instruktioner för att konfigurera DevKit att ansluta till Wi-Fi.

### <a name="enter-ap-mode"></a>Ange AP läge

Håll ned knappen B push och släpp återställningsknappen och sedan knappen B. Din DevKit försätts AP läge för att konfigurera Wi-Fi. Skärmen visar nätverksnamn (SSID) för DevKit och IP-adressen för configuration-portalen.

![Återställ knappen knappen B och SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Ansluta till DevKit AP

Nu kan du använda en annan Wi-Fi-aktiverad enhet (dator eller mobiltelefon) för att ansluta till DevKit SSID (markerat med föregående bild). Lämna lösenordet tomt.

![Nätverksinformation och Anslut knapp](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurera Wi-Fi för DevKit

Öppna IP-adressen visas på skärmen DevKit på datorn eller mobiltelefon webbläsare, Välj Wi-Fi-nätverk som du vill DevKit att ansluta till och skriv sedan lösenordet. Välj **Anslut**.

![Lösenord och knappen Anslut](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

När anslutningen lyckas, startas DevKit inom några sekunder. Då visas Wi-Fi-namn och IP-adress på skärmen:

![Wi-Fi-namn och IP-adress](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> IP-adress som visas i bilden kanske inte matchar den IP-adress tilldelas och visas på skärmen DevKit. Detta är normalt, eftersom Wi-Fi använder DHCP för att dynamiskt tilldela IP-adresser.

När du har konfigurerat Wi-Fi, kvar dina autentiseringsuppgifter på enheten för den anslutningen, även om enheten inte är ansluten. Till exempel om du konfigurerar DevKit för Wi-Fi hemma och sedan ta DevKit till office måste du konfigurera om AP-läge (från steg i avsnittet ”Ange AP läget”) för att ansluta DevKit till kontoret Wi-Fi. 

## <a name="start-using-the-devkit"></a>Börja använda DevKit

Standard-appar som körs på DevKit kontrollerar den senaste versionen av den inbyggda programvaran och visar vissa diagnos sensordata för dig.

### <a name="upgrade-to-the-latest-firmware"></a>Uppgradera till den senaste inbyggda programvaran

> [!NOTE] 
> Eftersom v1.1 gör DevKit ST säkert i startprogrammet. Du måste uppgradera inbyggda programvara om du kör under v1.1 för att göra det fungerar förmodligen.

Om du behöver en uppgradering av inbyggd programvara, visar skärmen aktuella och senaste firmware-versioner. Om du vill uppgradera, följer du de [uppgradera inbyggda programvara](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) guide.

![Visning av aktuella och senaste firmware-versioner](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Det här är en enstaka ansträngning. När du börja utveckla på DevKit och överför din app, kommer den senaste inbyggda programvaran med din app.

### <a name="test-various-sensors"></a>Testa olika sensorer

Tryck på knappen B för att testa sensorer. Fortsätt att trycka på och nedtryckt B för att gå igenom varje sensor.

![Visa knappen B och sensor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Nu är det dags att konfigurera utvecklingsmiljön: verktyg och paket som du kan skapa snygg IoT-appar. Du kan välja den Windows eller macOS version enligt ditt operativsystem.

### <a name="windows"></a>Windows

Vi rekommenderar att du kan använda installationspaketet för att förbereda utvecklingsmiljö. Om du stöter på problem, kan du följa den [manuella steg](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) att få det gjort.

#### <a name="download-the-latest-package"></a>Hämta senaste

ZIP-filen som du hämtar innehåller alla nödvändiga verktyg och paket för DevKit utveckling.

> [!div class="button"]
[Ladda ned](https://aka.ms/devkit/prod/installpackage/latest)

ZIP-filen innehåller följande verktyg och paket. Skriptet identifierar och hoppa över dem om du redan har några komponenter vara installerade.

* Node.js och Yarn: Runtime för installationsskriptet och automatiserade åtgärder.
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): plattformsoberoende kommandoradsverktyget upplevelse för att hantera Azure-resurser. MSI-filerna innehåller beroende Python och pip.
* [Visual Studio Code](https://code.visualstudio.com/) (VS-kod): Lightweight redigerare för DevKit utveckling.
* [Visual Studio Code-tillägget för Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): tillägg som gör att Arduino utveckling i Visual Studio-koden.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): verktyg som tillägget för Arduino bygger på.
* DevKit Board paket: Verktyget kedjor, bibliotek och projekt för DevKit.
* ST-Link-verktyget: Viktiga verktyg och drivrutiner.

#### <a name="run-the-installation-script"></a>Kör installationsskriptet för

I Utforskaren, leta upp ZIP-filen och extrahera den. Hitta `install.cmd`, högerklicka och välj **kör som administratör**.

![Utforskaren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Under installationen, kan du se förloppet för varje verktyget eller paketet.

![Installationsförlopp](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> Beroende på din miljö visas ibland fel när du installerar Arduino IDE. I det här fallet kan du försöka [installera IDE Arduino individuellt](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) och kör install.cmd igen. Annars följer du de [manuella steg](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) att installera alla nödvändiga verktyg och paket.

#### <a name="install-drivers"></a>Installera drivrutiner

VS-koden för Arduino tillägg beroende Arduino IDE. Om det här är första gången du installerar Arduino IDE uppmanas du att installera relevanta drivrutiner:

![komma-igång-drivrutin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Installationen tar cirka 10 minuter beroende på hur snabb din. När installationen är klar bör du se Visual Studio Code och Arduino IDE genvägar på ditt skrivbord.

> [!NOTE] 
> Ibland, när du startar VS kod uppmanas du med ett fel att den inte kan hitta Arduino IDE- eller relaterade board paketet. Stäng VS-kod för att lösa det och starta om Arduino IDE. VS kod ska leta upp Arduino IDE-sökvägen på rätt sätt.

### <a name="macos"></a>macOS

Vi rekommenderar att du kan använda en enda klickning installationsproceduren förbereda utvecklingsmiljö. Om du stöter på problem, kan du följa den [manuella steg](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) att få det gjort.

#### <a name="install-homebrew"></a>Installera Homebrew

> [!NOTE] 
> Om du har installerat Homebrew, kan du hoppa över det här steget.

Följ den [Homebrew Installationsinstruktioner](https://docs.brew.sh/Installation.html) att installera den.

#### <a name="download-the-latest-package"></a>Hämta senaste
ZIP-filen som du hämtar innehåller alla nödvändiga verktyg och paket för DevKit utveckling.

> [!div class="button"]
[Ladda ned](https://aka.ms/devkit/prod/installpackage/mac/latest)

ZIP-filen innehåller följande verktyg och paket. Skriptet identifierar och hoppa över dem om du redan har några komponenter vara installerade.

* Node.js och Yarn: Runtime för installationsskriptet och automatiserade åtgärder.
* [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): plattformsoberoende kommandoradsverktyget upplevelse för att hantera Azure-resurser.
* [Visual Studio Code](https://code.visualstudio.com/) (VS-kod): Lightweight redigerare för DevKit utveckling.
* [Visual Studio Code-tillägget för Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): tillägg som gör att Arduino utveckling i Visual Studio-koden.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): verktyg som tillägget för Arduino bygger på.
* DevKit Board paket: Verktyget kedjor, bibliotek och projekt för DevKit.
* ST-Link-verktyget: Viktiga verktyg och drivrutiner.

#### <a name="run-the-installation-script"></a>Kör installationsskriptet för

Leta upp .zip i Finder och extraherar du det:

![macOS finder](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Starta Terminal appen, leta upp den mapp du extrahera .zip-filen och kör:

```bash
./install.sh
```

![installera macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Om du uppfyller Homebrew Behörighetsfel kör `brew doctor` få det åtgärdat. Kontrollera [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) för mer information.

Nu har du alla nödvändiga verktyg och paket som har installerats för macOS.


## <a name="open-the-project-folder"></a>Öppna projektmappen

### <a name="start-vs-code"></a>Starta VS-kod

Kontrollera att din DevKit inte är anslutet. Starta VS kod först och ansluta DevKit till din dator. VS koden automatiskt hittar DevKit och öppnas startsidan:

![Introduktionssida](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> Ibland, när du startar VS kod uppmanas du med ett fel att den inte kan hitta Arduino IDE- eller relaterade board paketet. Stäng VS-kod och starta om Arduino IDE. VS kod ska leta upp Arduino IDE-sökvägen på rätt sätt.


### <a name="open-the-arduino-examples-folder"></a>Öppna mappen Arduino exempel

På den **Arduino exempel** fliken, bläddra till **exempel MXCHIP AZ3166** > **AzureIoT**, och välj **GetStarted**.

![Fliken Arduino exempel](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Om du råkar stänga fönstret kan du öppna det igen. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna paletten kommando skriver **Arduino**, och sedan söka efter och välj **Arduino: exempel**.

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

I fönstret lösning kör uppgiften `Ctrl+P` (macOS: `Cmd+P`) genom att ange `task cloud-provision`.

I terminalen VS kod hjälper dig att etablera nödvändiga Azure-tjänster i en interaktiv kommandorad:

![Interaktiva kommandoraden](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Skapa och ladda upp Arduino ritningarna

### <a name="windows"></a>Windows

1. Använd `Ctrl+P` att köra `task device-upload`.
2. Terminalen uppmanas du att ange konfigurationsläge. Om du vill göra det, hålla ned A, och sedan push och släpp återställningsknappen. Skärmen visar DevKit id och ”Configuration”.

Detta är att ange anslutningssträngen som hämtas från `task cloud-provision` steg.

VS kod startas verifierar och laddar upp Arduino ritningarna:

![Verifieringen och överföring av Arduino ritningarna](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit startar om och börjar köras koden.

> [!NOTE] 
> Ibland kan du få fel ”fel: AZ3166: Okänd paketet”. Detta beror på kortet paketindexet uppdateras inte. Kontrollera detta [vanliga frågor och svar steg](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) att lösa den.

### <a name="macos"></a>macOS

1. Placera DevKit i konfigurationsläge: Håll ned knappen A, och sedan push och versionen återställningsknappen. Skärmen visar ”Configuration”.
2. Använd `Cmd+P` att köra `task device-upload`.

Detta är att ange anslutningssträngen som hämtas från `task cloud-provision` steg.

VS kod startas verifierar och laddar upp Arduino ritningarna:

![enheten-överföringen](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit startar om och börjar köras koden.

> [!NOTE] 
> Ibland kan du få fel ”fel: AZ3166: Okänd paketet”. Detta beror på kortet paketindexet uppdateras inte. Kontrollera detta [vanliga frågor och svar steg](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) att lösa den.


## <a name="test-the-project"></a>Testa projektet

I VS-kod, följa dessa steg för att öppna och Ställ in seriella övervakaren:

1. Klicka på den `COM[X]` word i statusfältet för att ange rätt COM-port med `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Klicka på power plug ikon i statusfältet för att öppna Serial övervakaren: ![serial-Övervakaren](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Klicka på det tal som motsvarar den Baud-hastighet i statusfältet, och ange `115200`: ![baud-hastighet](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

Exempelprogrammet körts när du ser följande resultat:

* Seriell övervakaren visar samma information som innehållet i skärmbilden nedan.
* Indikator på MXChip IoT DevKit blinkar.

![Slutversionen i VS-kod](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, kan du hitta [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Du kan också ge feedback genom att lämna en kommentar i den här sidan.

## <a name="next-steps"></a>Nästa steg

En MXChip IoT DevKit har anslutit till din IoT-hubb och du har skickat den avbildade sensordata till din IoT-hubb.

Komma igång med Azure IoT Hub och om du vill utforska andra IoT-scenarier finns:

- [Hantera meddelanden mellan enheter och molnet med iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Spara meddelanden från IoT Hub i datalagring för Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Använd Power BI för att visualisera sensordata i realtid från Azure IoT-hubb](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Använda Web Apps visualisera sensordata i realtid från Azure IoT-hubb](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Väder prognos använder sensordata från IoT-hubb i Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Enhetshantering med IoT Hub-utforskaren](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Fjärrövervakning och aviseringar med Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
