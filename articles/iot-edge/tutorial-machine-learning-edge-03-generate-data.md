---
title: 'Självstudie: simulera enhets data – Azure IoT Edge & Machine Learning'
description: 'Självstudie: skapa virtuella enheter som genererar simulerad telemetri som senare kan användas för att träna en maskin inlärnings modell.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9b3b36cf5a0f426f2081179d08cf2f093a233fff
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665941"
---
# <a name="tutorial-generate-simulated-device-data"></a>Självstudie: skapa simulerade enhets data

> [!NOTE]
> Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt rekommenderar vi att du börjar med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa möjliga resultat.

I den här artikeln använder vi Machine Learning Training-data för att simulera en enhet som skickar telemetri till IoT Hub. Som anges i introduktionen använder den här självstudien för att [turbofan motor degradera simulerings data](https://c3.nasa.gov/dashlink/resources/139/) som simulerar data från en uppsättning flyg Plans motorer för utbildning och testning.

Från tillhör ande Readme. txt vet vi att:

* Data består av flera multivarierad tids serier
* Varje data uppsättning är indelad i utbildning och test del mängder
* Varje tids serie är från en annan motor
* Varje motor börjar med olika grader av inledande slitage och tillverknings variation

I den här självstudien använder vi en delmängd av en enda data uppsättning (FD003) för utbildning.

I verkligheten skulle varje motor vara en oberoende IoT-enhet. Förutsatt att du inte har en samling av turbofan-motorer som är anslutna till Internet, kommer vi att skapa en program vara för dessa enheter.

Simulatorn är ett C# program som använder sig av IoT Hub API: er för att registrera virtuella enheter program mässigt med IoT Hub. Vi läser sedan data för varje enhet från NASA data mängd och skickar dem till IoT-hubben med hjälp av en simulerad IoT-enhet. All kod för den här delen av själv studie kursen finns i DeviceHarness-katalogen för lagrings platsen.

DeviceHarness-projektet är ett .NET Core-projekt som C# har skrivits i fyra klasser:

* **Program:** Start punkten för körning som ansvarar för att hantera användarindata och övergripande samordning.
* **TrainingFileManager:** ansvarar för läsning och parsning av den valda data filen.
* **CycleData:** representerar en enskild rad med data i en fil som har konverterats till meddelande format.
* **TurbofanDevice:** ansvarig för att skapa en IoT-enhet som motsvarar en enskild enhet (tids serie) i data och överföring av data till IoT Hub via IoT-enheten.

De uppgifter som beskrivs i den här artikeln bör ta ungefär 20 minuter att slutföra.

Den verkliga motsvarigheten till arbetet i det här steget skulle förmodligen utföras av enhets utvecklare och moln utvecklare.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurera Visual Studio Code och build DeviceHarness Project

1. Öppna en fjärrskrivbordssession till den virtuella datorn, som visas i föregående artikel.

1. Öppna Visual Studio Code.

1. I Visual Studio Code väljer du **fil** > **Öppna mapp.** ...

1. I text rutan **mapp** anger `C:\source\IoTEdgeAndMlSample\DeviceHarness` och klickar på knappen **Välj mapp** .

   Om OmniSharp-fel visas i utdatafönstret måste du avinstallera C# tillägget, stänga och öppna vs Code igen, installera C# tillägget och sedan läsa in fönstret igen.

1. Eftersom du använder tillägg på den här datorn för första gången, kommer vissa tillägg att uppdatera och installera deras beroenden. Du kan uppmanas att uppdatera tillägget. I så fall, väljer du **Omladdnings fönster**.

1. Du uppmanas att lägga till nödvändiga till gångar för DeviceHarness. Välj **Ja** för att lägga till dem.

   * Det kan ta några sekunder innan meddelandet visas.
   * Om du missade det här meddelandet kontrollerar du ikonen "Bell" i det nedre högra hörnet.

   ![Meny tillägg för VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Välj **Återställ** för att återställa paket beroenden.

   ![VS Code Restore-prompt](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Kontrol lera att din miljö är korrekt konfigurerad genom att utlösa en build-, `Ctrl + Shift + B`-eller **Terminal** > **köra build-aktivitet**.

1. Du uppmanas att välja build-uppgiften som ska köras. Välj **build**.

1. Bygget körs och genererar ett meddelande som visar att det lyckades.

   ![Genererat utdata-meddelande](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Du kan göra detta genom att välja **Terminal** > **Konfigurera standard build-aktivitet...** och välja **skapa** från prompten.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Anslut till IoT Hub och kör DeviceHarness

Nu när vi har skapat projektet ansluter du till din IoT Hub för att få åtkomst till anslutnings strängen och övervakar förloppet för genereringen av data.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Logga in på Azure i Visual Studio Code

1. Logga in på din Azure-prenumeration i Visual Studio Code genom att öppna kommando-paletten `Ctrl + Shift + P` eller **visa** > **kommando palett...** .

1. Vid prompt-sökningen efter och väljer du **Azure: Logga**in.

1. Ett webbläsarfönster öppnas och du uppmanas att ange dina autentiseringsuppgifter. När du omdirigeras till en lyckad sida kan du stänga webbläsaren.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Anslut till din IoT-hubb och hämta nav-anslutningssträng

1. I det nedre avsnittet i Visual Studio Code Explorer väljer du rutan **Azure IoT Hub-enheter** för att expandera den.

1. I den expanderade rutan klickar du på **välj IoT Hub**.

1. När du uppmanas väljer du din Azure-prenumeration och sedan IoT Hub.

1. Klicka i ramen för **Azure IoT Hub-enheter** och klicka på **...** för fler åtgärder. Välj **kopiera IoT Hub anslutnings sträng**.

   ![Kopiera IoT Hub anslutnings sträng](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Kör DeviceHarness-projektet

1. Välj **visa** > **Terminal** för att öppna Visual Studio Code Terminal.

   Om du inte ser någon prompt väljer du RETUR.

1. Ange `dotnet run` i terminalen.

1. När du uppmanas att ange anslutnings strängen för IoT Hub klistrar du in anslutnings strängen som du kopierade i föregående avsnitt.

1. Klicka på knappen Uppdatera i rutan **Azure IoT Hub-enheter** .

   ![Uppdatera IoT Hub enhets lista](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observera att enheter läggs till IoT Hub och att enheterna visas i grönt för att indikera att data skickas via den enheten.

1. Du kan visa meddelanden som skickas till hubben genom att högerklicka på valfri enhet och välja **starta övervakning inbyggd händelse slut punkt**. Meddelandena visas i fönstret utdata i Visual Studio Code.

1. Stoppa övervakningen genom att klicka på fönstret utdata i **Azure IoT Hub Toolkit** och välj **stoppa övervakning inbyggd händelse slut punkt**.

1. Låt programmet köras klart, vilket tar några minuter.

## <a name="check-iot-hub-for-activity"></a>Kontrol lera IoT Hub för aktivitet

De data som skickas av DeviceHarness gick till din IoT-hubb. Det är enkelt att verifiera att data har nått hubben med hjälp av Azure Portal.

1. Öppna [Azure Portal](https://portal.azure.com/) och navigera till din IoT-hubb.

1. På sidan Översikt ser du att data har skickats till hubben:  

   ![Visa enhet till moln meddelanden i IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Verifiera data i Azure Storage

De data som vi precis skickade till din IoT Hub dirigerades till lagrings behållaren som vi skapade i föregående artikel. Nu ska vi titta på data i vårt lagrings konto.

1. Navigera till ditt lagringskonto i Azure Portal.

1. Välj **Storage Explorer (för hands version)** i lagrings konto navigeringen.

1. I Storage Explorer väljer du **BLOB-behållare** och sedan **devicedata**.

1. I innehålls rutan klickar du på mappen för namnet på IoT Hub, sedan året, månaden, dagen och timmen. Du kommer att se flera mappar som representerar minuterna när data skrevs.

   ![Visa mappar i Blob Storage](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klicka i någon av dessa mappar för att söka efter datafiler med etiketten **00** och **01** som motsvarar partitionen.

1. Filerna skrivs i [Avro](https://avro.apache.org/) -format, men om du dubbelklickar på en av dessa filer öppnas en annan webbläsare-flik som delvis återger data. Om du i stället uppmanas att öppna filen i ett program kan du välja VS Code och den kommer att återges på rätt sätt.

1. Du behöver inte försöka att läsa eller tolka data just nu. Vi kommer att göra det i nästa artikel.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi ett .NET Core-projekt för att skapa en uppsättning virtuella enheter och skicka data via de enheterna via vår IoT Hub och till en Azure Storage-behållare. Det här projektet simulerar ett verkligt scenario där fysiska enheter skickar data, inklusive sensor avläsningar, operativa inställningar, felaktiga signaler och lägen, till en IoT Hub och därefter till en granskad lagring. När tillräckligt med data har samlats in använder vi den för att träna modeller som förutsäger återstående livs längd (RUL) för enheten, som vi kommer att visa i nästa artikel.

Fortsätt till nästa artikel om du vill träna en maskin inlärnings modell med data.

> [!div class="nextstepaction"]
> [Träna och distribuera en Azure Machine Learning modell](tutorial-machine-learning-edge-04-train-model.md)
