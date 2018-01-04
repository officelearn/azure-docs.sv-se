---
title: "Snabbstart för installation av Azure Machine Learning-tjänster | Microsoft Docs"
description: "Den här snabbstarten visar hur du skapar resurser i Azure Machine Learning och hur du installerar Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/13/2017
ms.openlocfilehash: 9a2648d800b803bf0d9d565a806e627c49bb1a00
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Skapa konton för förhandsversionen av Azure Machine Learning och installera Azure Machine Learning Workbench
Azure Machine Learning-tjänster (förhandsversion) är en integrerad lösning från slutpunkt till slutpunkt för avancerade analyser inom dataforskning. Med den här lösningen kan professionella dataforskare få hjälp med att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här snabbstarten visar hur du skapar konton för att hantera experiment och modeller i förhandsversionen av Azure Machine Learning. Den visar också hur du installerar skrivbordsprogrammet Azure Machine Learning Workbench och CLI-verktyg. Sedan får du en snabb genomgång av funktionerna i förhandsversionen av Azure Machine Learning. Använd [Iris-datamängden](https://en.wikipedia.org/wiki/iris_flower_data_set) för att skapa en modell som förutspår typen av iris baserad på några av dess fysiska egenskaper.  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För närvarande kan skrivbordsappen Azure Machine Learning Workbench endast installeras på följande operativsystem: 
- Windows 10
- Windows Server 2016
- macOS Sierra
- macOS High Sierra

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-azure-machine-learning-accounts"></a>Skapa Azure Machine Learning-konton
Använd Azure Portal för att etablera Azure Machine Learning-konton: 
1. Klicka på knappen **Nytt** (+) i det övre vänstra hörnet i portalen.

2. Skriv **Machine Learning** i sökfältet. Välj **Machine Learning Experimentation (preview)** i sökresultatet.  Klicka på stjärnikonen om du vill lägga till det här alternativet som favorit i Azure Portal.

   ![Azure Machine Learning-sökning](media/quickstart-installation/portal-more-services.png)

3. Välj **+ Lägg till** för att konfigurera ett nytt Machine Learning-experimenteringskonto. Ett detaljerat formulär öppnas.

   ![Machine Learning-experimenteringskonto](media/quickstart-installation/portal-create-experimentation.png)

4. Fyll i Machine Learning-experimenteringsformuläret med följande information:

   Inställning|Föreslaget värde|Beskrivning
   ---|---|---
   Namn på experimenteringskonto | _Unikt namn_ |Välj ett unikt namn som identifierar kontot. Du kan till exempel använda ditt eget namn eller namnet på din avdelning eller ditt projekt. Välj det som bäst identifierar experimentet. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-). 
   Prenumeration | _Din prenumeration_ |Välj den Azure-prenumeration som ska användas för experimentet. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
   Resursgrupp | _Din resursgrupp_ | Du kan skapa ett nytt resursgruppnamn eller använda ett befintligt namn i prenumerationen.
   Plats | _Regionen som ligger närmast dina användare_ | Välj den plats som är närmast användarna och dataresurserna.
   Antal platser | 2 | Ange antalet platser. Det här valet påverkar [priset](https://azure.microsoft.com/pricing/details/machine-learning/). De första två platserna är kostnadsfria. I den här snabbstarten använder vi två platser. Du kan uppdatera antalet platser vid ett senare tillfälle i Azure Portal.
   Lagringskonto | _Unikt namn_ | Välj **Skapa nytt** och ange ett namn för att skapa ett Azure-lagringskonto. Alternativt kan du välja **Använd befintlig** och välja ett befintligt lagringskonto i listrutan. Lagringskontot är obligatoriskt. Det används för att lagra projektartefakter och köra historikdata. 
   Arbetsyta för experimenteringskonto | _Unikt namn_ | Ange ett namn för den nya arbetsytan. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-).
   Tilldela ägare för arbetsytan | _Ditt konto_ | Välj ditt eget konto som ägare till arbetsytan.
   Skapa modellhanteringskonto | *markering* | När du skapar experimenteringskontot kan du också skapa Machine Learning-modellhanteringskontot. Den här resursen används när du är redo att distribuera och hantera dina modeller som realtidswebbtjänster. Vi rekommenderar att du skapar modellhanteringskontot samtidigt som experimenteringskontot.
   Kontonamn | _Unikt namn_ | Välj ett unikt namn som identifierar modellhanteringskontot. Du kan till exempel använda ditt eget namn eller namnet på din avdelning eller ditt projekt. Välj det som bäst identifierar experimentet. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-). 
   Prisnivå för modellhantering | **DEVTEST** | Välj **Ingen prisnivå har valts** för att ange prisnivån för ditt nya modellhanteringskonto. Du kan spara pengar genom att välja prisnivån **DEVTEST**, om den är tillgänglig för din prenumeration (begränsad tillgänglighet). I annat fall väljer du prisnivån S1 för att spara pengar. Klicka på **Välj** för att spara ditt val av prisnivå. 
   Fäst vid instrumentpanelen | _markering_ | Markera alternativet **Fäst på instrumentpanelen** så att du enkelt kan spåra Machine Learning-experimenteringskontot på instrumentpanelen i Azure Portal.

5. Välj **Skapa** för att påbörja skapandeprocessen.

6. Klicka på **Aviseringar** (klockikonen) i verktygsfältet i Azure Portal för att övervaka distributionsprocessen. 

   Meddelandet **Distribution pågår** visas. Status ändras till **Distribueringen lyckades** när distributionen är klar. Sidan med Machine Learning-experimenteringskontot öppnas vid en lyckad distribution.
   
   ![Meddelanden i Azure Portal](media/quickstart-installation/portal-notification.png)

Installera Azure Machine Learning Wordbench på datorn enligt ett av de två avsnitten nedan, beroende på vilket operativsystem du har på din lokala dator. 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Installera Azure Machine Learning Workbench på Windows
Installera Azure Machine Learning Workbench på en dator som kör Windows 10, Windows Server 2016 eller senare.

1. Ladda ned det senaste installationsprogrammet för Azure Machine Learning Workbench: [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).

2. Dubbelklicka på installationsprogrammet **AmlWorkbenchSetup.msi** i Utforskaren.

   >[!IMPORTANT]
   >Ladda ned installationsprogrammet till hårddisken och kör programmet därifrån. Kör inte installationsprogrammet direkt i webbläsarens nedladdningswidget.

3. Slutför installationen enligt anvisningarna på skärmen.

   Installationsprogrammet laddar ned alla beroendekomponenter som behövs, till exempel Python, Miniconda och andra relaterade bibliotek. Det kan ta ungefär en halvtimme att hämta alla komponenter och slutföra installationen. 

4. Azure Machine Learning Workbench installeras i följande katalog:
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>Installera Azure Machine Learning Workbench på macOS
Installera Azure Machine Learning Workbench på en dator som kör macOS Sierra eller senare.

1. Ladda ned det senaste installationsprogrammet för Azure Machine Learning Workbench: [AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg).

   >[!IMPORTANT]
   >Ladda ned installationsprogrammet till hårddisken och kör programmet därifrån. Kör inte installationsprogrammet direkt i webbläsarens nedladdningswidget.

2. Dubbelklicka på installationsprogrammet **AmlWorkbench.dmg** från Finder.

3. Slutför installationen enligt anvisningarna på skärmen.

   Installationsprogrammet laddar ned alla beroendekomponenter som behövs, till exempel Python, Miniconda och andra relaterade bibliotek. Det kan ta ungefär en halvtimme att hämta alla komponenter och slutföra installationen. 

4. Azure Machine Learning Workbench installeras i följande katalog: 

   `/Applications/Azure ML Workbench.app`

## <a name="run-azure-machine-learning-workbench-to-sign-in-for-the-first-time"></a>Kör Azure Machine Learning Workbench och logga in för första gången
1. När installationen har slutförts klickar du på knappen **Launch Workbench** (Starta Workbench) på den sista skärmen i installationsprogrammet. Om du har stängt installationsprogrammet kan du starta appen genom att välja genvägen till **Azure Machine Learning Workbench** på skrivbordet eller **Start**-menyn.

2. Logga in på Workbench med samma konto som du använde tidigare för att etablera Azure-resurser. 

3. När du har loggat in försöker Workbench hitta de Machine Learning-experimenteringskonton som du skapade tidigare. Programmet söker efter alla Azure-prenumerationer som du har åtkomst till med dina inloggningsuppgifter. När minst ett experimenteringskonto har hittats öppnas Workbench med det kontot. Därefter visas de arbetsytor och projekt som hittats i det kontot. 

   >[!TIP]
   > Om du har åtkomst till mer än ett experimenteringskonto kan du växla till ett annat konto genom att välja avatarikonen i det nedre vänstra hörnet av Workbench.

Mer information om hur du skapar en miljö för att distribuera webbtjänster finns i [Deployment Environment Setup](deployment-setup-configuration.md) (Konfiguration av distributionsmiljön).

## <a name="create-a-new-project"></a>Skapa ett nytt projekt
1. Starta appen Azure Machine Learning Workbench och logga in. 

2. Klicka på **File** (Arkiv) > **New Project** (Nytt projekt) (eller klicka på tecknet **+** i fönstret **PROJECTS** (PROJEKT)). 

3. Fyll i fälten **Project name** (Projektnamn) och **Project directory** (Projektkatalog). **Project description** (Projektbeskrivning) är valfritt, men användbart. Lämna fältet **Visualstudio.com GIT Repository URL** (URL till GIT-lagringsplats för Visualstudio.com) tomt tills vidare. Välj en arbetsyta och sedan **Classifying Iris** (Klassificera Iris) som projektmall.

   >[!TIP]
   >Alternativt kan du gå skriva i URL:en till en GIT-lagringsplats som finns i ett [VSTS](https://www.visualstudio.com)-projekt (Visual Studio Team Service) i textfältet för GIT-lagringsplats ovan. Git-lagringsplatsen måste redan finnas, och den måste vara tom utan någon mastergren. Och du måste ha skrivbehörighet till den. Att du lägger till en Git-lagringsplats nu gör det möjligt att använda roaming och delning senare. [Läs mer](using-git-ml-project.md).

4. Klicka på knappen **Create** (Skapa) för att skapa projektet. Ett nytt projekt skapas och öppnas för dig. Nu kan du utforska projektets startsida, datakällor, anteckningsböcker och källkodsfiler. 

    >[!TIP]
    >Du kan också öppna projektet i Visual Studio Code eller andra redigerare genom att konfigurera en IDE-länk (Integrated Development Environment) och sedan öppna projektkatalogen i den. [Läs mer](how-to-configure-your-IDE.md). 

## <a name="run-a-python-script"></a>Köra ett Python-skript
Låt oss köra ett skript på din lokala dator. 

1. Alla projekt öppnas på en egen **instrumentpanelssida**. Välj **local** (lokalt) som körningsmål från kommandofältet upptill i programfönstret och välj **iris_sklearn.py** som det skript som ska köras. Det finns andra filer i exemplet som du kan ta en titt på senare. 

   ![Kommandofältet](media/quickstart-installation/run_control.png)

2. Skriv **0.01** i textfältet **Arguments** (Argument). Numret används i koden för att ange regulariseringshastigheten. Det här är ett värde som används för att konfigurera hur den linjär regressionsmodellen ska tränas. 

3. Välj knappen **Run** (Kör) för att börja köra **iris_sklearn.py** på datorn. 

   I koden används algoritmen för [logistisk regression](https://en.wikipedia.org/wiki/logistic_regression) från Pythonbiblioteket [scikit-learn](http://scikit-learn.org/stable/index.html) för att bygga modellen.

4. Panelen **Jobs** (Jobb) skjuts ut från höger om den inte redan visas, och ett **iris_sklearn**-jobb läggs till på panelen. Dess status övergår från **Submitting** (Skickas) till **Running** (Körs) när jobbet börjar köras och sedan till **Completed** (Slutfört) efter ett par sekunder. 

   Grattis! Du har kört ett Python-skript i Azure Machine Learning Workbench.

6. Upprepa steg 2 till 4 flera gånger. Använd nya argumentvärden varje gång, från **10** till **0.001**.

## <a name="view-run-history"></a>Visa körningshistorik
1. Gå till vyn **Runs** (Körningar) och välj **iris_sklearn.py** i körningslistan. Instrumentpanelen för körningshistorik för **iris_sklearn.py** öppnas. På instrumentpanelen visas alla körningar som har gjorts på **iris_sklearn.py**. 

   ![Instrumentpanelen för körningshistorik](media/quickstart-installation/run_view.png)

2. Instrumentpanelen visar också toppmått, en uppsättning standarddiagram och en lista med mått för varje körning. Du kan anpassa den här vyn genom att sortera, filtrera och justera konfigurationerna. Välj konfigurationsikonen eller filterikonen.

   ![Mått och grafer](media/quickstart-installation/run_dashboard.png)

3. Välj en slutförd körning så visas en detaljerad vy av den specifika körningen. Bland den detaljerade informationen finns uppgifter om flera mått, vilka filer som skapades och andra loggar som eventuellt kan komma till användning.

## <a name="next-steps"></a>Nästa steg
Nu har du skapat ett Azure Machine Learning-experimenteringskonto och ett Azure Machine Learning-modellhanteringskonto. Du har installerat skrivbordsappen Azure Machine Learning Workbench och kommandoradsgränssnittet. Du har skapat ett nytt projekt, skapat en modell genom att köra ett skript och utforskat körningshistorik för skriptet.

Om du vill ha en mer ingående beskrivning av det här arbetsflödet, inklusive hur du distribuerar din Iris-modell som en webbtjänst, kan du följa självstudiekursen *Classifying Iris* (Klassificera Iris). Självstudiekursen innehåller detaljerade steg för [dataförberedelse](tutorial-classifying-iris-part-1.md), [experimentering](tutorial-classifying-iris-part-2.md) och [modellhantering](tutorial-classifying-iris-part-3.md). 

> [!div class="nextstepaction"]
> [Självstudiekursen Klassificera Iris](tutorial-classifying-iris-part-1.md)
