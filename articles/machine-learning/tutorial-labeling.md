---
title: 'Självstudiekurs: Skapa ett etikettprojekt för bildklassificering'
titleSuffix: Azure Machine Learning
description: Lär dig hur du hanterar processen med att märka bilder så att de kan användas i flera klassens bildklassificeringsmodeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/02/2020
ms.openlocfilehash: fa33861d86ff8bee3e2a34fb3d93032ac6180880
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879723"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Självstudiekurs: Skapa ett etikettprojekt för bildklassificering i flera klasser 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här självstudien visar hur du hanterar processen för att märka (kallas även taggning) bilder som ska användas som data för att skapa maskininlärningsmodeller. Dataetikmärkning i Azure Machine Learning är i offentlig förhandsversion.

Om du vill träna en maskininlärningsmodell för att klassificera bilder behöver du hundratals eller tusentals bilder som är korrekt märkta.  Azure Machine Learning hjälper dig att hantera förloppet för ditt privata team av domänexperter när de märker dina data.
 
I den här guiden kommer du att använda bilder av katter och hundar.  Eftersom varje bild är antingen en katt eller en hund, är detta en *multi-class* märkning projekt. Du lär dig följande:

> [!div class="checklist"]
>
> * Skapa ett Azure-lagringskonto och ladda upp avbildningar till kontot.
> * Skapa en Azure Machine Learning-arbetsyta.
> * Skapa ett bildetikettprojekt i flera klasser.
> * Märk dina data.  Antingen kan du eller dina etiketter utföra den här uppgiften.
> * Slutför projektet genom att granska och exportera data.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning-arbetsyta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskininlärningsmodeller. Det binder din Azure-prenumeration och resursgrupp till ett lätt förbrukat objekt i tjänsten.

Du skapar en arbetsyta via Azure-portalen, en webbaserad konsol för hantering av dina Azure-resurser.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Starta ett märkningsprojekt

Därefter hanterar du datamärkningsprojektet i Azure Machine Learning studio, ett konsoliderat gränssnitt som innehåller verktygsinlärningsverktyg för att utföra datavetenskapliga scenarier för datavetenskapsutövare på alla färdighetsnivåer. Studion stöds inte i webbläsare i Internet Explorer.

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com).

1. Välj din prenumeration och arbetsytan som du skapade.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Skapa ett datalager

Azure Machine Learning-datalager används för att lagra anslutningsinformation, till exempel ditt prenumerations-ID och tokenauktorisering. Här använder du ett datalager för att ansluta till lagringskontot som innehåller bilderna för den här självstudien.

1. Välj **Datastores**till vänster på arbetsytan .

1. Välj **+ Nytt datalager**.

1. Fyll i formuläret med följande inställningar:

    Field|Beskrivning 
    ---|---
    Datastore namn | Ge databutiken ett namn.  Här använder vi **labeling_tutorial**.
    Datalagertyp | Välj typ av lagring.  Här använder vi **Azure Blob Storage**, den föredragna lagringen för avbildningar.
    Metod för kontoval | Välj **Ange manuellt**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Autentiseringstyp | Välj **SAS-token**.
    Kontonyckel | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Välj **Skapa** om du vill skapa databutiken.

### <a name="add-labelers-to-workspace"></a>Lägga till etiketter på arbetsytan

Konfigurera arbetsytan så att den innehåller alla personer som ska märka data för något av dina projekt.  Senare lägger du till dessa etiketter i ditt specifika märkningsprojekt.

1. Välj **Datamärkning**på vänster sida .

1. Högst upp på sidan väljer du **Märkre.**

1. Välj **Lägg till etikett om** du vill lägga till en etiketts e-postadress.

1. Fortsätt att lägga till fler etiketter tills du är klar.

### <a name="create-a-labeling-project"></a>Skapa ett etikettprojekt

Nu när du har din lista över etiketter och tillgång till de data som du vill ha märkt, skapa din märkning projekt.

1. Högst upp på sidan väljer du **Projekt**.

1. Välj **+ Lägg till projekt**.

    ![Skapa ett projekt](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>Projektinformation

1. Använd följande indata för formuläret **Projektinformation:**

    Field|Beskrivning 
    ---|---
    Projektnamn | Ge projektet ett namn.  Här kommer vi att använda **handledning-katter-n-hundar**.
    Märka uppgiftstyp | Välj **Bildklassificering Multi-klass**.  
    
    Välj **Nästa** om du vill fortsätta att skapa projektet.

### <a name="select-or-create-a-dataset"></a>Markera eller skapa en datauppsättning

1.   I formuläret **Välj eller skapa en datauppsättning** väljer du det andra alternativet, Skapa en **datauppsättning**och väljer sedan länken **Från datalager**.

1. Använd följande indata för formuläret **Skapa datauppsättning från datalager:**

    1. I formuläret **Grundläggande information** lägger du till ett namn, här använder vi bilder **för handledning**.  Lägg till en beskrivning om du vill.  Välj sedan **Nästa**.
    1. I **formuläret Datastore-urval** använder du listrutan för att välja ditt **tidigare skapade datalager,** till exempel **tutorial_images (Azure Blob Storage)**
    1. Därefter väljer du **Bläddra** i formuläret **För datalager** och väljer sedan **MultiClass - DogsCats**.  Välj **Spara** om du vill använda **/MultiClass - DogsCats** som sökväg.
    1. Välj **Nästa** för att bekräfta information och sedan **Skapa** för att skapa datauppsättningen.
    1. Markera cirkeln bredvid datauppsättningsnamnet i listan, till exempel **bilder-för-självstudiekurs**.

1. Välj **Nästa** om du vill fortsätta att skapa projektet.

### <a name="label-classes"></a>Etikettklasser

1. Skriv ett etikettnamn i formuläret **Etikettklasser** och välj sedan **+Lägg till etikett** för att skriva nästa etikett.  För det här projektet är etiketterna **Cat,** **Dog**och **Osäker .**

1. Välj **Nästa** när alla etiketter har lagts till.

### <a name="labeling-instructions"></a>Märkningsinstruktioner

1. I formuläret **Märkningsinstruktioner** kan du tillhandahålla en länk till en webbplats som innehåller detaljerade instruktioner för dina etiketter.  Vi lämnar det tomt för den här guiden.

1. Du kan också lägga till en kort beskrivning av uppgiften direkt i formuläret .  Typ **Märkning handledning - Katter & Hundar.**

1. Välj **Nästa**.

1. Lämna kryssrutan omarkerad i **ml-formuläret för assisterad etiketter.** ML assisterad märkning kräver mer data än du kommer att använda i den här självstudien.

1. Välj **Skapa projekt**.

Den här sidan uppdateras inte automatiskt. Efter en paus uppdaterar du sidan manuellt tills projektets status ändras till **Skapad**.

### <a name="add-labelers-to-your-project"></a>Lägga till etiketter i projektet

Lägg till några eller alla etiketter i det här projektet.

1. Välj det projektnamn som ska öppnas för projektet.  

1. Högst upp på sidan väljer du **Teams**.

1. Välj länken **labeling_tutorial standardteam.**

1. Använd nu **Tilldela etiketter** för att lägga till de etiketter som du vill delta i det här projektet. 

1. Välj i listan över etiketter som du skapade tidigare.  När du har valt alla etiketter som du vill använda väljer du **Tilldela etiketter för** att lägga till dem i standardprojektgruppen.

## <a name="start-labeling"></a>Börja etikettera

Du har nu konfigurerat dina Azure-resurser och konfigurerat ett datamärkningsprojekt. Det är dags att lägga till etiketter i dina data.

### <a name="notify-labelers"></a>Meddela etiketter

Om du har massor av bilder att märka, förhoppningsvis har du också massor av labelers för att slutföra uppgiften.  Nu vill du skicka dem instruktioner så att de kan komma åt data och börja märka.

1. I [Machine Learning Studio](https://ml.azure.com)väljer du **Datamärkning** till vänster för att hitta projektet.  

1. Välj länken för projektets namn.

1. Högst upp på sidan väljer du **Information**.  Du ser en sammanfattning av projektet.

    ![Projektinformation](media/tutorial-labeling/project-details.png)

1. Kopiera **url-länken för etikettportalen** som du vill skicka till dem.

1. Välj nu **Team** högst upp för att hitta ditt märkningsteam.  

1. Välj länken för teamets namn.

1. Högst upp på sidan väljer du **E-postteam** för att starta din e-post.  Klistra in den url till etikettportalen som du just kopierade.  

Varje gång en etikett går till portal-URL:en visas fler bilder att märka tills kön är tom.  

### <a name="tag-the-images"></a>Tagga bilderna

I den här delen av självstudien ska du byta roller från *projektadministratören* till en *etikett.*  Använd webbadressen som du skickade till teamet.  Den här webbadressen tar dig till projektets etikettportal.  Om du hade lagt till instruktioner skulle du se dem här när du kommer till sidan.

1. Högst upp på sidan väljer du **Uppgifter som** ska börja märkas.

1. Välj en miniatyrbild till höger för att visa hur många bilder du vill märka på en gång. Du måste märka alla dessa bilder innan du kan gå vidare. Växla bara layouter när du har en ny sida med omärkta data. Om du byter layout rensas sidans pågående taggningsarbete.

1. Markera en eller flera bilder och välj sedan en tagg som ska tillämpas på markeringen. Taggen visas under bilden.  Fortsätt att markera och tagga alla bilder på sidan.  Om du vill markera alla bilder som visas samtidigt väljer du **Markera alla**. Markera minst en bild om du vill använda en tagg.


    > [!TIP]
    > Du kan välja de första nio taggarna med hjälp av sifferknapparna på tangentbordet.

1. När alla bilder på sidan har taggats väljer du **Skicka** för att skicka in dessa etiketter.

    ![Tagga bilder](media/tutorial-labeling/catsndogs.gif)

1. När du har skickat taggar för de ställda data uppdaterar Azure sidan med en ny uppsättning avbildningar från arbetskön.

## <a name="complete-the-project"></a>Slutföra projektet

Nu ska du byta tillbaka roller till *projektadministratören* för märkningsprojektet.

Som chef kanske du vill granska etikettns arbete.  

### <a name="review-labeled-data"></a>Granska märkta data

1. I [Machine Learning Studio](https://ml.azure.com)väljer du **Datamärkning** till vänster för att hitta projektet.  

1. Välj länken för projektets namn.

1. Instrumentpanelen visar projektets förlopp.

1. Högst upp på sidan väljer du **Data**.

1. På vänster sida väljer du **Märkta data** för att se dina taggade bilder.  

1. När du inte håller med om en etikett markerar du bilden och väljer sedan **Avvisa** längst ned på sidan.  Taggarna tas bort och bilden sätts tillbaka i kön av omärkta bilder.

### <a name="export-labeled-data"></a>Exportera märkta data

Du kan exportera etikettdata för Machine Learning-experiment när som helst. Användare exporterar ofta flera gånger och tränar olika modeller, i stället för att vänta på att alla bilder ska märkas.

Bildetiketter kan exporteras i [COCO-format](http://cocodataset.org/#format-data) eller som en Azure Machine Learning-datauppsättning. Datauppsättningsformatet gör det enkelt att använda för utbildning i Azure Machine Learning.  

1. I [Machine Learning Studio](https://ml.azure.com)väljer du **Datamärkning** till vänster för att hitta projektet.  

1. Välj länken för projektets namn.

1. Välj **Exportera** och välj **Exportera som Azure ML-datauppsättning**. 

    Exportens status visas precis under **knappen Exportera.** 

1. När etiketterna har exporterats väljer du **Datauppsättningar** till vänster för att visa resultatet.

## <a name="clean-up-resources"></a>Rensa resurser


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du märkt bilder.  Använd nu dina märkta data:

> [!div class="nextstepaction"]
> [Träna en modell för bildigenkänning av maskininlärning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
