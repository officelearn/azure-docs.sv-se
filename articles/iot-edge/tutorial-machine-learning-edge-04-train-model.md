---
title: 'Självstudie: träna och distribuera en modell Machine Learning på Azure IoT Edge'
description: I den här självstudien kommer du att träna en maskin inlärnings modell med Azure Machine Learning och sedan paketera modellen som en behållar avbildning som kan distribueras som en Azure IoT Edge modul.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c0613d319c0c82fa61d75ed016d68d38dfcea8d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701832"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Självstudie: träna och distribuera en Azure Machine Learning modell

> [!NOTE]
> Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt rekommenderar vi att du börjar med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa möjliga resultat.

I den här artikeln använder vi Azure Notebooks först för att träna en maskin inlärnings modell med Azure Machine Learning och sedan paketera modellen som en behållar avbildning som kan distribueras som en Azure IoT Edge modul. Azure Notebooks dra nytta av en Azure Machine Learning arbets yta, som är ett grundläggande block som används för att experimentera, träna och distribuera maskin inlärnings modeller.

Aktiviteterna i den här delen av självstudien är uppdelade i två antecknings böcker.

* **01-turbofan\_regression. ipynb:** Den här antecknings boken vägleder dig genom stegen för att träna och publicera en modell med hjälp av Azure Machine Learning. De steg som ingår är i stort sett följande:

  1. Ladda ned, Förbered och utforska tränings data
  2. Använd arbets ytan tjänst för att skapa och köra ett Machine Learning-experiment
  3. Utvärdera modell resultatet från experimentet
  4. Publicera den bästa modellen på arbets ytan tjänst

* **02-turbofan\_distribuera\_Model. ipynb:** Den här antecknings boken tar modellen som skapats i den tidigare antecknings boken och använder den för att skapa en behållar avbildning som kan distribueras till en Azure IoT Edge enhet.

  1. Skapa ett bedömnings skript för modellen
  2. Skapa och publicera avbildningen
  3. Distribuera avbildningen som en webb tjänst på Azure Container instance
  4. Använd webb tjänsten för att verifiera modellen och avbildningen fungerar som förväntat

Stegen i den här artikeln kan vanligt vis utföras av data experter.

## <a name="set-up-azure-notebooks"></a>Konfigurera Azure Notebooks

Vi använder Azure Notebooks för att vara värd för de två Jupyter-Anteckningsbokarna och de filer som stöds. Här skapar och konfigurerar vi ett Azure Notebooks-projekt. Om du inte har använt Jupyter och/eller Azure Notebooks, är här några introduktions dokument:

* **Snabb start:** [skapa och dela en bärbar dator](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Självstudie:** [skapa och kör en Jupyter-anteckningsbok med python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Precis som med den virtuella Developer-datorn kan du använda Azure Notebooks för att säkerställa en konsekvent miljö för övningen.

> [!NOTE]
> När du har konfigurerat tjänsten kan du använda tjänsten Azure Notebooks från vilken dator som helst. Under installationen bör du använda den virtuella utvecklings datorn som innehåller alla filer som du behöver.

### <a name="create-an-azure-notebooks-account"></a>Skapa ett Azure Notebooks konto

Azure Notebook-konton är oberoende av Azure-prenumerationer. Om du vill använda Azure Notebooks måste du skapa ett konto.

1. Navigera till [Azure-anteckningsböcker](https://notebooks.azure.com).

2. Klicka på **Logga** in i det övre högra hörnet på sidan.

3. Logga in med ett arbets-eller skol konto (Azure Active Directory) eller ditt personliga konto (Microsoft-konto).

4. Om du inte har använt Azure Notebooks tidigare uppmanas du att bevilja åtkomst till Azure Notebooks-appen.

5. Skapa ett användar-ID för Azure Notebooks.

### <a name="upload-jupyter-notebooks-files"></a>Ladda upp filer för Jupyter Notebooks

I det här steget skapar vi ett nytt Azure Notebooks-projekt och laddar upp filer till den. Mer specifikt är de filer som vi laddar upp:

* **01-turbofan\_regression. ipynb**: Jupyter Notebook-fil som vägleder dig genom processen att ladda ned data som genereras av enhets nätet från Azure Storage-kontot. utforska och förbereda data för att träna klassificeraren; träna modellen, testa data med hjälp av test data uppsättningen som finns i filen test\_FD003. txt. och spara sedan klassificerings modellen i Machine Learning service-arbetsytan.

* **02-turbofan\_distribuera\_Model. ipynb:** Jupyter Notebook som vägleder dig genom processen att använda klassificerings modellen som sparats i arbets ytan Machine Learning tjänst för att skapa en behållar avbildning. När avbildningen har skapats vägleder den bärbara datorn dig genom processen att distribuera avbildningen som en webb tjänst så att du kan kontrol lera att den fungerar som förväntat. Den verifierade avbildningen distribueras till vår IoT Edge enhet i avsnittet [skapa och distribuera anpassade IoT Edge moduler](tutorial-machine-learning-edge-06-custom-modules.md) i den här självstudien.

* **Test\_FD003. txt:** Den här filen innehåller de data som vi ska använda som vår test uppsättning när du verifierar vår utbildade klassificerare. Vi valde att använda test data som har angetts för den ursprungliga tävlingen som vår test uppsättning för att förenkla exemplet.

* **RUL\_FD003. txt:** Den här filen innehåller RUL för den senaste cykeln för varje enhet i filen test\_FD003. txt. Se filen **Readme. txt** och **modellen för skadas spridning** i avsnittet C:\\source\\IoTEdgeAndMlSample\\data\\turbofan för en detaljerad förklaring av data.

* **Utils.py:** Innehåller en uppsättning python-verktyg för att arbeta med data. Den första antecknings boken innehåller en detaljerad förklaring av funktionerna.

* **Readme.MD:** README som beskriver hur du använder antecknings böckerna.

Skapa ett nytt projekt och överför filerna till din bärbara dator.

1. Välj **Mina projekt** i den översta meny raden.

1. Välj **+ nytt projekt**. Ange ett namn och ett ID. Projektet behöver inte vara offentligt eller ha ett viktigt.

1. Välj **Ladda upp** och välj **från dator**.

1. Välj **Välj filer**.

1. Navigera till **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Markera alla filer och klicka på **Öppna**.

1. Välj **Ladda upp** för att börja ladda upp och välj sedan **klart** när processen är klar.

## <a name="run-azure-notebooks"></a>Kör Azure Notebooks

Nu när projektet har skapats kör du den **01-turbofan\_regression. ipynb** Notebook.

1. På sidan turbofan-projekt väljer du **01-turbofan\_regression. ipynb**.

    ![Välj den första notebook som ska köras](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Om du uppmanas väljer du python 3,6-kärnan i dialog rutan och väljer **Ange kernel**.

3. Om antecknings boken **inte är betrodd**klickar du på widgeten **ej betrodd** överst till höger i antecknings boken. Välj **förtroende**när dialog rutan öppnas.

4. Följ anvisningarna i antecknings boken.

    * `Ctrl + Enter` kör en cell.
    * `Shift + Enter` kör en cell och navigerar till nästa cell.
    * När en cell körs har den en asterisk mellan hakparenteserna, som **[\*]** . När den är klar ersätts asterisken med ett tal och relevanta utdata kan visas nedan. Eftersom cellerna ofta bygger på de föregåendes arbete, kan endast en cell köras i taget.

5. När du är färdig med att köra **01-turbofan\_regression. ipynb** Notebook går du tillbaka till projekt sidan.

6. Öppna **02-turbofan\_distribuera\_Model. ipynb** och upprepa stegen i det här avsnittet för att köra den andra antecknings boken.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi två Jupyter-anteckningsböcker som körs i Azure Notebooks för att använda data från turbofan-enheter för att träna en återstående RUL-klassificerare () för att spara klassificeraren som en modell, för att skapa en behållar avbildning och för att distribuera och testa avbildningen som ett webb-se rvice.

Fortsätt till nästa artikel om du vill skapa en IoT Edge enhet.

> [!div class="nextstepaction"]
> [Konfigurera en IoT Edge enhet](tutorial-machine-learning-edge-05-configure-edge-device.md)
