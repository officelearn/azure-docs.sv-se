---
title: 'Självstudiekurs: Generera simulerade enhetsdata – Machine Learning på Azure IoT Edge'
description: Skapa virtuella enheter som genererar simulerad telemetri som senare kan användas för att träna en maskininlärningsmodell.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f7a971315183e867ae06b58801d5855f90462a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76722398"
---
# <a name="tutorial-generate-simulated-device-data"></a>Självstudiekurs: Generera simulerade enhetsdata

> [!NOTE]
> Den här artikeln är en del av en serie för en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt, uppmuntrar vi dig att börja med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln använder vi maskininlärningsutbildningsdata för att simulera en enhet som skickar telemetri till Azure IoT Hub. Som anges i inledningen använder den här guiden [turbofanmotorns nedbrytningssimuleringsdatauppsättning](https://c3.nasa.gov/dashlink/resources/139/) för att simulera data från en uppsättning flygplansmotorer för utbildning och testning.

I vårt experimentella scenario vet vi att:

* Data består av flera multivariata tidsserier.
* Varje datauppsättning är uppdelad i tränings- och testundergrupper.
* Varje tidsserie kommer från en annan motor.
* Varje motor börjar med olika grader av inledande slitage och tillverkning variation.

För den här självstudien använder vi undergruppen för träningsdata i en enda datauppsättning (FD003).

I verkligheten skulle varje motor vara en oberoende IoT-enhet. Förutsatt att du inte har en samling internet-anslutna turbofan motorer tillgängliga, kommer vi att bygga en programvara stand-in för dessa enheter.

Simulatorn är ett C#-program som använder IoT Hub API:er för att programmässigt registrera virtuella enheter med IoT Hub. Vi läser sedan data för varje enhet från NASA-erhållna dataundergruppen och skickar den till din IoT-hubb med en simulerad IoT-enhet. All kod för den här delen av självstudien finns i DeviceHarness-katalogen i databasen.

DeviceHarness-projektet är ett .NET-kärnprojekt skrivet i C# som består av fyra klasser:

* **Program:** Startpunkten för utförande som ansvarar för hantering av användarindata och övergripande samordning.
* **UtbildningFileManager:** Ansvarig för att läsa och tolka den valda datafilen.
* **CycleData:** Representerar en enda rad med data i en fil som konverterats till meddelandeformat.
* **TurbofanDevice:** Ansvarig för att skapa en IoT-enhet, som motsvarar en enda enhet (tidsserie), i data och överföra data till IoT Hub.

De uppgifter som beskrivs i den här artikeln bör ta cirka 20 minuter att slutföra.

Den verkliga motsvarigheten till arbetet i det här steget skulle sannolikt utföras av enhetsutvecklare och molnutvecklare.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurera Visual Studio-kod och skapa DeviceHarness-projekt

1. Öppna en fjärrskrivbordssession till den virtuella utvecklingsdatorn.

1. Öppna mappen i `C:\source\IoTEdgeAndMlSample\DeviceHarness` Visual Studio-kod.

1. Eftersom du använder tillägg på den här datorn för första gången uppdateras och installeras deras beroenden i vissa tillägg. Du kan uppmanas att uppdatera tillägget. Om så är fallet väljer du **Ladda om fönster**.

   Om OmniSharp-fel visas i utdatafönstret måste du avinstallera C#-tillägget.

1. Du uppmanas att lägga till nödvändiga resurser för DeviceHarness. Välj **Ja** om du vill lägga till dem.

   * Det kan ta några sekunder innan meddelandet visas.
   * Om du missade det här meddelandet kontrollerar du klockikonen i det nedre högra hörnet.

   ![VS-kod förlängning popup](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Välj **Återställ** om du vill återställa paketberoendena.

   ![VS-kodåterställningsfråga](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Om du inte får dessa meddelanden stänger du Visual Studio-kod, `C:\source\IoTEdgeAndMlSample\DeviceHarness`tar bort lagerplats- och obj-kataloger i , öppnar Visual Studio-kod och öppnar deviceHarness-mappen igen.

1. Verifiera att din miljö är korrekt konfigurerad genom att utlösa en version, **Ctrl** + **Skift** + **B**eller **Terminal** > Run Build**Task**.

1. Du uppmanas att välja den bygguppgift som ska köras. Välj **Bygg**.

1. Bygget körs och utdata ett lyckat meddelande.

   ![Skapa efterföljande utdatameddelande](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Du kan göra den här versionen till standardversionsuppgift genom att välja **Terminal** > **Konfigurera standardversionsuppgift...** och välja **Skapa** från prompten.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Ansluta till IoT Hub och kör DeviceHarness

Nu när vi har projektbyggnaden ansluter du till din IoT-hubb för att komma åt anslutningssträngen och övervaka datagenereringens förlopp.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Logga in på Azure i Visual Studio-kod

1. Logga in på din Azure-prenumeration i Visual `Ctrl + Shift + P` Studio-kod genom att öppna kommandopaletten eller **Visa** > **kommandopalett**.

1. Sök efter kommandot **Azure: Sign In.**

   Ett webbläsarfönster öppnas och uppmanas att ange dina autentiseringsuppgifter. När du omdirigeras till en framgångssida kan du stänga webbläsaren.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Ansluta till IoT-hubben och hämta hubbanslutningssträngen

1. I den nedre delen av Utforskaren för Visual Studio-kod väljer du **Azure IoT Hub-ramen** för att expandera den.

1. Klicka på **Välj IoT Hub**i den utökade ramen .

1. När du uppmanas till det väljer du din Azure-prenumeration och sedan till din IoT-hubb.

1. Klicka på **...** till höger om **Azure IoT Hub** för fler åtgärder. Välj **Kopiera IoT Hub-anslutningssträng**.

   ![Kopiera anslutningssträng för IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Köra DeviceHarness-projektet

1. Välj **Visa** > **terminal** för att öppna Visual Studio-kodterminalen.

   Om du inte ser någon fråga trycker du på Retur.

1. Ange `dotnet run` i terminalen.

1. Klistra in anslutningssträngen som kopierats i föregående avsnitt när du uppmanas att ange anslutningssträngen.

1. Klicka på uppdateringsknappen i azure **IoT Hub-enhetsramen.**

   ![Uppdatera ioT-hubbenhetslista](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observera att enheter läggs till i IoT Hub och att enheterna visas i grönt för att indikera att data skickas via den enheten. När enheterna har skickat meddelanden till IoT-hubben kopplar de från och ser blå ut.

1. Du kan visa meddelanden som skickas till navet genom att högerklicka på vilken enhet som helst och välja **Start övervakning inbyggd händelseslutpunkt**. Meddelandena visas i utdatafönstret i Visual Studio-kod.

1. Stoppa övervakningen genom att klicka i utdatafönstret för **Azure IoT Hub** och välj **Sluta övervaka inbyggd händelseslutpunkt**.

1. Låt programmet köras till slutförande, vilket tar några minuter.

## <a name="check-iot-hub-for-activity"></a>Kontrollera IoT Hub för aktivitet

Data som skickades av DeviceHarness gick till din IoT-hubb, där du kan verifiera i Azure-portalen.

1. Öppna [Azure-portalen](https://portal.azure.com/) och navigera till IoT-hubben som skapats för den här självstudien.

1. Välj **Mått**under **Övervakning**på menyn till vänster.

1. På diagramdefinitionssidan klickar du på listrutan **Mått,** rullar nedåt i listan och väljer **Routning: data som levereras till lagring**. Diagrammet ska visa toppen av när data dirigerades till lagring.

   ![Diagrammet visar topp när data levereras till lagring](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validera data i Azure Storage

De data som vi just skickade till din IoT-hubb dirigerades till lagringsbehållaren som vi skapade i föregående artikel. Låt oss titta på data i vårt lagringskonto.

1. Navigera till ditt lagringskonto i Azure Portal.

1. Välj **Storage Explorer (preview)** i navigatorn för lagringskontot .

1. I lagringsutforskaren väljer `devicedata`du **Blob Containers** och sedan .

1. Klicka på mappen i innehållsfönstret för namnet på IoT-hubben, följt av år, månad, dag och timme. Du kommer att se flera mappar som representerar protokollet när data skrevs.

   ![Visa mappar i blob-lagring](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klicka i en av dessa mappar för att hitta datafiler märkta **00** och **01** som motsvarar partitionen.

1. Filerna är skrivna i [Avro-format.](https://avro.apache.org/) Dubbelklicka på en av dessa filer för att öppna en annan webbläsarflik och delvis återge data. Om du uppmanas att öppna filen i ett program kan du välja VS-kod och den återges korrekt.

1. Det finns ingen anledning att försöka läsa eller tolka data just nu; vi kommer att göra det i nästa artikel.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi ett .NET Core-projekt för att skapa en uppsättning virtuella IoT-enheter och skicka data via dem till vår IoT-hubb och till en Azure Storage-behållare. Det här projektet simulerar ett verkligt scenario där fysiska IoT-enheter skickar data till en IoT Hub och vidare till en kurerad lagring. Dessa data omfattar sensoravläsningar, driftinställningar, felsignaler och lägen och så vidare. När tillräckligt med data har samlats in använder vi den för att träna modeller som förutsäger den återstående livslängden (RUL) för enheten. Vi demonstrerar den här maskininlärningen i nästa artikel.

Fortsätt till nästa artikel för att träna en maskininlärningsmodell med data.

> [!div class="nextstepaction"]
> [Träna och distribuera en Azure Machine Learning-modell](tutorial-machine-learning-edge-04-train-model.md)
