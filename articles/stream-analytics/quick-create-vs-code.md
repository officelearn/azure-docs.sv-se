---
title: Skapa ett Azure Stream Analytics moln jobb i Visual Studio Code (för hands version)
description: Den här snabb starten visar hur du kommer igång genom att skapa ett Stream Analytics jobb, konfigurera indata, utdata och definiera en fråga med Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 3301be3a067982cb90e663fe3782319eb0b90ba0
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673136"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Snabbstart: Skapa ett Azure Stream Analytics moln jobb i Visual Studio Code (för hands version)

Den här snabb starten visar hur du skapar och kör ett Stream Analytics jobb med hjälp av Azure Stream Analytics tillägget för Visual Studio Code. Exempeljobbet läser strömmande data från en IoT Hub-enhet. Du definierar ett jobb som beräknar medeltemperaturen när den överstiger 27° och skriver utdatahändelser till en ny fil i Blob Storage.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure Portal](https://portal.azure.com/).

* Installera [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-extension"></a>Installera Azure Stream Analytics-tillägget

1. Öppna Visual Studio Code.

2. Från **tillägg** i den vänstra rutan söker du efter **Stream Analytics** och väljer **Installera** i **Azure Stream Analytics** tillägget.

3. När tillägget har installerats kontrollerar du att **Azure Stream Analytics verktyg** är synligt i dina **aktiverade tillägg**.

   ![Azure Stream Analytics verktyg under aktiverade tillägg i Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Aktivera Azure Stream Analytics-tillägget

1. Välj **Azure** -ikonen i AKTIVITETS fältet vs-kod. **Stream Analytics** visas i sido fältet. Under **Stream Analytics**väljer **du logga in på Azure**. 

   ![Logga in på Azure i Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. När du är inloggad visas namnet på ditt Azure-konto i statusfältet i det nedre vänstra hörnet i VS Code-fönstret.

> [!NOTE]
> Azure Stream Analytics verktyg loggas automatiskt in nästa gången om du inte loggar ut. Om ditt konto har tvåfaktorautentisering, rekommenderar vi att du använder autentisering i stället för att använda en PIN-kod.
> Om du har problem med att lista resurser kan du logga ut och logga in igen i allmänhet. Logga ut genom att ange kommandot `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics-jobbet bör du förbereda de data som senare konfigureras som jobbindata. Förbered de indata som krävs för jobbet genom att utföra följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Välj **Skapa en resurs** > **Sakernas internet** > **IoT Hub**.

3. I rutan **IoT-hubb** anger du följande information:
   
   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Subscription  | \<Din prenumeration\> |  Ange den prenumeration som du vill använda. |
   |Resource group   |   asaquickstart-resourcegroup  |   Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto. |
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

   ![Skapa blobbcontainern](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. Tryck på **CTRL + SKIFT + P** i Visual Studio Code för att öppna kommando paletten. Skriv sedan **ASA** och välj **ASA: Skapa ett nytt projekt @ no__t-0.

   ![Skapa ett nytt projekt](./media/quick-create-vs-code/create-new-project.png)

2. Ange ditt projekt namn som **myASAproj** och välj en mapp för projektet.

    ![Skapa projekt namn](./media/quick-create-vs-code/create-project-name.png)

3. Det nya projektet kommer att läggas till i din arbets yta. Ett ASA-projekt består av frågans skript **(*. asaql)** , en **JobConfig. JSON** -fil och en **asaproj. JSON** -konfigurationsfil.

   ![Stream Analytics projektfiler i VS Code](./media/quick-create-vs-code/asa-project-files.png)

4. Konfigurations filen **asaproj. JSON** innehåller indata, utdata och information om jobb konfigurations filen som krävs för att skicka Stream Analytics jobb till Azure.

   ![Stream Analytics jobb konfigurations fil i VS Code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> När du lägger till indata och utdata från paletten kommando, läggs motsvarande sökvägar till i **asaproj. JSON** automatiskt. Om du lägger till eller tar bort indata eller utdata på disken direkt måste du manuellt lägga till eller ta bort dem från **asaproj. JSON**. Du kan välja att placera indata och utdata på en plats och sedan referera till dem i olika jobb genom att ange Sök vägarna i varje **asaproj. JSON**.

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

1. Öppna **myASAproj. asaql** från projektmappen.

2. Lägg till följande fråga:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```
## <a name="test-with-sample-data"></a>Testa med exempeldata
Innan du kör frågan i molnet kan du testa din fråga med lokala exempel data för att verifiera fråge logiken.

Följ anvisningarna i [testa med exempel data](vscode-local-run.md) för mer information. 

 ![Testa med exempel data i VS Code](./media/quick-create-vs-code/vscode-localrun.gif)

## <a name="define-an-input"></a>Definiera indata

1. Välj **Ctrl + Shift + P** för att öppna kommando-paletten och ange **ASA: Lägg till inmatade @ no__t-0.

   ![Lägg till Stream Analytics in VS Code](./media/quick-create-vs-code/add-input.png)

2. Välj **IoT Hub** för indatatypen.

   ![Välj IoT Hub som indatamängds alternativ](./media/quick-create-vs-code/iot-hub.png)

3. Välj det ASA-frågeuttryck som ska använda indatamängden. Den ska automatiskt fylla i med sökvägen till filen **myASAproj. asaql**.

   ![Välj ett ASA-skript i Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Ange indatafilens namn som **IotHub**.

5. Redigera **IoTHub. JSON** med följande värden. Behåll standardvärdena för fält som inte nämns nedan. Du kan använda CodeLens för att ange en sträng, välja från en listruta eller ändra texten direkt i filen.

   |Inställning|Föreslaget värde|Beskrivning|
   |-------|---------------|-----------|
   |Name|Indata|Ange ett namn som identifierar jobbets indata.|
   |IotHubNamespace|MyASAIoTHub|Välj eller ange namnet på IoT-hubben. IoT-hubbnamn identifieras automatiskt om de skapas i samma prenumeration.|
   |SharedAccessPolicyName|iothubowner| |

   ![Konfigurera indatatyper i Visual Studio Code](./media/quick-create-vs-code/configure-input.png)



## <a name="define-an-output"></a>Definiera utdata

1. Välj **Ctrl + Shift + P** för att öppna kommando paletten. Ange sedan **ASA: Lägg till utdata @ no__t-0.

   ![Lägg till Stream Analytics utdata i VS Code](./media/quick-create-vs-code/add-output.png)

2. Välj **Blob Storage** för typ av mottagare.

3. Välj det ASA-frågeuttryck som ska använda den här indatamängden.

4. Ange namnet på utdatafilen som **BlobStorage**.

5. Redigera **BlobStorage** med följande värden. Behåll standardvärdena för fält som inte nämns nedan. Använd CodeLens för att hjälpa dig att välja i en listruta eller ange en sträng. 

   |Inställning|Föreslaget värde|Beskrivning|
   |-------|---------------|-----------|
   |Name|Output| Ange ett namn för att identifiera jobbets utdata.|
   |Lagringskonto|asaquickstartstorage|Välj eller ange namnet på ditt lagrings konto. Lagringskontonamn identifieras automatiskt om de skapas i samma prenumeration.|
   |Container|container1|Välj en befintlig container som du skapade i ditt lagringskonto.|
   |Sökvägsmönster|utdata|Ange namnet för en sökväg som ska skapas i containern.|

 ![Konfigurera utdata i Visual Studio Code](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Kompilera skriptet

Skript kompileringen gör två saker: kontrol lera syntaxen och generera Azure Resource Manager mallar för Autodistribution.

Det finns två sätt att utlösa skript kompilering:

1. Välj skriptet från arbets ytan och Utlös sedan kompilering från kommando-paletten. 

   ![Använd kommando paletten VS Code för att kompilera skriptet](./media/quick-create-vs-code/compile-script1.png)

2. Högerklicka på skriptet och välj **ASA: kompilera skript**.

    ![Högerklicka på det ASA-skript som ska kompileras](./media/quick-create-vs-code/compile-script2.png)

3. Efter kompileringen kan du hitta de två genererade Azure Resource Manager mallar i **Deploy** -mappen i projektet. De här två filerna används för Autodistribution.

    ![Mallar för Stream Analytics distribution i Utforskaren](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Skicka ett Stream Analytics jobb till Azure

1. I fönstret skript redigeraren i Visual Studio Code väljer du **Välj bland dina prenumerationer**.

   ![Välj från din prenumerations text i skript redigeraren](./media/quick-create-vs-code/select-subscription.png)

2. Välj din prenumeration i popup-listan.

3. Välj ett jobb * *. Välj sedan skapa ett nytt jobb.

4. Ange ditt jobb namn, **myASAjob** och följ sedan anvisningarna för att välja resurs grupp och plats.

5. Välj **Skicka till Azure**. Loggarna finns i fönstret utdata. 

6. När jobbet har skapats kan du se det i **Stream Analytics Explorer**.

![List jobb i Stream Analytics Explorer](./media/quick-create-vs-code/list-job.png)


## <a name="run-the-iot-simulator"></a>Köra IoT-simulatorn

1. Öppna [Raspberry Pi Azure IoT-onlinesimulatorn](https://azure-samples.github.io/raspberry-pi-web-simulator/) i en ny webbläsarflik eller ett fönster.

2. Ersätt platshållaren på rad 15 med Azure IoT Hub-enhetens anslutningssträng, som du sparade i föregående avsnitt.

3. Klicka på **Run** (Kör). Utdata bör visas de sensordata och meddelanden som skickas till din IoT-hubb.

   ![Raspberry Pi Azure IoT-onlinesimulator](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

1. Öppna **Stream Analytics Explorer** i Visual Studio Code och hitta jobbet, **myASAJob**.

2. Högerklicka på jobb namnet. Välj sedan **Starta** från snabb menyn.

   ![Starta Stream Analytics jobb i VS Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Välj **nu** i popup-fönstret för att starta jobbet.

4. Observera att jobb statusen har ändrats till att **köras**. Högerklicka på jobb namnet och välj **Öppna jobb visning i portalen** för att se händelse mått för indata och utdata. Den här åtgärden kan ta några minuter.

5. Om du vill visa resultaten öppnar du blob-lagringen i Visual Studio Code-tillägget eller i Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure-portalen och välj sedan namnet på den resurs du skapade.  

2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen som ska tas bort i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett enkelt Stream Analytics-jobb med Visual Studio Code. Du kan också distribuera Stream Analytics jobb med hjälp av [Azure Portal](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md)och Visual Studio (Stream-Analytics-Quick-Create-vs.MD). 

Fortsätt till följande artikel om du vill veta mer om Azure Stream Analytics-verktyg för Visual Studio:

> [!div class="nextstepaction"]
> [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
