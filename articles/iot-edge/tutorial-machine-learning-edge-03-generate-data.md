---
title: Skapa simulerad enhetsdata – Machine Learning på Azure IoT Edge | Microsoft Docs
description: Skapa virtuella enheter som genererar simulerad telemetri som kan användas senare för att träna en maskininlärningsmodell.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 666172e3685b923ca0d0e5fa02878341fcd0a216
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543863"
---
# <a name="tutorial-generate-simulated-device-data"></a>Självstudier: Generera simulerade enhetsdata

> [!NOTE]
> Den här artikeln ingår i en serie med en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har hamnat på den här artikeln direkt, rekommenderar vi att du börja med den [först artikel](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln använder vi maskininlärning finjusteringsdata att simulera en enhet som skickar telemetri till IoT Hub. Enligt informationen i inledningen, den här självstudien för slutpunkt till slutpunkt används den [Turbofan engine försämring simulering datauppsättning](https://c3.nasa.gov/dashlink/resources/139/) att simulera data från en uppsättning flygplan motorer för träning och testning.

Vi vet att från tillhörande readme.txt:

* Data består av flera multivarierad tidsserier
* Varje datauppsättning är uppdelad i delmängder för träning och testning
* Varje tidsserie är från en annan modul
* Varje motor som börjar med olika grader av inom variation och inledande slitage

I den här självstudien använder vi utbildning data delmängd av en enda datauppsättning (FD003).

I verkligheten kan är varje motor en oberoende IoT-enhet. Under förutsättning att du inte har en uppsättning Internetanslutna turbofan motorer tillgänglig, kommer vi att skapa stället programvara för dessa enheter.

Simulatorn är en C# program som använder API: er för IoT Hub programmässigt registrera virtuella enheter med IoT Hub. Vi läser data för varje enhet från en delmängd av data som tillhandahållits av NASA och skicka den till din IoT-hubb med en simulerad IoT-enhet. All kod för den här delen av kursen finns i katalogen DeviceHarness på lagringsplatsen.

DeviceHarness projektet är en .NET core-projektet som skrivits i C# som består av fyra klasser:

* **Program:** Startpunkten för körning som ansvarar för att hantera indata från användaren och övergripande koordination.
* **TrainingFileManager:** ansvarar för att läsa och tolka datafilen.
* **CycleData:** representerar en enda rad med data i en fil som ska konverteras till meddelandeformat.
* **TurbofanDevice:** ansvarar för att skapa en IoT-enhet som motsvarar en enskild enhet (tidsserier) i data och överföring av data till IoT Hub via IoT-enheten.

De uppgifter som beskrivs i den här artikeln bör ta ungefär 20 minuter för att slutföra.

Verkliga motsvarighet till arbetet i det här steget utförs troligen av enheten utvecklare och molnutvecklare.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurera Visual Studio Code och skapar DeviceHarness projekt

1. Öppna en fjärrskrivbordssession till den virtuella datorn som visas i föregående artikel.

1. Öppna Visual Studio Code.

1. I Visual Studio Code, Välj **filen** > **Öppna mapp...** .

1. I den **mappen** textrutan Ange `C:\source\IoTEdgeAndMlSample\DeviceHarness` och klicka på den **Välj mapp** knappen.

   Om OmniSharp fel visas i utdatafönstret, måste du avinstallera den C# tillägg, Stäng och öppna VS Code, installera den C# tillägget och sedan ladda om fönstret.

1. Eftersom du använder tillägg på den här datorn för första gången, vissa tillägg uppdateras och installera beroenden. Du kan uppmanas att uppdatera tillägget. I så, fall Välj **Reload Window**.

1. Du uppmanas att lägga till tillgångar som krävs för DeviceHarness. Välj **Ja** att lägga till dem.

   * Meddelandet kan ta några sekunder visas.
   * Om du missade meddelandet kan du kontrollera ”klockikonen” i det nedre högra hörnet.

   ![Popup-fönstret för tillägg för VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Välj **återställa** att återställa paketberoenden.

   ![VS Code Återställ fråga](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Verifiera att din miljö är korrekt konfigurerad genom att utlösa en build `Ctrl + Shift + B` eller **Terminal** > **kör Skapa uppgift**.

1. Du uppmanas att välja att skapa aktiviteten ska köras. Välj **skapa**.

1. Bygget körs och matar ut ett meddelande.

   ![Skapa lyckades utdatameddelande](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Du kan göra detta skapa build standardaktiviteten genom att välja **Terminal** > **konfigurera standard skapa uppgift...**  och välja **skapa** från Kommandotolken.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Ansluta till IoT Hub och kör DeviceHarness

Nu när vi har projektet att skapa, ansluta till din IoT-hubb för att komma åt anslutningssträngen och övervaka förloppet för genereringen av data.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Logga in på Azure i Visual Studio Code

1. Logga in på din Azure-prenumeration i Visual Studio Code genom att öppna kommandopaletten, `Ctrl + Shift + P` eller **visa** > **Kommandopaletten...** .

1. Vid fråga sökningen för och väljer **Azure: Logga In**.

1. Ett webbläsarfönster öppnas och du uppmanas att ange dina autentiseringsuppgifter. När du blir omdirigerad till sidan för genomfört, kan du stänga webbläsaren.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Ansluta till din IoT-hubb och hämta hub-anslutningssträngen

1. I avsnittet längst ned i Visual Studio Code-Utforskaren, Välj den **Azure IoT Hub-enheter** ramens så att det expanderas.

1. I den expanderade tidsperiod, klickar du på **Välj IoT Hub**.

1. När du uppmanas, Välj din Azure-prenumeration och sedan din IoT-hubb.

1. Klicka på den **Azure IoT Hub-enheter** ram och klicka på **...**  efter fler åtgärder. Välj **kopia IoT Hub-anslutningssträngen**.

   ![Kopiera anslutningssträngen för IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Kör projektet DeviceHarness

1. Välj **visa** > **Terminal** att öppna Visual Studio Code-terminalen.

   Om du inte ser en uppmaning, tryck på RETUR.

1. Ange `dotnet run` i terminalen.

1. När du tillfrågas om IoT Hub-anslutningssträngen, klistra in anslutningssträngen som du kopierade i föregående avsnitt.

1. I den **Azure IoT Hub-enheter** klickar du på Uppdatera-knappen.

   ![Uppdatera listan över enheter i IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observera att enheter läggs till IoT-hubb och att enheterna som visas i grönt att ange dessa data som skickas via den enheten.

1. Du kan visa meddelandena som skickas till hubben genom att högerklicka på valfri enhet och välja **starta inbyggda händelse slutpunkt för övervakning av**. Meddelandena visas i utdatafönstret i Visual Studio Code.

1. Stoppa övervakning genom att klicka i den **Azure IoT Hub Toolkit** utdata rutan och välj **stoppa inbyggda händelse slutpunkt för övervakning av**.

1. Låt programmet att slutföras, vilket tar några minuter.

## <a name="check-iot-hub-for-activity"></a>Kontrollera IoT Hub för aktivitet

Data som skickas av DeviceHarness gick till din IoT hub. Det är enkelt att verifiera att data har nått din hubb med Azure portal.

1. Öppna den [Azure-portalen](https://portal.azure.com/) och navigera till din IoT-hubb.

1. På översiktssidan översikt bör du se att data har skickats till hubben:  

   ![Visa enhet att meddelanden från molnet i IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Verifiera data i Azure Storage

Informationen som vi just skickade till din IoT-hubb har dirigeras till behållaren som vi skapade i föregående artikel. Vi tittar på data i våra storage-konto.

1. Navigera till ditt lagringskonto i Azure Portal.

1. Storage-konto Navigatören, väljer du **Lagringsutforskaren (förhandsversion)** .

1. I Lagringsutforskaren, väljer **Blobbehållare** sedan **devicedata**.

1. I innehållsrutan klickar du på mappen för namnet på IoT hub, och sedan året, månaden, dagen och timmen. Du kan se flera mappar som representerar minuter när data skrevs.

   ![Visa mappar i blob storage](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klicka på någon av dessa mappar och hitta filer med etiketten **00** och **01** för partitionen.

1. Filerna som är skrivna i [Avro](https://avro.apache.org/) formatet, men Dubbelklicka på någon av dessa filer öppnas en annan flik i webbläsaren och delvis återge data. Om du i stället uppmanas du att öppna filen i ett program, kan du välja VS-kod och det renderas korrekt.

1. Det finns inget behov av att försök att läsa och tolka data just nu. Vi gör det i nästa artikel.

## <a name="next-steps"></a>Nästa steg

I den här artikeln används vi en .NET Core-projektet för att skapa en uppsättning virtuella enheter och skicka data via dessa enheter via vår IoT Hub och till en Azure Storage-behållare. Det här projektet simulerar ett verkligt scenario där fysiska enheter skicka data, inklusive sensoravläsning, inställningar, fel signaler och lägen, och så vidare till en IoT Hub och och uppåt till en granskad storage. När tillräckligt med data har samlats in, vi bara använda den för att skapa modeller som förutsäga komponenternas livslängd (RUL) för enheten, vilket visar vi i nästa artikel.

Fortsätt till nästa artikel för att träna en maskininlärningsmodell med data.

> [!div class="nextstepaction"]
> [Träna och distribuera en Azure Machine Learning-modell](tutorial-machine-learning-edge-04-train-model.md)
