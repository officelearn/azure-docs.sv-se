---
title: Snabbstart Skapa en klassificerare - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du lära dig hur du använder webbplatsen Custom Vision för att skapa en bildklassificeringsmodell.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: b664a586398e297a00ea9cd8fe68dc65e6ade5c8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170016"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Snabbstart: Så här skapar du en klassificerare med anpassad vision

I den här snabbstarten får du lära dig hur du skapar en klassificerare via webbplatsen Custom Vision. När du har byggt en klassarmodell kan du använda tjänsten Custom Vision för bildklassificering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

- En uppsättning bilder som du kan träna klassificeraren med. Se nedan för tips om hur du väljer bilder.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Skapa anpassade visionsresurser i Azure-portalen

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

I webbläsaren navigerar du till [webbsidan Custom Vision](https://customvision.ai) och väljer __Logga in__. Logga in med samma konto som du använde för att logga in på Azure-portalen.

![Bild på inloggningssidan](./media/browser-home.png)


1. Om du vill skapa ditt första projekt väljer du **Nytt projekt**. Dialogrutan **Skapa nytt projekt** visas.

    ![Den nya projektdialogrutan innehåller fält för namn, beskrivning och domäner.](./media/getting-started-build-a-classifier/new-project.png)

1. Ange ett namn och en beskrivning för projektet. Välj sedan en resursgrupp. Om ditt inloggade konto är kopplat till ett Azure-konto visar listrutan Resursgrupp alla dina Azure-resursgrupper som innehåller en anpassad visionstjänstresurs. 

   > [!NOTE]
   > Om det inte finns någon resursgrupp, bekräfta att du har loggat in [på customvision.ai](https://customvision.ai) med samma konto som du använde för att logga in på [Azure-portalen](https://portal.azure.com/). Bekräfta också att du har valt samma "Katalog" i Custom Vision-portalen som katalogen i Azure-portalen där dina Custom Vision-resurser finns. På båda webbplatserna kan du välja din katalog på menyn med rullgardinsmenyn längst upp till höger på skärmen. 

1. Välj __Klassificering__ under __Projekttyper__. Välj sedan **Multilabel** eller **Multiclass**under __Klassificeringstyper,__ beroende på användningsfall. Multilabel-klassificeringen tillämpar valfritt antal av dina taggar på en bild (noll eller mer), medan multiklassklassificering sorterar bilder i enstaka kategorier (varje bild du skickar sorteras till den mest sannolika taggen). Du kan ändra klassificeringstypen senare om du vill.

1. Välj sedan en av de tillgängliga domänerna. Varje domän optimerar klassificeraren för specifika typer av bilder, enligt beskrivningen i följande tabell. Du kommer att kunna ändra domänen senare om du vill.

    |Domain|Syfte|
    |---|---|
    |__Allmänna__| Optimerad för ett brett spektrum av bildklassificeringsuppgifter. Om ingen av de andra domänerna är lämplig, eller om du är osäker på vilken domän du ska välja, väljer du den allmänna domänen. |
    |__Mat__|Optimerad för fotografier av rätter som du skulle se dem på en restaurang meny. Om du vill klassificera fotografier av enskilda frukter eller grönsaker använder du domänen Mat.|
    |__Landmärken__|Optimerad för igenkännbara landmärken, både naturliga och konstgjorda. Den här domänen fungerar bäst när landmärket syns tydligt i fotografiet. Denna domän fungerar även om landmärket är något hindras av människor framför den.|
    |__Detaljhandeln__|Optimerad för bilder som finns i en shoppingkatalog eller shoppingwebbplats. Om du vill ha hög precision klassificera mellan klänningar, byxor och skjortor, använd denna domän.|
    |__Kompakta domäner__| Optimerad för begränsningar av realtidsklassificering på mobila enheter. De modeller som genereras av kompakta domäner kan exporteras för att köras lokalt.|

1. Slutligen väljer du __Skapa projekt__.

## <a name="choose-training-images"></a>Välj träningsbilder

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

I det här avsnittet ska du ladda upp och manuellt tagga bilder för att träna klassificeraren. 

1. Om du vill lägga till bilder klickar du på knappen __Lägg till bilder__ och väljer bläddra sedan bland lokala __filer__. Välj __Öppna__ om du vill gå till taggning. Taggvalet tillämpas på hela den grupp bilder som du har valt att ladda upp, så det är enklare att ladda upp bilder i separata grupper enligt önskade taggar. Du kan också ändra taggarna för enskilda bilder när de har laddats upp.

    ![Kontrollen Lägg till bilder visas längst upp till vänster och som en knapp längst ned i mitten.](./media/getting-started-build-a-classifier/add-images01.png)


1. Om du vill skapa en tagg anger du text i fältet __Mina taggar__ och trycker på Retur. Om taggen redan finns visas den i en rullgardinsmeny. I ett projekt med flera etiketter kan du lägga till mer än en tagg i dina bilder, men i ett projekt med flera klass kan du bara lägga till en. Om du vill ladda upp bilderna är du klar med knappen __Ladda upp [nummer]-filer.__ 

    ![Bild på tagg- och uppladdningssidan](./media/getting-started-build-a-classifier/add-images03.png)

1. Välj __Klar__ när bilderna har laddats upp.

    ![Förloppsindikatorn visar alla slutförda aktiviteter.](./media/getting-started-build-a-classifier/add-images04.png)

Om du vill ladda upp en annan uppsättning bilder går du tillbaka till början av det här avsnittet och upprepar stegen.

## <a name="train-the-classifier"></a>Träna klassificeraren

Om du vill träna **Train** klassificeraren väljer du tågknappen. Klassificeraren använder alla aktuella bilder för att skapa en modell som identifierar de visuella egenskaperna för varje tagg.

![Tågknappen längst upp till höger i webbsidans sidhuvudsverktygsfält](./media/getting-started-build-a-classifier/train01.png)

Träningsprocessen bör bara ta några minuter. Under den här tiden visas information om utbildningsprocessen på fliken **Prestanda.**

![Webbläsarfönstret med en träningsdialogruta i huvudavsnittet](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Utvärdera klassificeraren

När utbildningen har slutförts uppskattas modellens prestanda och visas. Custom Vision-tjänsten använder de bilder som du skickade in för utbildning för att beräkna precision och återkallande, med hjälp av en process som kallas [k-fold cross validering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precision och återkallande är två olika mätningar av en klassificerares effektivitet:

- **Precision** anger den andel av identifierade klassificeringar som var korrekta. Till exempel, om modellen identifierade 100 bilder som hundar, och 99 av dem var faktiskt av hundar, då precisionen skulle vara 99%.
- **Återkallande** anger den andel av faktiska klassificeringar som har identifierats korrekt. Till exempel, om det faktiskt fanns 100 bilder av äpplen, och modellen identifierade 80 som äpplen, skulle återkallandet vara 80%.

![Träningsresultaten visar den övergripande precisionen och återkallandet och precisionen och återkallandet för varje tagg i klassificeraren.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Tröskelvärde för sannolikhet

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Hantera träningsiterationer

Varje gång du tränar klassificeraren skapar du en ny _iteration_ med egna uppdaterade prestandamått. Du kan visa alla dina iterationer i den vänstra rutan på fliken **Prestanda.** Du hittar också knappen **Ta bort,** som du kan använda för att ta bort en iteration om den är föråldrad. När du tar bort en iteration tar du bort alla bilder som är unikt associerade med den.

Se [Använda din modell med förutsägelse-API:et](./use-prediction-api.md) för att lära dig hur du kommer åt dina tränade modeller programmässigt.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar och tränar en bildklassificeringsmodell med hjälp av webbplatsen Custom Vision. Därefter får du mer information om den iterativa processen för att förbättra din modell.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)

