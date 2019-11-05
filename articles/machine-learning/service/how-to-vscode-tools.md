---
title: Använda Visual Studio Code för Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur du installerar Azure Machine Learning för Visual Studio Code och hur du skapar ett experiment i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: df23c41629dfe86058f0f2fb6602e85fd5a6d494
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489294"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Kom igång med Azure Machine Learning för Visual Studio Code

I den här artikeln får du lära dig hur du använder **Azure Machine Learning för Visual Studio Code** -tillägget för att träna och distribuera Machine Learning-modeller.

[Azure Machine Learning](overview-what-is-azure-ml.md) effektiviserar byggnaden, utbildningen och distributionen av Machine Learning-modeller.
+ För utbildning har den stöd för att köra experiment lokalt eller via fjärr anslutning. För varje experiment kan du logga anpassade mått för flera körningar för att finjustera disponeringsparametrarna
+ Du kan också använda Azure Machine Learning för att enkelt distribuera maskin inlärnings modeller för testnings-och produktions behoven.

## <a name="prerequisites"></a>Förutsättningar

+ Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

+ Installera [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), en lätt kod redigerare som körs på Windows, Mac och Linux.

+ [Installera Python 3,5 eller senare](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>Installera tillägget

När du installerar tillägget för Azure Machine Learning installeras två fler tillägg automatiskt. De är tillägget [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) och [Microsoft python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). Mer information om hur du använder python-tillägget för att redigera, köra och felsöka python-kod finns i [själv studie kursen om python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).

Så här installerar du Azure Machine Learning-tillägget:

1. Öppna Visual Studio Code.

1. Växla till fliken tillägg och Sök efter "Azure Machine Learning".

1. På fliken tillägg väljer du **Installera**.

1. En välkomst flik för tillägget öppnas i Visual Studio Code och Azure-symbolen (markerad i rött i följande skärm bild) läggs till i aktivitets fältet.

   ![Azure-ikonen i aktivitets fältet i Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. I dialog rutan väljer du **Logga in** och följer anvisningarna för att autentisera med Azure.

   Tillägget Azure-konto, som har installerats tillsammans med Azure Machine Learning för kod tillägget för Visual Studio, hjälper dig att autentisera med ditt Azure-konto. En lista över kommandon finns på sidan för [tillägget Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!TIP]
> Du kan också hämta tilläggs installations programmet direkt från [Azure Machine Learning för Visual Studio Code Extension (för hands version)](https://aka.ms/vscodetoolsforai).

## <a name="quickstart-with-azure-machine-learning"></a>Snabb start med Azure Machine Learning
Det finns flera sätt att köra dina utbildnings skript med hjälp av Azure Machine Learning. Om du precis har kommit igång ska vi först gå igenom hur du snabbt kan skicka ett utbildnings skript som körs i Azure.

Om du redan är bekant med Azure Machine Learning koncept och vill ha mer information om hur du hanterar och använder dem med tillägget, se [Azure Machine Learning djupgående med vs Code](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) nedan.

## <a name="run-an-existing-python-training-script-in-azure"></a>Köra ett befintligt python-utbildnings skript i Azure
Om du har ett befintligt utbildnings skript ger Azure Machine Learning tillägget för VS Code inte bara en utmärkt redigering, fel sökning och käll hantering, men det gör det också enkelt att köra och lagra mått för ditt skript i Azure.

Då sätter vi igång. Du kan använda ditt eget utbildnings skript om du har det klart eller klonat exemplet [VSCode-tools-for-AI lagrings platsen](https://github.com/microsoft/vscode-tools-for-ai). Detta är offentlig lagrings platsen för att skicka problem med det här tillägget. Den innehåller också en liten **mnist** -exempel mapp som vi ska använda för det här exemplet.

1. Öppna mappen **mnist** i vs Code.

1. Skapa en ny python-miljö med ditt favorit paket för virtuella miljöer eller Anaconda och installera TensorFlow-och numpy-paketen.

1. Välj den nya miljö som du skapade som python-tolk längst ned till vänster i statusfältet för VS-koden.

1. Öppna **Train.py** och kör den genom att öppna fel söknings programmet och trycka på knappen Kör (eller bara trycka på F5).

   [![utbildning för att köra MNIST](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

Om allt är korrekt installerat körs skriptet och en TensorFlow-modell skapas i mappen utdata.

[![Visa TensorFlow-modellen](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

Nu när du vet att skriptet fungerar som det ska kan vi köra det i Azure!

Detta kan vara enkelt att göra utan extra ändring av **Train.py**. Men med bara några få enkla ändringar kan du använda Azure Machine Learning för att automatiskt spåra viktiga mått som du väljer om varje övnings körning.

### <a name="make-azure-aware-of-your-run-metrics"></a>Gör Azure medveten om dina körnings mått
Ändra ditt projekt så att Azure kan vara medveten om viktig information i dina körningar:

1. Skapa en fil med namnet **amlrun.py** i samma mapp som **Train.py**

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. Importera amlrun-filen i **Train.py**

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. Initiera körnings objekt i **Train.py**

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Logga mått till Azure med funktionen kör. log ():

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Kör skriptet i Azure
Klart! Använd nu bara tillägget för att köra skriptet i molnet! Observera att följande genom gångs video tar frihet att komprimera hur lång tid det tar att skapa en ny Azure ML-arbetsyta och beräkning, samt hur lång tid det tar att köra övnings skriptet.

   [![starta ett Azure ML-experiment](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

När du har klickat på knappen Kör experiment, svara på prompterna på följande sätt:

1. Välj en Azure-prenumeration
1. Välj att skapa en *ny* Azure ml-arbetsyta
1. Välj från en uppsättning förkonfigurerade mallar för att initiera python-miljön för körningarna. Mallarna ger en start punkt och innehåller inställningar för:
    1. **PyTorch**, **TensorFlow**eller **Scikit – lär dig**
    1. **Enkel** eller **distribuerad** beräknings utbildning
    1. **Allmänt** för anpassade miljöer
1. Se till att listan med pip-och Conda-paket är slutförda för ditt skript genom att lägga till eventuella paket som inte ingår i mallen.
1. Granska standard namn och specifikationer för experiment körningen och klicka på länken **Skicka experiment** i JSON-filen. JSON-filen sparas inte eftersom det bara finns där du kan granska eller ändra experiment inställningarna innan de skickas.
1. Luta dig tillbaka och minska medan tillägget ställer in allt för dig och kör skriptet!

    [![träna i molnet](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

Om några sekunder får du ett meddelande om att experimentet har skickats till Azure vid den tidpunkt då du kan se hur det fortskrider antingen i Azure Machine Learning Studio genom att klicka på länken **Visa experiment körning** i vs Code Notification eller i vs Code by knappen Uppdatera på fliken Azure.

För tillfället stöds endast visning av körnings mått i Studio. Länken **Visa experiment körning** som nämns ovan tar dig till körningen där du ser de mått som du har loggat.
[![experiment som körs i portalen](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Azure Machine Learning djupgående med VS Code

I genom gången ovan skickade vi ett experiment genom att följa den enklaste sökvägen. Som du kanske har märkt tillägget minimerar du de steg som du behöver hantera för att köra ett experiment. I det här avsnittet tar vi upp hur du kan hantera alla Azure Machine Learning koncept individuellt, vilket ger maximal kontroll.

Innan du börjar träna och distribuerar maskin inlärnings modeller i Visual Studio Code, måste du skapa en [Azure Machine Learning arbets yta](concept-workspace.md) i molnet. Den här arbets ytan kommer att innehålla dina modeller och resurser.

### <a name="create-a-workspace"></a>Skapa en arbetsyta

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

    [![skapa en arbets yta](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. Högerklicka på din Azure-prenumeration och välj **skapa arbets yta**. Som standard genereras ett namn som innehåller datum och tid då filen skapades. Ändra namnet till **TeamWorkspace** och tryck på RETUR.

1. Välj en resurs grupp i listan om du vet vilka du kan välja eller skapa en ny. Om du skapar en ny väljer du en plats som är närmast den plats som du planerar att distribuera din modell. I det här fallet valde vi **västra USA 2**.

1. När du trycker på Retur får Azure Machine Learning begäran om att skapa din arbets yta. Du får ett meddelande om processen i meddelande fältet i Visual Studio Code.

1. Expandera noden prenumeration för att hitta den nya arbets ytan.

### <a name="create-an-experiment"></a>Skapa ett experiment
Ett eller flera experiment kan skapas på arbets ytan för att spåra och analysera enskilda modell inlärnings körningar. Körningar kan göras i Azure-molnet eller på den lokala datorn.

1. Expandera arbets ytan **TeamWorkspace** . Högerklicka på noden **experiment** och välj **Skapa experiment** på snabb menyn.

1. Ange ett namn på experimentet i prompten. I exempel skärmarna heter experimentet **MNIST**.

1. Välj RETUR för att skapa det nya experimentet. Det nya experimentet visas i trädet som underordnat till **experiment** -noden.

1. I en arbets yta kan du högerklicka på ett experiment för att ställa in det som det **aktiva** experimentet. De **aktiva** experiment länkarna som experimenterar i molnet till den mapp du för närvarande har öppnat i Visual Studio Code. Den här mappen ska innehålla dina lokala Python-skript. Genom att ställa in ett aktivt experiment lagras viktiga mått för alla utbildningar i experimentet, oavsett var de körs.

    [![skapa ett experiment](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>Skapa och hantera beräknings mål

Med Azure Machine Learning för Visual Studio Code kan du förbereda dina data, träna modeller och distribuera dem både lokalt och på fjärranslutna beräknings mål.

Tillägget stöder flera fjärrberäknings mål för Azure Machine Learning. Mer information finns i den fullständiga listan med [beräknings mål som stöds för Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Skapa beräknings mål för Azure Machine Learning i Visual Studio Code

Så här skapar du ett beräknings mål:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

1. I trädvyn expanderar du din Azure-prenumeration och Azure Machine Learning arbets ytan.

1. Under noden arbets yta högerklickar du på **Compute** -noden och väljer **skapa beräkning**.

1. Välj Compute Target-typ i listan.

1. Välj en storlek på virtuell dator i paletten kommando tolk. Du kan filtrera beräkningarna med text, till exempel "GPU".

1. I kommando tolken i paletten anger du ett namn för beräknings målet.

1. När du har angett namnet kommer beräkningen att skapas med hjälp av standard parametrar. Om du vill ändra parametrarna högerklickar du på den nya beräkningen och väljer **Redigera beräkning**.

1. I JSON-filen som visas gör du önskade ändringar och klickar sedan på "Spara och fortsätt" CodeLens (med tangent bordet kan du trycka på **CTRL-SHIFT-p** för att anropa kommando paletten och köra kommandot **Azure ml: Spara och fortsätt** )

Här är ett exempel på hur du skapar och redigerar en Azure Machine Learning Compute (AMLCompute):

[![skapa AML Compute i Visual Studio Code](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Kör konfigurations filen

Om du vill köra ett Azure Machine Learning experiment vid en beräkning måste du konfigurera beräkningen på lämpligt sätt. En körnings konfigurations fil är den mekanism som används i den här miljön.

Här är ett exempel på hur du skapar en körnings konfiguration för AmlCompute, som skapats ovan.

[![skapa en körnings konfiguration för en beräkning](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

Om du vill köra Azure ML-experiment på den lokala datorn krävs fortfarande körnings konfigurations filen. När du skapar en lokal körnings konfiguration används den python-miljö som används som standard sökvägen till tolken som du har angett i VS Code.

### <a name="train-and-tune-models"></a>Träna och finjustera modeller

Med Azure ML-tillägget för VS-kod finns det flera sätt att köra ett utbildnings skript i ett experiment.

1. Högerklicka på övnings skriptet och välj **Azure ml: Kör som experiment i Azure**
1. Klicka på ikonen kör experimentera-verktygsfältet.
1. Högerklicka på noden kör konfiguration.
1. Använd kommando paletten VS Code för att köra **Azure ml: kör experimentet**

Köra ett Azure Machine Learning experiment:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code.

1. I trädvyn expanderar du din Azure-prenumeration och Azure Machine Learning arbets ytan.

1. Under noden arbets yta expanderar du noden **experiment** och högerklickar på det experiment som du vill köra.

1. Välj **Kör experiment**.

1. Välj namnet på den python-fil som du vill köra för att träna din modell och tryck på RETUR för att skicka in körningen. OBS! den valda filen måste finnas i mappen som du för närvarande har öppnat i VS Code.

1. När körningen har skickats visas en **körnings nod** under det experiment du väljer. Använd den här noden för att övervaka status för dina körningar. Obs: det kan vara nödvändigt att regelbundet uppdatera fönstret för att se den senaste statusen.

Här är ett exempel på hur du kan köra ett experiment på den redan skapade beräkningen:

[![köra ett experiment lokalt](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Distribuera och hantera modeller
I Azure Machine Learning kan du distribuera och hantera dina Machine Learning-modeller i molnet och på gränsen.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrera din modell för Azure Machine Learning från Visual Studio Code

Nu när du har tränat din modell kan du registrera den i din arbets yta. Du kan spåra och distribuera registrerade modeller.

Så här registrerar du din modell:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

1. I trädvyn expanderar du din Azure-prenumeration och Azure Machine Learning arbets ytan.

1. Under noden arbets yta högerklickar du på **modeller** och väljer **Registrera modell**.

1. På paletten kommando, i fältet, anger du ett modell namn.

1. I listan väljer du om du vill ladda upp en **modell fil** (för enskilda modeller) eller en **modell mapp** (för modeller med flera filer, till exempel TensorFlow).

1. Välj din mapp eller fil.

1. När du är klar med att konfigurera modell egenskaperna väljer du **Skicka**i det nedre högra hörnet i fönstret.

Här är ett exempel på hur du registrerar din modell för att Azure Machine Learning:

[![att registrera en modell på AML](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Distribuera din tjänst från Visual Studio Code

I Visual Studio Code kan du distribuera din webb tjänst till:
+ Azure Container Instances (ACI) för testning.
+ Azure Kubernetes service (AKS) för produktion.

Du behöver inte skapa en ACI-behållare för att testa i förväg eftersom ACI-behållare skapas vid behov. Du måste dock konfigurera AKS-kluster i förväg. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

Så här distribuerar du en webb tjänst:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

1. I trädvyn expanderar du din Azure-prenumeration och din Azure Machine Learning-arbetsyta.

1. Under noden arbets yta expanderar du noden **modeller** .

1. Högerklicka på den modell som du vill distribuera och välj **distribuera tjänst från registrerad modell** på snabb menyn.

1. På paletten kommando väljer du det beräknings mål som du vill distribuera till.

1. På paletten kommando, i fältet, anger du ett namn för den här tjänsten.

1. I paletten kommando väljer du nyckeln Enter på tangent bordet för att bläddra efter och välja skript filen.

1. På paletten kommando väljer du nyckeln Enter på tangent bordet för att bläddra efter och välja Conda beroende fil.

1. När du är klar med konfigurationen av tjänst egenskaperna väljer du **Skicka** för att distribuera i det nedre högra hörnet i fönstret. I tjänstens egenskaps fil kan du ange en lokal Docker-fil eller en schema. JSON-fil.

Webb tjänsten har nu distribuerats.

Här är ett exempel på hur du distribuerar en webb tjänst:

[![distribuera en webb tjänst](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Experimentera med ytterligare funktioner

Du kan använda kommando paletten för att få åtkomst till många Azure Machine Learning funktioner i Visual Studio Code. Så här anropar du kommando paletten Ctrl + Shift + P. Härifrån kan du söka efter ytterligare Azure ML-funktioner i tillägget.

[![kortkommandon för Azure Machine Learning för Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Nästa steg

* En genom gång av hur du tränar med Azure Machine Learning utanför Visual Studio Code finns i [Självstudier: träna modeller med Azure Machine Learning](tutorial-train-models-with-aml.md).
* En genom gång av hur du kan redigera, köra och felsöka kod lokalt finns i [själv studie kursen om python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).
