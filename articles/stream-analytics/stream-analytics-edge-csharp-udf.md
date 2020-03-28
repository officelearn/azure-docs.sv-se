---
title: Självstudiekurs - Skriv C# användardefinierade funktioner för Azure Stream Analytics-jobb i Visual Studio (förhandsversion)
description: Den här självstudien visar hur du skriver c# användardefinierade funktioner för Stream Analytics-jobb i Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 1d71f4c5616efb05efe2733c49507b085ca2dcf6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75426302"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Självstudiekurs: Skriv en C# användardefinierad funktion för Azure Stream Analytics-jobb (förhandsversion)

Med användardefinierade funktioner med C# (UDF skapas i Visual Studio) kan du utöka Azure Stream Analytics-frågespråket med dina egna funktioner. Du kan återanvända befintlig kod (inklusive DLL-filer) och använda matematiska eller komplex logik med C#. Det finns tre sätt att implementera UDF:er: CodeBehind-filer i ett Stream Analytics-projekt, UDF:er från ett lokalt C#-projekt eller UDF:er från ett befintligt paket från ett lagringskonto. Den här självstudien använder metoden CodeBehind för att implementera en grundläggande C#-funktion. UDF-funktionen för Stream Analytics-jobb är för närvarande i förhandsversion och bör inte användas i produktionsarbetsbelastningar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en användardefinierad funktion i C# med CodeBehind.
> * Testa ditt Stream Analytics-jobb lokalt.
> * Publicera ditt jobb till Azure.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att du har slutfört följande krav:

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installera [Stream Analytics-verktygen för Visual Studio](stream-analytics-tools-for-visual-studio-install.md) och arbetsbelastningen **Azure development** (Azure-utveckling) eller **Data Storage and Processing** (Datalagring och bearbetning).
* Ta en titt på den befintliga [Stream Analytics Edge-utvecklingsguiden] om du skapar ett IoT Edge-jobb (stream-analytics-tools-for-visual-studio-edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Skapa en container i ditt Azure Storage-konto

Behållaren som du skapar används för att lagra det kompilerade C#-paketet. Om du skapar ett Edge-jobb används det här lagringskontot också för att distribuera paketet till din IoT Edge-enhet. Använd en särskild container för varje Stream Analytics-jobb. Det går inte att använda samma container för flera Stream Analytics Edge-jobb. Om du redan har ett lagringskonto med befintliga containrar kan du använda dem. Annars måste du [skapa en ny container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Skapa ett Stream Analytics-projekt i Visual Studio

1. Starta Visual Studio.

2. Välj **Arkiv > Nytt > Projekt**.

3. I malllistan till vänster väljer du **Stream Analytics**och väljer sedan Azure Stream Analytics **Edge Application** eller Azure Stream **Analytics Application**.

4.  Ange projektets **namn**, **plats** och **lösningsnamn**. Välj sedan **OK**.

    ![Skapa ett Azure Stream Analytics Edge-projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Konfigurera sökvägen för sammansättningspaketet

1. Öppna Visual Studio och navigera till **Solution Explorer**.

2. Dubbelklicka på jobbets konfigurationsfil, `EdgeJobConfig.json`.

3. Expandera avsnittet **användardefinierade kod Configuration** avsnittet och fyll i konfigurationen med följande föreslagna värden:

   |**Inställning**|**Föreslaget värde**|
   |-------|---------------|
   |Resurs för globala lagringsinställningar|Välj datakälla från det aktuella kontot|
   |Prenumeration på globala lagringsinställningar| < din prenumeration >|
   |Lagringskonto för globala lagringsinställningar| < ditt lagringskonto >|
   |Resurs för anpassade inställningar för kodlagring|Välj datakälla från det aktuella kontot|
   |Lagringskonto för anpassade lagringsinställningar för kod|< ditt lagringskonto >|
   |Behållare för anpassade inställningar för kodlagring|< din förvaringsbehållare >|


## <a name="write-a-c-udf-with-codebehind"></a>Skriv en C#-UDF med CodeBehind
En CodeBehind-fil är en C#-fil som är associerad med ett enda ASA-frågeskript. Visual Studio-verktyg kommer automatiskt komprimera CodeBehind-filen och överföra den till Azure storage-kontot när den skickas. Alla klasser måste definieras som *offentliga* och alla objekt måste definieras som *statiska offentliga*.

1. I **Solution Explorer**, expandera **Script.asql** för att hitta CodeBehind-filen **Script.asaql.cs**.

2. Ersätt koden med följande exempel:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implementera en användardefinerad funktion

1. I **Solution Explorer**öppnar du filen **Script.asaql**.

2. Ersätt den befintliga frågan med följande:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Lokal testning

1. Hämta [exempeldatafilen för temperatursimulatorn](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. I **Solution Explorer**, expandera **Indata**, högerklicka på **Input.json** och välj **Lägg till lokala indata**.

   ![Lägg till lokala indata till Stream Analytics-jobbet i Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Ange den lokala indatafilens sökväg för de exempeldata som du hämtade och **spara**.

    ![Konfiguration för lokala indata till Stream Analytics-jobbet i Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Klicka på **Kör lokalt** i Skriptredigeraren. När resultatet har sparats för lokal körning trycker du på valfri tangent för att se resultatet i tabellformat. 

    ![Köra Azure Stream Analytics-jobb lokalt med Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Du kan också välja **Öppna resultatmappen** för att se raw-filer i JSON- och CSV-format.

    ![Visa resultat från lokalt Azure Stream Analytics-jobb med Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Felsöka en UDF
Du kan felsöka dina C#-UDF:er lokalt på samma sätt som du skulle felsöka standard-C#-kod. 

1. Lägga till brytpunkter i en C#-funktion.

    ![Lägga till brytpunkter i en användardefinierad funktion för Stream Analytics i Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Starta felsökningen genom att trycka på **F5**. Programmet kommer att avbrytas vid dina brytpunkter som förväntat.

    ![Visa felsökningsresultat för användardefinierad funktion för Stream Analytics](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publicera ditt jobb på Azure
När du har testat din fråga lokalt väljer du **skicka till Azure** i skriptredigeraren för att publicera projektet på Azure.

![Skicka in ditt Stream Analytics Edge-jobb till Azure från Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Distribuera till IoT Edge-enheter
Om du väljer att skapa ett Stream Analytics Edge-jobb kan detta nu distribueras som en IoT Edge-modul. Följ snabbstarten om [IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart) för att skapa en IoT-hubb, registrera en IoT Edge-enhet och installera och starta IoT Edge-körningen på din enhet. Följ sedan självstudien om att [distribuera jobbet](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) om du vill distribuera ditt Stream Analytics-jobb som en IoT Edge-modul. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en enkel C#-användardefinierad funktion med CodeBehind, publicerade jobbet till Azure och distribuerade jobbet till Azure eller IoT Edge-enheten. 

Om du vill veta mer om de olika sätten att använda C# användardefinierade funktioner för Stream Analytics-jobb fortsätter du till den här artikeln:

> [!div class="nextstepaction"]
> [Skriva funktioner i C# för Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
