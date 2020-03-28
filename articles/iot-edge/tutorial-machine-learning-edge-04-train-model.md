---
title: 'Självstudiekurs: Träna och distribuera en modell – Machine Learning på Azure IoT Edge'
description: I den här självstudien ska du träna en maskininlärningsmodell med Azure Machine Learning och sedan paketera modellen som en behållaravbildning som kan distribueras som en Azure IoT Edge Module.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57630b789233dd23e61398f445b434e4ba08b48e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80236024"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Självstudiekurs: Träna och distribuera en Azure Machine Learning-modell

> [!NOTE]
> Den här artikeln är en del av en serie för en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt, uppmuntrar vi dig att börja med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln utför vi följande uppgifter:

* Använd Azure Notebooks för att träna en maskininlärningsmodell.
* Paketera den tränade modellen som en behållaravbildning.
* Distribuera behållaravbildningen som en Azure IoT Edge-modul.

Azure-anteckningsböckerna drar nytta av en Azure Machine Learning-arbetsyta, ett grundläggande block som används för att experimentera, träna och distribuera maskininlärningsmodeller.

Stegen i den här artikeln kan vanligtvis utföras av dataexperter.

## <a name="set-up-azure-notebooks"></a>Konfigurera Azure-anteckningsböcker

Vi använder Azure Notebooks som värd för de två Jupyter-anteckningsböckerna och stödfilerna. Här skapar och konfigurerar vi ett Azure Notebooks-projekt. Om du inte har använt Jupyter och/eller Azure Notebooks följer här ett par inledande dokument:

* **Snabbstart:** [Skapa och dela en anteckningsbok](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Självstudiekurs:** [Skapa och kör en Jupyter-anteckningsbok med Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Med hjälp av Azure-anteckningsböcker säkerställer en konsekvent miljö för övningen.

> [!NOTE]
> När azure notebooks-tjänsten har konfigurerats kan du komma åt från vilken dator som helst. Under installationen bör du använda utvecklingsdass, som har alla filer som du behöver.

### <a name="create-an-azure-notebooks-account"></a>Skapa ett Azure Notebooks-konto

Om du vill använda Azure-anteckningsböcker måste du skapa ett konto. Azure Notebook-konton är oberoende av Azure-prenumerationer.

1. Navigera till [Azure-anteckningsböcker](https://notebooks.azure.com).

1. Klicka **på Logga in i** det övre högra hörnet på sidan.

1. Logga in med ditt arbets- eller skolkonto (Azure Active Directory) eller ditt personliga konto (Microsoft-konto).

1. Om du inte har använt Azure Notebooks tidigare uppmanas du att bevilja åtkomst för Azure Notebooks-appen.

1. Skapa ett användar-ID för Azure-anteckningsböcker.

### <a name="upload-jupyter-notebook-files"></a>Ladda upp Jupyter-anteckningsboksfiler

Vi laddar upp exempelboksböcker till ett nytt Azure Notebooks-projekt.

1. På användarsidan för ditt nya konto väljer du **Mina projekt** i den övre menyraden.

1. Lägg till ett nytt **+** projekt genom att välja knappen.

1. Ange ett **projektnamn**i dialogrutan **Skapa nytt projekt** . 

1. Lämna **Public** och **README** okontrollerat eftersom det inte finns något behov av att projektet är offentligt eller att ha en readme.

1. Välj **Skapa**.

1. Välj **Ladda upp** (uppåtpilikonen) och välj Från **dator**.

1. Välj **Välj filer**.

1. Navigera till **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Markera alla filer i listan och klicka på **Öppna**.

1. Markera rutan **Jag litar på innehållet i dessa filer.**

1. Välj **Ladda upp** för att börja ladda upp och välj sedan **Klar** när processen är klar.

### <a name="azure-notebook-files"></a>Filer i Azure-anteckningsbok

Nu ska vi granska de filer som du har laddat upp till ditt Azure Notebooks-projekt. Aktiviteterna i den här delen av självstudien sträcker sig över två anteckningsboksfiler, som använder några stödfiler.

* **01-turbofan\_regression.ipynb:** Den här anteckningsboken använder arbetsytan Machine Learning-tjänst för att skapa och köra ett maskininlärningsexperiment. I stort sett gör anteckningsboken följande steg:

  1. Hämtar data från Azure Storage-kontot som genererades av enhetsselen.
  1. Utforskar och förbereder data och använder sedan data för att träna klassifierarmodellen.
  1. Utvärdera modellen från experimentet med hjälp av\_en testdatauppsättning (Test FD003.txt).
  1. Publicerar den bästa klassifierarmodellen till machine learning-tjänstarbetsytan.

* **02-turbofan\_\_distribuera model.ipynb:** Den här anteckningsboken tar modellen som skapats i den tidigare anteckningsboken och använder den för att skapa en behållaravbildning som är redo att distribueras till en Azure IoT Edge-enhet. Anteckningsboken utför följande steg:

  1. Skapar ett bedömningsskript för modellen.
  1. Skapar en behållaravbildning med hjälp av klassifierarmodellen som sparades på machine learning-tjänstarbetsytan.
  1. Distribuerar avbildningen som en webbtjänst på Azure Container Instance.
  1. Använder webbtjänsten för att validera modellen och avbildningen fungerar som förväntat. Den validerade avbildningen kommer att distribueras till vår IoT Edge-enhet i delen [Skapa och distribuera anpassade IoT Edge-moduler](tutorial-machine-learning-edge-06-custom-modules.md) av den här självstudien.

* **Testa\_FD003.txt:** Den här filen innehåller de data vi kommer att använda som vår testuppsättning när vi validerar vår utbildade klassificerare. Vi valde att använda testdata, som föreskrivs för den ursprungliga tävlingen, som vår testuppsättning för sin enkelhet.

* **RUL\_FD003.txt:** Den här filen innehåller återstående användbar livslängd (RUL) för\_den sista cykeln för varje enhet i testf003.txt-filen. Se readme.txt och Damage Propagation Modeling.pdf-filerna\\\\i C: source IoTEdgeAndMlSample\\data\\Turbofan för en detaljerad förklaring av data.

* **Utils.py:** Innehåller en uppsättning Python-verktygsfunktioner för att arbeta med data. Den första anteckningsboken innehåller en detaljerad förklaring av funktionerna.

* **README.md:** Readme som beskriver användningen av anteckningsböckerna.  

## <a name="run-azure-notebooks"></a>Kör Azure-anteckningsböcker

Nu när projektet har skapats kan du köra anteckningsböckerna. 

1. Välj **01-turbofan\_regression.ipynb**på projektsidan .

    ![Välj första anteckningsboken som ska köras](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Om anteckningsboken visas som **Inte betrodd**klickar du på widgeten **Inte betrodd** längst upp till höger i anteckningsboken. När dialogrutan kommer upp väljer du **Förtroende**.

1. Bäst resultat får du om du läser dokumentationen för varje cell och kör den individuellt. Välj **Kör** i verktygsfältet. Senare, du hittar det lämpligt att köra flera celler. Du kan bortse från uppgraderings- och utfasningsvarningar.

    När en cell körs visas en asterisk mellan hakparenteserna ([\*]). När cellens åtgärd är klar ersätts asterisken med ett tal och relevanta utdata kan visas. Cellerna i en anteckningsbok bygger sekventiellt och bara en kan köras åt gången.

    Du kan också använda köralternativ `Ctrl`  +  `Enter` från **cellmenyn,** `Shift`  +  `Enter` för att köra en cell och köra en cell och gå vidare till nästa cell.

    > [!TIP]
    > Undvik att köra samma anteckningsbok från flera flikar i webbläsaren för konsekventa cellåtgärder.

1. Skriv in värdena för din Azure-prenumeration, dina inställningar och resurser i cellen som följer instruktioner för **ange globala egenskaper.** Kör sedan cellen.

    ![Ange globala egenskaper i anteckningsboken](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Leta efter länken som instruerar dig att logga in för att autentisera efter att den har körts i cellen före **Arbetsyteinformationen**efter att den har körts:

    ![Logga in fråga om enhetsautentisering](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Öppna länken och ange den angivna koden. Den här inloggningsproceduren autentiserar Jupyter-anteckningsboken för åtkomst till Azure-resurser med hjälp av Kommandoradsgränssnittet i Microsoft Azure.  

    ![Autentisera program på enhetsbekräftelse](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Kopiera värdet från körnings-ID:t i cellen som föregår **Utforska resultaten**och klistra in det för körnings-ID:t i cellen som följer **Reconstitute en körning**.

   ![Kopiera körnings-ID mellan celler](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Kör de återstående cellerna i anteckningsboken.

1. Spara anteckningsboken och gå tillbaka till projektsidan.

1. Öppna **\_02-turbofan\_distribuera model.ipynb** och kör varje cell. Du måste logga in för att autentisera i cellen som följer **Konfigurera arbetsytan**.

1. Spara anteckningsboken och gå tillbaka till projektsidan.

### <a name="verify-success"></a>Verifiera lyckade

Kontrollera att anteckningsböckerna har slutförts genom att kontrollera att några objekt har skapats.

1. På projektsidan för Azure Notebooks väljer du **Visa dolda objekt** så att objektnamn som börjar med en punkt visas.

1. Kontrollera att följande filer har skapats:

    | Fil | Beskrivning |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Konfigurationsfil som används för att skapa Arbetsytan för Azure Machine Learning. |
    | ./aml_config/model_config.json | Konfigurationsfil som vi måste distribuera modellen i **arbetsytan turbofanDemo** Machine Learning i Azure. |
    | myenv.yml| Innehåller information om beroenden för den distribuerade Machine Learning-modellen.|

1. Kontrollera att följande Azure-resurser har skapats. Vissa resursnamn läggs till med slumpmässiga tecken.

    | Azure-resurs | Namn |
    | --- | --- |
    | Arbetsyta för maskininlärning | turborfanDemo |
    | Container Registry | turbofandemoxxxxxx |
    | Program Insights | turbofaninsightxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxx |
    | Lagring | turbofanstoragexxxxxxx |

### <a name="debugging"></a>Felsökning

Du kan infoga Python-satser i anteckningsboken `print()` för felsökning, till exempel kommandot för att visa värden. Om du ser variabler eller objekt som inte har definierats kör du cellerna där de först deklareras eller instansieras.

Du kan behöva ta bort tidigare skapade filer och Azure-resurser om du behöver göra om anteckningsböckerna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi två Jupyter-anteckningsböcker som körs i Azure Notebooks för att använda data från turbofanenheterna för att träna en återstående RUL-klassificerare, för att spara klassificeraren som modell, skapa en behållaravbildning och för att distribuera och testa avbildningen som en webb Tjänst.

Fortsätt till nästa artikel för att skapa en IoT Edge-enhet.

> [!div class="nextstepaction"]
> [Konfigurera en IoT Edge-enhet](tutorial-machine-learning-edge-05-configure-edge-device.md)
