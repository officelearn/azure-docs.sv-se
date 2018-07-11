---
title: IoT DevKit till molnet – ansluta IoT DevKit AZ3166 till Azure IoT Hub | Microsoft Docs
description: I de här självstudierna lär du dig hur du konfigurerar och ansluter IoT DevKit AZ3166 på Azure IoT Hub så att den kan skicka data till Azure-molnplattformen.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: cf9ee5339c53eb4f9c74f6b5f251a7963555d676
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928757"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Ansluta IoT DevKit AZ3166 till Azure IoT Hub i molnet

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Du kan använda den [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) att utveckla och prototyp Internet of Things (IoT) lösningar som utnyttjar Microsoft Azure-tjänster. Den innehåller en Arduino-kompatibelt tavla med omfattande kringutrustning och sensorer, ett paket med öppen källkod tavla och en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Vad du gör
Anslut den [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) till Azure IoT hub som du skapar, samla in temperatur och fuktighet data från sensorer och skicka data till IoT hub.

Har inte en DevKit ännu? Försök [DevKit simulator](https://azure-samples.github.io/iot-devkit-web-simulator/) eller [skaffa ett](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du ansluter IoT DevKit till en trådlös åtkomstpunkt och Förbered din utvecklingsmiljö.
* Hur du skapar en IoT-hubb och registrera en enhet för MXChip IoT DevKit.
* Så här att samla in sensordata genom att köra ett exempelprogram på MXChip IoT DevKit.
* Så här skickar sensordata till din IoT hub.

## <a name="what-you-need"></a>Vad du behöver

* En MXChip IoT DevKit tavla med Micro USB-kabel. [Hämta nu](https://aka.ms/iot-devkit-purchase).
* En dator som kör Windows 10- eller macOS 10.10 +.
* En aktiv Azure-prenumeration. [Aktivera en kostnadsfri 30-dagars utvärderingsversion Microsoft Azure-konto](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Förbered maskinvaran

Koppla samman maskinvara till datorn.

Du behöver den här maskinvara:

* DevKit tavla
* Micro USB-kabel

![Maskinvara som krävs](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Ansluta DevKit till datorn:

1. Anslut USB slutet på din dator.
2. Anslut Micro USB slutet till DevKit.
3. Grön Indikator för kraft bekräftar anslutningen.

![Maskinvaruanslutningar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Konfigurera Wi-Fi

IoT-projekt är beroende av internet-anslutning. Använd följande instruktioner för att konfigurera DevKit att ansluta till Wi-Fi.

### <a name="enter-ap-mode"></a>Ingå läget för Asien och Stillahavsområdet

Håll ned knappen B, push och släpp återställningsknappen och sedan knappen B. Din DevKit försätts i Asien och Stillahavsområdet läge för att konfigurera Wi-Fi. På skärmen visas nätverksnamn (SSID) för DevKit och IP-adressen för configuration-portalen.

![Återställ knappen, knappen B och SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Ansluta till DevKit Asien och Stillahavsområdet

Nu kan du använda en annan Wi-Fi-aktiverad enhet (dator eller mobiltelefon) för att ansluta till DevKit SSID (markerat i föregående bild). Lämna lösenordet tomt.

![Nätverksinformation och knappen Anslut](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurera Wi-Fi för DevKit

Öppna IP-adressen som visas på skärmen DevKit på din dator eller en mobiltelefon webbläsare, markera Wi-Fi-nätverk som du vill DevKit att ansluta till och sedan ange lösenordet. Välj **Anslut**.

![Lösenord och knappen Anslut](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

När anslutningen lyckas, startar om DevKit på några sekunder. Då visas Wi-Fi-namn och IP-adress på skärmen:

![Wi-Fi-namn och IP-adress](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> IP-adressen som visas i fotot kanske inte överensstämmer med själva IP-adressen tilldelas och visas på skärmen DevKit. Detta är normalt, eftersom Wi-Fi använder DHCP för att dynamiskt tilldela IP-adresser.

När Wi-Fi har konfigurerats, behålls dina autentiseringsuppgifter på enheten för anslutningen, även om enheten är inte ansluten. Om du konfigurerar DevKit för Wi-Fi hemma och sedan vidta DevKit till office, behöver du exempelvis konfigurera om AP-läge (med början i steg i avsnittet ”Ange AP läget”) för att ansluta DevKit till kontoret Wi-Fi. 

## <a name="start-using-the-devkit"></a>Börja använda DevKit

Standardapp som körs på DevKit kontrollerar den senaste versionen av den inbyggda programvaran och visar vissa diagnos sensordata åt dig.

### <a name="upgrade-to-the-latest-firmware"></a>Uppgradera till den senaste inbyggda programvaran

> [!NOTE] 
> Sedan v1.1 gör DevKit ST SAFE i startprogrammet. Du måste uppgradera inbyggda programvaran om du kör under v1.1 för att göra det fungerar förmodligen.

Om du behöver en uppgradering av inbyggd programvara, visar skärmen de aktuella och de senaste firmware-versionerna. Om du vill uppgradera, följer du de [uppgradera firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) guide.

![Visning av versioner av inbyggd programvara med aktuella och de senaste](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Det här är en enstaka ansträngning. När du börja utveckla på DevKit och ladda upp din app, kommer den senaste inbyggda programvaran med din app.

### <a name="test-various-sensors"></a>Testa olika sensorer

Tryck på knappen B för att testa sensorer. Fortsätt att trycka på och nedtryckt B för att gå igenom varje sensor.

![Visning av knappen B och sensorn](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Nu är det dags att konfigurera utvecklingsmiljön: verktyg och -paket för dig att skapa fantastiska IoT-program. Du kan välja den Windows- eller macOS-version enligt ditt operativsystem.

### <a name="windows"></a>Windows

Vi rekommenderar att du kan använda installationspaketet för att förbereda utvecklingsmiljön. Om du stöter på problem, kan du följa den [manuella steg](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) att den är klar.

#### <a name="download-the-latest-package"></a>Ladda ned det senaste-paketet

ZIP-filen som du laddar ned innehåller alla nödvändiga verktyg och paket för DevKit utveckling.

> [!div class="button"]
[Ladda ned](https://aka.ms/devkit/prod/installpackage/latest)

ZIP-filen innehåller följande verktyg och paket. Skriptet identifierar och hoppa över dem om du redan har vissa komponenter som är installerade.

* Node.js och Yarn: Runtime för installationsskriptet och automatiserade uppgifter.
* [Azure CLI 2.0-MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser. MSI innehåller beroende Python och pip.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): lätt Kodredigerare för DevKit utveckling.
* [Visual Studio Code-tillägg för Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): tillägg som möjliggör Arduino-utveckling i Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): verktyg som tillägget för Arduino förlitar sig på.
* DevKit tavla paket: Verktyget kedjor, bibliotek och projekt för DevKit.
* ST-Link-verktyget: Viktiga verktyg och drivrutiner.

#### <a name="run-the-installation-script"></a>Kör installationsskriptet

I Windows Utforskaren, leta upp ZIP-filen och extrahera den. Hitta `install.cmd`, högerklicka på den och välj **kör som administratör**.

![Utforskaren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Under installationen kan se du förloppet för varje verktyg eller paket.

![Installationsförlopp](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> Beroende på din miljö kan visas ibland fel när du installerar Arduino IDE. I det här fallet kan du försöka [installera Arduino IDE individuellt](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) och kör install.cmd igen. Annars följer du de [manuella steg](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) att installera alla nödvändiga verktyg och paket.

#### <a name="install-drivers"></a>Installera drivrutiner

VS-kod för Arduino-tillägget är beroende av Arduino IDE. Om det här är första gången du installerar Arduino IDE uppmanas du att installera relevanta drivrutiner:

![komma-igång-drivrutin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Installationen tar cirka 10 minuter beroende på din internet-hastighet. När installationen är klar bör du se Visual Studio Code och Arduino IDE genvägar på skrivbordet.

> [!NOTE] 
> Ibland när du startar VS Code, uppmanas du med ett fel att den inte hittar Arduino IDE eller relaterade tavla paketet. Om du vill lösa det genom att Stäng VS Code och starta om Arduino IDE. VS kod ska leta upp den Arduino IDE-sökvägen korrekt.

### <a name="macos"></a>macOS

Vi rekommenderar att du kan använda en installation för att förbereda utvecklingsmiljön. Om du stöter på problem, kan du följa den [manuella steg](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) att den är klar.

#### <a name="install-homebrew"></a>Installera Homebrew

> [!NOTE] 
> Om du har installerat Homebrew kan du hoppa över det här steget.

Följ den [anvisningarna](https://docs.brew.sh/Installation.html) att installera den.

#### <a name="download-the-latest-package"></a>Ladda ned det senaste-paketet
ZIP-filen som du laddar ned innehåller alla nödvändiga verktyg och paket för DevKit utveckling.

> [!div class="button"]
[Ladda ned](https://aka.ms/devkit/prod/installpackage/mac/latest)

ZIP-filen innehåller följande verktyg och paket. Skriptet identifierar och hoppa över dem om du redan har vissa komponenter som är installerade.

* Node.js och Yarn: Runtime för installationsskriptet och automatiserade uppgifter.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): lätt Kodredigerare för DevKit utveckling.
* [Visual Studio Code-tillägg för Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): tillägg som möjliggör Arduino-utveckling i Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): verktyg som tillägget för Arduino förlitar sig på.
* DevKit tavla paket: Verktyget kedjor, bibliotek och projekt för DevKit.
* ST-Link-verktyget: Viktiga verktyg och drivrutiner.

#### <a name="run-the-installation-script"></a>Kör installationsskriptet

Leta upp .zip i Finder och extrahera det:

![macOS finder](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Starta Terminal appen, leta upp den mapp du extrahera .zip-filen och kör:

```bash
./install.sh
```

![installera macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Om du uppfyller Homebrew Behörighetsfel kör `brew doctor` att korrigera detta. Kontrollera [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) för mer information.

Nu har du alla nödvändiga verktyg och paket som är installerade för macOS.


## <a name="open-the-project-folder"></a>Öppna projektmappen

### <a name="start-vs-code"></a>Starta VS Code

Kontrollera att din DevKit inte är ansluten. Starta VS Code först och ansluta DevKit till din dator. VS Code automatiskt hittar DevKit och öppnas startsidan:

![Introduktionssida](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> Ibland när du startar VS Code, uppmanas du med ett fel att den inte hittar Arduino IDE eller relaterade tavla paketet. Stäng VS Code och starta om Arduino IDE. VS kod ska leta upp den Arduino IDE-sökvägen korrekt.


### <a name="open-the-arduino-examples-folder"></a>Öppna mappen Arduino-exempel

På den **Arduino exempel** och bläddra till **exempel för MXCHIP AZ3166** > **AzureIoT**, och välj **GetStarted**.

![Fliken Arduino-exempel](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Om du råkar stänga fönstret kan öppna du den. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: exempel**.

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

Kör uppgiften i Lösningsfönstret `Ctrl+P` (Mac OS: `Cmd+P`) genom att ange `task cloud-provision`.

I VS Code-terminalen hjälper dig att etablera nödvändiga Azure-tjänster i en interaktiv kommandorad:

![Interaktiv kommandoraden](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Skapa och ladda upp Arduino-skiss

### <a name="windows"></a>Windows

1. Använd `Ctrl+P` att köra `task device-upload`.
2. Terminalen uppmanas du att ange konfigurationsläge. För att göra det, håll ned knappen A, och sedan push- och släpp återställningsknappen. På skärmen visas DevKit id och ”Configuration”.

Detta är att ange anslutningssträngen som hämtar från `task cloud-provision` steg.

VS Code startar sedan verifierar och laddar upp Arduino skiss:

![Verifiering och överföring av Arduino-skiss](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit startar om och börjat köra koden.

> [!NOTE] 
> Ibland kan du får felet ”fel: AZ3166: Okänd package”. Detta beror på tavlan paketindexet uppdateras inte. Kontrollera detta [vanliga frågor och svar steg](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) att lösa den.

### <a name="macos"></a>macOS

1. Placera DevKit i konfigurationsläge: Håll ned knappen A, och sedan push-meddelanden och versionen återställningsknappen. På skärmen visas ”Configuration”.
2. Använd `Cmd+P` att köra `task device-upload`.

Detta är att ange anslutningssträngen som hämtar från `task cloud-provision` steg.

VS Code startar sedan verifierar och laddar upp Arduino skiss:

![enhet-överföringen](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit startar om och börjat köra koden.

> [!NOTE] 
> Ibland kan du får felet ”fel: AZ3166: Okänd package”. Detta beror på tavlan paketindexet uppdateras inte. Kontrollera detta [vanliga frågor och svar steg](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) att lösa den.


## <a name="test-the-project"></a>Testa projektet

Följ dessa steg för att öppna och konfigurera seriell övervakaren i VS Code:

1. Klicka på den `COM[X]` word på statusfältet för att ange rätt COM-porten med `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Klicka på power plugin-ikonen i statusfältet för att öppna den seriella Monitor: ![seriell – övervaka](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. I statusfältet, klicka på det tal som representerar det överföringshastigheten och ange `115200`: ![-överföringshastighet](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

Exempelprogrammet körs när du ser följande resultat:

* Seriell övervakaren visar samma information som innehållet i skärmbilden nedan.
* RGB-LED på MXChip IoT DevKit blinkar.

![Slutgiltiga utdata i VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan du hitta [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Du kan också ge oss feedback genom att lämna en kommentar på den här sidan.

## <a name="next-steps"></a>Nästa steg

Du har anslutit en MXChip IoT DevKit till IoT-hubben och du har skickat det avbildade sensordata till din IoT-hubb.

Komma igång med Azure IoT Hub och utforska andra IoT-scenarier, se:

- [Hantera meddelanden mellan enheter och molnet med iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Spara meddelanden från IoT Hub i datalagring för Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Använd Power BI för att visualisera sensordata i realtid från Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Använda Web Apps för att visualisera sensordata i realtid från Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Väderprognoser med sensordata från IoT hub i Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Enhetshantering med IoT Hub-utforskaren](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Fjärrövervakning och aviseringar med Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
