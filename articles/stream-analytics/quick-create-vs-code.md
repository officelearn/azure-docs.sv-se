---
title: Snabbstart – skapa ett Azure Stream Analytics-jobb i Visual Studio-kod
description: Den här snabbstarten visar hur du kommer igång genom att skapa ett Stream Analytics-jobb, konfigurera indata och utdata och definiera en fråga med Visual Studio-kod.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: e3e878b4f548da64ab04eb079d51b0846cf35c57
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76313883"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Snabbstart: Skapa ett Azure Stream Analytics-jobb i Visual Studio-kod (förhandsversion)

Den här snabbstarten visar hur du skapar och kör ett Azure Stream Analytics-jobb med hjälp av Azure Stream Analytics Tools-tillägget för Visual Studio-kod. Exempeljobbet läser strömmande data från en Azure IoT Hub-enhet. Du definierar ett jobb som beräknar medeltemperaturen när den överstiger 27° och skriver utdatahändelser till en ny fil i Blob Storage.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure-portalen](https://portal.azure.com/).

* Installera [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Installera tillägget Azure Stream Analytics Tools

1. Öppna Visual Studio Code.

2. Sök efter **Stream Analytics** **från Tillägg** i den vänstra rutan och välj **Installera** i tillägget Azure Stream **Analytics Tools.**

3. När tillägget har installerats kontrollerar du att **Azure Stream Analytics Tools** visas i **Aktiverade tillägg**.

   ![Azure Stream Analytics Tools under aktiverade tillägg i Visual Studio-kod](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Aktivera tillägget Azure Stream Analytics Tools

1. Välj **Azure-ikonen** i aktivitetsfältet Visual Studio-kod. Under **Stream Analytics** i sidofältet väljer du Logga in på **Azure**.

   ![Logga in på Azure i Visual Studio-kod](./media/quick-create-vs-code/azure-sign-in.png)

2. När du är inloggad visas ditt Azure-kontonamn i statusfältet i det nedre vänstra hörnet i Fönstret Visual Studio-kod.

> [!NOTE]
> Azure Stream Analytics Tools-tillägget loggar automatiskt in dig nästa gång om du inte loggar ut. Om ditt konto har tvåfaktorsautentisering rekommenderar vi att du använder telefonautentisering i stället för att använda en PIN-kod.
> Om du har problem med att lista resurser hjälper det vanligtvis att logga ut och logga in igen. Om du vill logga `Azure: Sign Out`ut anger du kommandot .

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics-jobbet bör du förbereda de data som senare konfigureras som jobbindata. Så här förbereder du indata som jobbet kräver:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj Skapa ett**resurs-Internet** >  **Create a resource** > för**saker IoT Hub**.

3. I rutan **IoT-hubb** anger du följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Prenumeration  | \<Din prenumeration\> |  Ange den prenumeration som du vill använda. |
   |Resursgrupp   |   asaquickstart-resourcegroup  |   Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto. |
   |Region  |  \<Välj den region som är närmast dina användare\> | Välj en geografisk plats där du kan vara värd för din IoT-hubb. Använd den plats som är närmast dina användare. |
   |IoT-hubbnamn  | MyASAIoTHub  |   Välj ett namn för din IoT-hubb.   |

   ![Skapa en IoT Hub](./media/quick-create-vs-code/create-iot-hub.png)

4. Välj **Nästa: Ange storlek och skala**.

5. Gör ett val för **pris- och skalnivå**. För den här snabbstarten väljer du nivån **F1 – kostnadsfri** om den fortfarande är tillgänglig för din prenumeration. Om den kostnadsfria nivån inte är tillgänglig väljer du den lägsta tillgängliga nivån. Mer information finns i [Azure IoT Hub-priser](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Storlek och skala din IoT-hubb](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Välj **Granska + skapa**. Granska din IoT-hubbinformation och välj **Skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i fönstret **Meddelanden.**

7. På navigeringsmenyn för IoT-hubben väljer du **Lägg till** under **IoT-enheter**. Lägg till ett ID för **enhets-ID**och välj **Spara**.

   ![Lägga till en enhet i IoT-hubben](./media/quick-create-vs-code/add-device-iot-hub.png)

8. När enheten har skapats öppnar du enheten från **IoT-enhetslistan.** Kopiera strängen i **Anslutningssträngen (primärnyckel)** och spara den på ett anteckningsblock som ska användas senare.

   ![Kopiera anslutningssträng för IoT-hubbenheter](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Köra IoT-simulatorn

1. Öppna [Raspberry Pi Azure IoT-onlinesimulatorn](https://azure-samples.github.io/raspberry-pi-web-simulator/) i en ny webbläsarflik eller ett fönster.

2. Ersätt platshållaren på linje 15 med anslutningssträngen för IoT-hubbenheter som du sparade tidigare.

3. Välj **Kör**. Utdata ska visa sensordata och meddelanden som skickas till din IoT-hubb.

   ![Raspberry Pi Azure IoT Online Simulator med utdata](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Skala bloblagring

1. Välj Skapa ett > **resurslagringslagringskonto** > **Storage account**i det övre vänstra hörnet på Azure-portalen . **Create a resource**

2. I fönsterrutan **Skapa lagringskonto** anger du namn, plats och resursgrupp för lagringskonto. Välj samma plats och resursgrupp som IoT-hubben som du skapade. Välj sedan **Granska + skapa** för att skapa kontot.

   ![Skapa lagringskonto](./media/quick-create-vs-code/create-storage-account.png)

3. När ditt **lagringskonto** har skapats väljer du panelen Blobbar i **fönstret Översikt.**

   ![Översikt över lagringskonto](./media/quick-create-vs-code/blob-storage.png)

4. Från sidan **Blob Service** väljer du **Container** och anger ett namn för containern, till exempel **container1**. Lämna **offentlig åtkomstnivå** som **privat (ingen anonym åtkomst)** och välj **OK**.

   ![Skapa en blobcontainer](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. Öppna kommandopaletten **i** Visual Studio-kod. Ange sedan **ASA** och välj **ASA: Skapa nytt projekt**.

   ![Skapa ett nytt projekt](./media/quick-create-vs-code/create-new-project.png)

2. Ange ditt projektnamn, till exempel **myASAproj,** och välj en mapp för projektet.

    ![Skapa ett projektnamn](./media/quick-create-vs-code/create-project-name.png)

3. Det nya projektet läggs till på arbetsytan. Ett Stream Analytics-projekt består av tre mappar: **Indata,** **Utdata**och **Funktioner**. Den har också frågeskriptet **(*.asaql),** en **JobConfig.json-fil** och en **asaproj.json-konfigurationsfil.**

    Konfigurationsfilen **asaproj.json** innehåller den information om indata, utdata och jobbkonfiguration som behövs för att skicka Stream Analytics-jobbet till Azure.

    ![Stream Analytics-projektfiler i Visual Studio-kod](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> När du lägger till in- och utdata från kommandopaletten läggs motsvarande banor automatiskt till **asaproj.json.** Om du lägger till eller tar bort indata eller utdata på disken direkt måste du manuellt lägga till eller ta bort dem från **asaproj.json**. Du kan välja att placera indata och utdata på ett ställe och sedan referera till dem i olika jobb genom att ange sökvägarna i varje **asaproj.json-fil.**

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

1. Öppna **myASAproj.asaql** från projektmappen.

2. Lägg till följande fråga:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="test-the-query-locally-with-sample-data"></a>Testa frågan lokalt med exempeldata

Innan du kör frågan i molnet kan du testa den lokalt med en lokal exempeldatafil eller med data som hämtas från liveindata för att verifiera frågelogiken.

Följ instruktionerna i [Testfrågor lokalt med exempeldata](visual-studio-code-local-run.md) för mer information.

 ![Testa med exempeldata i Visual Studio-kod](./media/vscode-local-run/localrun-localinput.gif)

## <a name="define-a-live-input"></a>Definiera en live-inmatning

1. Högerklicka på mappen **Ingångar** i Stream Analytics-projektet. Välj sedan **ASA: Lägg till indata** på snabbmenyn.

    ![Lägga till indata från mappen Ingångar](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Du kan också välja **Ctrl+Skift+P** för att öppna kommandopaletten och ange **ASA: Lägg till indata**.

   ![Lägga till Stream Analytics-indata i Visual Studio-kod](./media/quick-create-vs-code/add-input.png)

2. Välj **IoT Hub** för indatatypen.

   ![Välj IoT-hubb som indataalternativ](./media/quick-create-vs-code/iot-hub.png)

3. Om du har lagt till indata från kommandopaletten väljer du det Stream Analytics-frågeskript som ska använda indata. Det bör fyllas i automatiskt med filsökvägen till **myASAproj.asaql**.

   ![Välja ett Stream Analytics-skript i Visual Studio-kod](./media/quick-create-vs-code/asa-script.png)

4. Välj **Välj bland dina Azure-prenumerationer** på den nedrullningsbara menyn.

    ![Välj bland prenumerationer](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Redigera den nyligen genererade **IoTHub1.json-filen** med följande värden. Behåll standardvärden för fält som inte nämns här.

   |Inställning|Föreslaget värde|Beskrivning|
   |-------|---------------|-----------|
   |Namn|Indata|Ange ett namn för att identifiera jobbets indata.|
   |IotHubNamespace|MyASAIoTHub|Välj eller ange namnet på din IoT-hubb. IoT-hubbnamn identifieras automatiskt om de skapas i samma prenumeration.|
   |SharedAccessPolicyName|iothubowner| |

   Du kan använda funktionen CodeLens för att ange en sträng, välja från en listruta eller ändra texten direkt i filen. Följande skärmbild visar **Välj bland dina prenumerationer** som ett exempel. Autentiseringsuppgifterna visas automatiskt och sparas i lokal autentiseringshanterare.

   ![Konfigurera indata i Visual Studio-kod](./media/quick-create-vs-code/configure-input.png)

   ![Konfigurera indatavärde i Visual Studio-kod](./media/quick-create-vs-code/configure-input-value.png)

## <a name="preview-input"></a>Förhandsgranska indata

Välj **Förhandsgranska data** i **IoTHub1.json** från den översta raden. Vissa indata hämtas från IoT-hubben och visas i förhandsgranskningsfönstret. Den här processen kan ta ett tag.

 ![Förhandsgranska liveinmatning](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Definiera en utdata

1. Välj **Ctrl+Skift+P** om du vill öppna kommandopaletten. Ange sedan **ASA: Lägg till utdata**.

   ![Lägga till Stream Analytics-utdata i Visual Studio-kod](./media/quick-create-vs-code/add-output.png)

2. Välj **Blob Storage** för handfattypen.

3. Välj det Stream Analytics-frågeskript som ska använda den här indata.

4. Ange utdatafilnamnet som **BlobStorage**.

5. Redigera **BlobStorage** med hjälp av följande värden. Behåll standardvärden för fält som inte nämns här. Använd funktionen CodeLens för att välja från en listruta eller ange en sträng.

   |Inställning|Föreslaget värde|Beskrivning|
   |-------|---------------|-----------|
   |Namn|Resultat| Ange ett namn för att identifiera projektets utdata.|
   |Lagringskonto|asaquickstartstorage|Välj eller ange namnet på ditt lagringskonto. Namn på lagringskonto identifieras automatiskt om de skapas i samma prenumeration.|
   |Container|container1|Välj en befintlig container som du skapade i ditt lagringskonto.|
   |Sökvägsmönster|utdata|Ange namnet för en sökväg som ska skapas i containern.|

   ![Konfigurera utdata i Visual Studio-kod](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Kompilera skriptet

Syntaxen för skriptkompileringskontroller och genererar Azure Resource Manager-mallarna för automatisk distribution.

Det finns två sätt att utlösa skriptkompilering:

- Markera skriptet från arbetsytan och kompilera sedan från kommandopaletten.

   ![Använda kommandopaletten Visual Studio Code för att kompilera skriptet](./media/quick-create-vs-code/compile-script1.png)

- Högerklicka på skriptet och välj **ASA: Kompilera skript**.

    ![Högerklicka på Stream Analytics-skriptet för att kompilera](./media/quick-create-vs-code/compile-script2.png)

Efter kompileringen hittar du de två genererade Azure Resource Manager-mallarna i mappen **Distribuera** i projektet. Dessa två filer används för automatisk distribution.

![Strömma analytics-distributionsmallar i Utforskaren](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Skicka ett Stream Analytics-jobb till Azure

1. I skriptredigerarens fönster i frågeskriptet väljer du **Skicka till Azure**.

   ![Välj från din prenumerationstext i skriptredigeraren](./media/quick-create-vs-code/submit-job.png)

2. Välj din prenumeration i popup-listan.

3. Välj **Välj ett jobb**. Välj sedan **Skapa nytt jobb**.

4. Ange ditt jobbnamn, **myASAjob**. Följ sedan instruktionerna för att välja resursgrupp och plats.

5. Välj **Skicka till Azure**. Du hittar loggarna i utdatafönstret. 

6. När jobbet skapas kan du se det i **Stream Analytics Explorer**.

    ![Listat jobb i Stream Analytics Explorer](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

1. Öppna **Stream Analytics Explorer** i Visual Studio-kod och hitta ditt jobb, **myASAJob**.

2. Högerklicka på jobbnamnet. Välj sedan **Start** på snabbmenyn.

   ![Starta Stream Analytics-jobbet i Visual Studio-kod](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Välj **Nu** i popup-fönstret för att starta jobbet.

4. Observera att jobbstatusen har ändrats till **Kör**. Högerklicka på jobbnamnet och välj **Öppna jobbvy i portalen** om du vill visa indata- och utdatahändelsemåtten. Den här åtgärden kan ta några minuter.

5. Om du vill visa resultatet öppnar du blob-lagringen i Tillägget Visual Studio-kod eller i Azure-portalen.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resursgruppen, direktuppspelningsjobbet och alla relaterade resurser. Om du tar bort jobbet undviks fakturering av streamingenheter som jobbet förbrukar. 

Om du planerar att använda jobbet i framtiden kan du stoppa det och starta om det senare. Om du inte ska använda det här jobbet igen gör du så här för att ta bort alla resurser som du har skapat i den här snabbstarten:

1. På den vänstra menyn i Azure-portalen väljer du **Resursgrupper** och väljer sedan namnet på den resurs som du skapade.  

2. Välj **Ta bort** på din resursgruppssida. Ange namnet på den resurs som ska tas bort i textrutan och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett enkelt Stream Analytics-jobb med hjälp av Visual Studio Code. Du kan också distribuera Stream Analytics-jobb med hjälp av [Azure-portalen,](stream-analytics-quick-create-portal.md) [PowerShell](stream-analytics-quick-create-powershell.md)och [Visual Studio](stream-analytics-quick-create-vs.md).

Om du vill veta mer om Azure Stream Analytics Tools för Visual Studio Code fortsätter du till följande artiklar:

* [Testa Azure Stream Analytics-jobb lokalt mot liveindata med Visual Studio-kod](visual-studio-code-local-run-live-input.md)

* [Använda Visual Studio-kod för att visa Azure Stream Analytics-jobb](visual-studio-code-explore-jobs.md)

* [Konfigurera CI/CD-pipelines med hjälp av npm-paketet](setup-cicd-vs-code.md)
