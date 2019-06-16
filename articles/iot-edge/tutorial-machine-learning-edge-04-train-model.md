---
title: Träna och distribuera en modell – Machine Learning på Azure IoT Edge | Microsoft Docs
description: Träna en maskininlärningsmodell med hjälp av Azure Machine Learning och sedan paketera modellen som en behållaravbildning som kan distribueras som en Azure IoT Edge-modul.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a94560bcb66c2ed59f78eef4f6a08b3f0227dc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057640"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Självstudier: Träna och distribuera en Azure Machine Learning-modell

> [!NOTE]
> Den här artikeln ingår i en serie med en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har hamnat på den här artikeln direkt, rekommenderar vi att du börja med den [först artikel](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln använder vi Azure anteckningsböcker först att träna en maskininlärningsmodell med hjälp av Azure Machine Learning och sedan paketera den modellen som en behållaravbildning som kan distribueras som en Azure IoT Edge-modul. Azure-anteckningsböcker dra nytta av en Azure Machine Learning-tjänsten arbetsyta, vilket är ett grundläggande block som används för att experimentera, träna och distribuera machine learning-modeller.

Aktiviteterna i den här delen av självstudiekursen delas mellan två anteckningsböcker.

* **01-turbofan\_regression.ipynb:** Den här anteckningsboken beskriver stegen för att träna och publicera en modell med hjälp av Azure Machine Learning. Allmänna ordalag är stegen:

  1. Ladda ned, förbereda och utforska utbildningsdata
  2. Använd arbetsytan service för att skapa och köra en machine learning-experiment
  3. Utvärdera modellen resultaten från experimentet
  4. Publicera den bästa modellen till arbetsytan service

* **02 turbofan\_distribuera\_model.ipynb:** Den här anteckningsboken tar modellen som skapats i föregående anteckningsboken och används för att skapa en behållaravbildning som är redo att distribueras till en Azure IoT Edge-enhet.

  1. Skapa ett bedömningsskript för modellen
  2. Skapa och publicera avbildningen
  3. Distribuera avbildningen som en webbtjänst på Azure-Behållarinstans
  4. Använd webbtjänsten för att verifiera modellen och den avbildning som fungerar som förväntat

Stegen i den här artikeln kan vanligtvis utföras av datatekniker.

## <a name="set-up-azure-notebooks"></a>Konfigurera Azure-anteckningsböcker

Vi använder Azure-anteckningsböcker som värd för två Jupyter-anteckningsböcker och stödfiler. Här vi skapar och konfigurerar ett projekt med Azure-anteckningsböcker. Om du inte har använt Jupyter och/eller Azure-datorer följer här några grundläggande dokument:

* **Snabbstart:** [Skapa och dela en notebook-fil](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Självstudie:** [Skapa och kör en Jupyter-anteckningsbok med Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Som med den virtuella datorn i developer innan, garanterar med Azure anteckningsböcker en konsekvent miljö för den här övningen.

> [!NOTE]
> När du har konfigurerat kan anteckningsböcker i Azure-tjänsten nås från valfri dator. Under installationen, bör du använda en virtuell utvecklingsdator dit alla filer som du behöver.

### <a name="create-an-azure-notebooks-account"></a>Skapa ett konto i Azure-anteckningsböcker

Azure Notebook-konton är fristående från Azure-prenumerationer. Om du vill använda Azure-datorer, måste du skapa ett konto.

1. Gå till [Azure anteckningsböcker](http://notebooks.azure.com).

2. Klicka på **logga In** i det övre högra hörnet på sidan.

3. Logga in med ditt arbets eller skolkonto (Azure Active Directory) eller ditt personliga konto (Microsoft Account).

4. Om du inte har använt Azure anteckningsböcker innan uppmanas du att bevilja åtkomst för Azure-anteckningsböcker appen.

5. Skapa ett användar-ID för Azure-anteckningsböcker.

### <a name="upload-jupyter-notebooks-files"></a>Ladda upp filer för Jupyter-anteckningsböcker

I det här steget ska vi skapa ett nytt projekt i Azure-anteckningsböcker och ladda upp filer till den. Mer specifikt är som vi överför filerna:

* **01-turbofan\_regression.ipynb**: Jupyter notebook-fil som beskriver processen för att hämta data som genereras av en enhet stomme från Azure storage-kontot; Utforska och förbereda data för att träna klassificerare; Träna modellen; testa data med hjälp av test-datauppsättning hittades i testet\_FD003.txt filen; och slutligen spara klassificeraren modellera i tjänsten Machine Learning-arbetsytan.

* **02 turbofan\_distribuera\_model.ipynb:** Jupyter-anteckningsbok som guidar dig genom processen med att använda klassificerare modellen sparas i tjänsten Machine Learning-arbetsytan för att skapa en behållaravbildning. Notebook-får du genom processen för att distribuera avbildningen som en webbtjänst så att du kan verifiera den fungerar som förväntat när avbildningen har skapats. Verifierade avbildningen ska distribueras till vår Edge-enhet i den [skapa och distribuera anpassade IoT Edge-moduler](tutorial-machine-learning-edge-06-custom-modules.md) delen av den här självstudien.

* **Test\_FD003.txt:** Den här filen innehåller de data som vi använder som våra test som angetts vid verifiering av våra tränade klassificerare. Vi har valt att använda testdata som angavs för den ursprungliga tävlingen som våra test som angetts för enkelhetens skull i exemplet.

* **RUL\_FD003.txt:** Den här filen innehåller motors återstående Livslängd för den senaste cykeln av varje enhet i testet\_FD003.txt fil. Se den **readme.txt** och **skada spridning Modeling.pdf** filer på enhet C:\\källa\\IoTEdgeAndMlSample\\data\\Turbofan för en detaljerad förklaring av data.

* **Utils.py:** Innehåller en uppsättning funktioner för Python-verktyg för att arbeta med data. Första anteckningsboken innehåller en detaljerad förklaring av funktionerna.

* **README.md:** Viktigt-fil som beskriver användningen av de bärbara datorerna.

Skapa ett nytt projekt och överför filerna till din bärbara dator.

1. Välj **Mina projekt** från den översta menyraden.

1. Välj **+ nytt projekt**. Ange ett namn och ett ID. Det finns inget behov av projektet ska vara offentlig eller har ett viktigt.

1. Välj **överför** och välj **från datorn**.

1. Välj **Välj filer**.

1. Gå till **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Markera alla filer och klicka på **öppna**.

1. Välj **överför** att börja ladda upp och välj sedan **klar** när processen är klar.

## <a name="run-azure-notebooks"></a>Kör Azure anteckningsböcker

Nu när projektet har skapats, kör den **01 turbofan\_regression.ipynb** anteckningsboken.

1. Välj projektsida turbofan **01 turbofan\_regression.ipynb**.

    ![Välj första anteckningsboken för att köra](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Om du uppmanas väljer Python 3.6 Kernel i dialogrutan och välj **ange Kernel**.

3. Om anteckningsboken är **inte betrodd**, klickar du på den **inte betrodd** widget uppe till höger i anteckningsboken. När dialogrutan visas, väljer du **förtroende**.

4. Följ anvisningarna i anteckningsboken.

    * `Ctrl + Enter` Kör en cell.
    * `Shift + Enter` Kör en cell och navigerar till nästa cell.
    * När en cell körs den som har en asterisk mellan hakparenteser, **[\*]** . När den är klar, asterisken kommer att ersättas med en siffra och relevanta utdata kanske visas nedan. Eftersom celler bygga ofta på verk som tillhör de tidigare inställningarna, kan endast en cell köras i taget.

5. När du är klar körs den **01 turbofan\_regression.ipynb** anteckningsboken gå tillbaka till projektsidan för.

6. Öppna **02 turbofan\_distribuera\_model.ipynb** och upprepa stegen i det här avsnittet för att köra andra anteckningsboken.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har använde vi två Jupyter-anteckningsböcker som körs i Azure-datorer för att använda data från turbofan enheter för att träna en återstående livslängd (RUL)-klassificerare att spara klassificeraren som en modell för att skapa en behållaravbildning och för att distribuera och testa avbildningen som ett webb-se rnet.

Fortsätt till nästa artikel för att skapa en IoT Edge-enhet.

> [!div class="nextstepaction"]
> [Konfigurera en IoT Edge-enhet](tutorial-machine-learning-edge-05-configure-edge-device.md)
