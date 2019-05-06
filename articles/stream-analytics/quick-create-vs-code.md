---
title: Skapa cloud Azure Stream Analytics-jobb i Visual Studio Code (förhandsversion)
description: Den här snabbstarten visar hur du kommer igång genom att skapa ett Stream Analytics-jobb, konfigurera indata och utdata, och definiera en fråga med Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 511dab7090f6114c7769d504166f3e2c137d43ca
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65071840"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Snabbstart: Skapa cloud Azure Stream Analytics-jobb i Visual Studio Code (förhandsversion)

Den här snabbstarten visar hur du skapar och kör ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-tillägget för Visual Studio Code. Exempeljobbet läser strömmande data från en IoT Hub-enhet. Du definierar ett jobb som beräknar medeltemperaturen när den överstiger 27° och skriver utdatahändelser till en ny fil i Blob Storage.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure Portal](https://portal.azure.com/).

* Installera [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-extension"></a>Installera tillägget Azure Stream Analytics

1. Öppna Visual Studio Code.

2. Från **tillägg** på den vänstra rutan Sök efter **Stream Analytics** och välj **installera** på den **Azure Stream Analytics** tillägget.

3. När tillägget har installerats kan du kontrollera att **Azure Stream Analytics Tools** visas i din **aktiverat tillägg**.

   ![Azure Stream Analytics-verktyg under aktiverat tillägg i Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Aktivera tillägget Azure Stream Analytics

1. Välj den **Azure** -ikonen i Aktivitetsfältet VS Code. **Stream Analytics** kommer att visas i fältet på klientsidan. Under **Stream Analytics**väljer **logga in på Azure**. 

   ![Logga in på Azure i Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. När du har loggat in visas namnet på ditt Azure i statusfältet i det nedre vänstra hörnet i VS Code-fönstret.

> [!NOTE]
> Azure Stream Analytics-verktyg kommer automatiskt att logga in vid nästa om du inte loggar. Om ditt konto har tvåfaktorsautentisering, rekommenderas att du använder phone autentisering i stället för en PIN-kod.
> Om du har problem med lista över resurser, hjälper logga ut och logga in igen vanligtvis. Ange kommandot för att logga ut `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics-jobbet bör du förbereda de data som senare konfigureras som jobbindata. Förbered de indata som krävs för jobbet genom att utföra följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Välj **Skapa en resurs** > **Sakernas internet** > **IoT Hub**.

3. I rutan **IoT-hubb** anger du följande information:
   
   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Prenumeration  | \<Din prenumeration\> |  Ange den prenumeration som du vill använda. |
   |Resursgrupp   |   asaquickstart-resourcegroup  |   Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto. |
   |Region  |  \<Välj den region som är närmast dina användare\> | Välj en geografisk plats där du kan hantera din IoT-hubb. Använd den plats som är närmast dina användare. |
   |IoT-hubbnamn  | MyASAIoTHub  |   Välj ett namn för din IoT-hubb.   |

   ![Skapa en IoT Hub](./media/quick-create-vs-code/create-iot-hub.png)

4. Välj **Nästa: Ange storlek och skala**.

5. Välj **pris- och skalningsnivå**. För den här snabbstarten väljer du nivån **F1 – kostnadsfri** om den fortfarande är tillgänglig för din prenumeration. Om den kostnadsfria nivån inte är tillgänglig väljer du den lägsta tillgängliga nivån. Mer information finns i [Prissättning för IoT-hubb](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Bestäm storlek och skala för din IoT-hubb](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Välj **Granska + skapa**. Gå igenom informationen om IoT-hubben och klicka på **Skapa**. Det kan ta några minuter innan IoT-hubben skapas. Du kan övervaka förloppet i **meddelandefönstret**.

7. I navigeringsmenyn för din IoT-hubb klickar du på **Lägg till** under **IoT-enheter**. Lägg till ett **Enhets-ID** och klicka på **Spara**.

   ![Lägg till en enhet i din IoT-hubb](./media/quick-create-vs-code/add-device-iot-hub.png)

8. När enheten har skapats öppnar du enheten från listan över **IoT-enheter**. Kopiera **Anslutningssträng – primärnyckel** och spara den i en anteckningsfil för senare användning.

   ![Kopiera anslutningssträngen för IoT-hubbenhet](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Skala bloblagring

1. Välj **Skapa en resurs** > **Lagring** > **Lagringskonto** i det övre vänstra hörnet i Azure-portalen.

2. I fönsterrutan **Skapa lagringskonto** anger du namn, plats och resursgrupp för lagringskonto. Välj samma plats och resursgrupp som den IoT-hubb som du skapade. Klicka sedan på **Granska + skapa** för att skapa kontot.

   ![Skapa lagringskonto](./media/quick-create-vs-code/create-storage-account.png)

3. När ditt lagringskonto har skapats väljer du panelen **Blobar** på panelen **Översikt**.

   ![Översikt över lagringskonto](./media/quick-create-vs-code/blob-storage.png)

4. Från sidan **Blob Service** väljer du **Container** och anger ett namn för containern, till exempel *container1*. Låt **Offentlig åtkomstnivå** vara **Privat (ingen anonym åtkomst)** och välj **OK**.

   ![Skapa blobcontainer](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. I Visual Studio Code trycker du på **Ctrl + Skift + P** att öppna kommandopaletten. Skriv sedan **ASA** och välj **ASA: Skapa nytt projekt**.

   ![Skapa nytt projekt](./media/quick-create-vs-code/create-new-project.png)

2. Ange ditt projektnamn som **myASAproj** och välj en mapp för ditt projekt.

    ![Skapa projektnamn](./media/quick-create-vs-code/create-project-name.png)

3. Det nya projektet läggs till din arbetsyta. En ASA-projektet består av Frågeskript **(*.asaql)**, ett **JobConfig.json** -fil och en **asaproj.json** konfigurationsfilen.

   ![Stream Analytics-projektfiler i VS Code](./media/quick-create-vs-code/asa-project-files.png)

4. Den **asaproj.json** konfigurationsfilen innehåller indata, utdata och jobbet filen konfigurationsinformation som behövs för att skicka ett Stream Analytics-jobb till Azure.

   ![Konfigurationsfilen för Stream Analytics-jobb i VS Code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> När du lägger till indata och utdata från kommandopaletten motsvarande sökvägar ska läggas till i **asaproj.json** automatiskt. Om du lägger till eller ta bort indata eller utdata på disken direkt, måste du manuellt lägga till eller ta bort dem från **asaproj.json**. Du kan välja att placera indata och utdata i en placera sedan referera till dem i olika jobb genom att ange sökvägarna i varje **asaproj.json**.

## <a name="define-an-input"></a>Definiera indata

1. Välj **Ctrl + Skift + P** öppna kommandopaletten och ange **ASA: Lägg till indata**.

   ![Lägg till indata för Stream Analytics i VS Code](./media/quick-create-vs-code/add-input.png)

2. Välj **IoT Hub** för indatatyp.

   ![Välj IoT Hub som alternativ](./media/quick-create-vs-code/iot-hub.png)

3. Välj fråga ASA-skriptet som ska använda indata. Det bör automatiskt fylla i med sökväg till **myASAproj.asaql**.

   ![Välj en ASA-skriptet i Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Ange indatafilnamn som **IotHub.json**.

5. Redigera **IoTHub.json** med följande värden. Behåll standardvärdena för fält som inte nämns nedan. Du kan använda CodeLens för att hjälpa dig att ange en sträng, Välj från en listruta eller ändra texten direkt i filen.

   |Inställning|Föreslaget värde|Beskrivning|
   |-------|---------------|-----------|
   |Namn|Indata|Ange ett namn som identifierar jobbets indata.|
   |IotHubNamespace|MyASAIoTHub|Välj eller ange namnet på IoT-hubben. IoT-hubbnamn identifieras automatiskt om de skapas i samma prenumeration.|
   |Slutpunkt|Meddelandetjänster| |
   |SharedAccessPolicyName|iothubowner| |

## <a name="define-an-output"></a>Definiera utdata

1. Välj **Ctrl + Skift + P** att öppna kommandopaletten. Ange sedan **ASA: Lägg till utdata**.

   ![Lägg till Stream Analytics-utdata i VS Code](./media/quick-create-vs-code/add-output.png)

2. Välj **Blob Storage** för mottagartyp.

3. Välj fråga ASA-skriptet som ska använda denna indata.

4. Ange namnet på utdatafilen som **BlobStorage.json**.

5. Redigera **BlobStorage.json** med följande värden. Behåll standardvärdena för fält som inte nämns nedan. Använd CodeLens för att ange en sträng eller välj en listruta.

   |Inställning|Föreslaget värde|Beskrivning|
   |-------|---------------|-----------|
   |Namn|Resultat| Ange ett namn som identifierar jobbets utdata.|
   |Lagringskonto|asaquickstartstorage|Välj eller ange namnet på ditt lagringskonto. Lagringskontonamn identifieras automatiskt om de skapas i samma prenumeration.|
   |Container|container1|Välj en befintlig container som du skapade i ditt lagringskonto.|
   |Sökvägsmönster|utdata|Ange namnet för en sökväg som ska skapas i containern.|

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

1. Öppna **myASAproj.asaql** i projektmappen.

2. Lägg till följande fråga:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="compile-the-script"></a>Kompilera skriptet

Kompilering av skriptet gör två saker: Kontrollera syntax och skapa Azure Resource Manager-mallar för autodeployment.

Det finns två sätt att utlösa kompilering av skriptet:

1. Väljer du skriptet från arbetsytan och sedan sätts kompilera från kommandopaletten. 

   ![Använda VS Code kommandopaletten för att kompilera skriptet](./media/quick-create-vs-code/compile-script1.png)

2. Högerklicka på skriptet och välj **ASA: kompilera skriptet**.

    ![Högerklicka på ASA-skriptet för att kompilera](./media/quick-create-vs-code/compile-script2.png)

3. Efter kompilering, du kan hitta de två genererade Azure Resource Manager-mallarna i **distribuera** mapp i ditt projekt. Dessa två filer används för autodeployment.

    ![Stream Analytics-distributionsmallar i Utforskaren](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Skicka ett Stream Analytics-jobb till Azure

1. I skriptet redigeraren fönstret i Visual Studio Code, väljer **Välj från dina prenumerationer**.

   ![Välj från dina prenumerationer text i Skriptredigeraren](./media/quick-create-vs-code/select-subscription.png)

2. Välj din prenumeration från listan över popup-fönstret.

3. Välj jobb **. Välj sedan skapa ett nytt jobb.

4. Ange Jobbnamnet på din **myASAjob** och följ sedan anvisningarna för att välja den resursgrupp och plats.

5. Välj **skicka till Azure**. Loggarna finns i utdatafönstret. 

6. När jobbet har skapats kan se du den i Stream Analytics Explorer.

## <a name="run-the-iot-simulator"></a>Köra IoT-simulatorn

1. Öppna [Raspberry Pi Azure IoT-onlinesimulatorn](https://azure-samples.github.io/raspberry-pi-web-simulator/) i en ny webbläsarflik eller ett fönster.

2. Ersätt platshållaren på rad 15 med Azure IoT Hub-enhetens anslutningssträng, som du sparade i föregående avsnitt.

3. Klicka på **Run** (Kör). Utdata bör visas de sensordata och meddelanden som skickas till din IoT-hubb.

   ![Raspberry Pi Azure IoT-onlinesimulator](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

1. Öppna **Stream Analytics Explorer** i Visual Studio Code och Sök efter ditt jobb **myASAJob**.

2. Högerklicka på jobbnamnet. Välj **starta** på snabbmenyn.

![Starta Stream Analytics-jobbet i VS Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Välj **nu** i popup-fönster för att starta jobbet.

4. Obs jobbet har statusen har ändrats till **kör**. Högerklicka på jobbnamnet och välj **öppna Jobbvy i portalen** att se indata och utdata händelse mått. Den här åtgärden kan ta några minuter.

5. Om du vill visa resultatet genom att öppna blob-lagringen i Visual Studio Code-tillägget eller i Azure-portalen.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure-portalen och välj sedan namnet på den resurs du skapade.  

2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen som ska tas bort i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har distribuerat du ett enkelt Stream Analytics-jobb med hjälp av Visual Studio Code. Du kan också distribuera Stream Analytics-jobb med den [Azure-portalen](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md), och Visual Studio (stream-analytics-Snabbstart – skapa – vs.md). 

Fortsätt till följande artikel om du vill veta mer om Azure Stream Analytics-verktyg för Visual Studio:

> [!div class="nextstepaction"]
> [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)