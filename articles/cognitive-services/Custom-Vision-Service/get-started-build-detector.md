---
title: Snabbstart Bygg en objektdetektor - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du lära dig hur du använder webbplatsen Custom Vision för att skapa en bildklassificeringsmodell.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: ba121400368f37c4a562a9c34e209c59d15b173c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404116"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Snabbstart: Så här bygger du en objektdetektor med anpassad syn

I den här snabbstarten får du lära dig hur du skapar en objektdetektor via webbplatsen Custom Vision. När du har byggt en detektormodell kan du använda tjänsten Custom Vision för objektidentifiering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

- En uppsättning bilder som du kan träna din detektormodell med. Du kan använda uppsättningen [exempelbilder](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) på GitHub. Eller så kan du välja dina egna bilder med hjälp av tipsen nedan.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Skapa anpassade visionsresurser i Azure-portalen

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

I webbläsaren navigerar du till [webbsidan Custom Vision](https://customvision.ai) och väljer __Logga in__. Logga in med samma konto som du använde för att logga in på Azure-portalen.

![Bild på inloggningssidan](./media/browser-home.png)


1. Om du vill skapa ditt första projekt väljer du **Nytt projekt**. Dialogrutan **Skapa nytt projekt** visas.

    ![Den nya projektdialogrutan innehåller fält för namn, beskrivning och domäner.](./media/get-started-build-detector/new-project.png)

1. Ange ett namn och en beskrivning för projektet. Välj sedan en resursgrupp. Om ditt inloggade konto är kopplat till ett Azure-konto visar listrutan Resursgrupp alla dina Azure-resursgrupper som innehåller en anpassad visionstjänstresurs. 

   > [!NOTE]
   > Om det inte finns någon resursgrupp, bekräfta att du har loggat in [på customvision.ai](https://customvision.ai) med samma konto som du använde för att logga in på [Azure-portalen](https://portal.azure.com/). Bekräfta också att du har valt samma "Katalog" i Custom Vision-portalen som katalogen i Azure-portalen där dina Custom Vision-resurser finns. På båda webbplatserna kan du välja din katalog på menyn med rullgardinsmenyn längst upp till höger på skärmen. 

1. Välj __Objektidentifiering__ under __Projekttyper__.

1. Välj sedan en av de tillgängliga domänerna. Varje domän optimerar detektorn för specifika typer av bilder, enligt beskrivningen i följande tabell. Du kommer att kunna ändra domänen senare om du vill.

    |Domain|Syfte|
    |---|---|
    |__Allmänt__| Optimerad för ett brett spektrum av objektidentifieringsuppgifter. Om ingen av de andra domänerna är lämplig, eller om du är osäker på vilken domän du ska välja, väljer du den allmänna domänen. |
    |__Logotyp__|Optimerad för att hitta varumärkeslogotyper i bilder.|
    |__Kompakta domäner__| Optimerad för begränsningar av objektidentifiering i realtid på mobila enheter. De modeller som genereras av kompakta domäner kan exporteras för att köras lokalt.|

1. Slutligen väljer du __Skapa projekt__.

## <a name="choose-training-images"></a>Välj träningsbilder

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

I det här avsnittet kommer du att ladda upp och manuellt tagga bilder för att träna detektorn. 

1. Om du vill lägga till bilder klickar du på knappen __Lägg till bilder__ och väljer bläddra sedan bland lokala __filer__. Välj __Öppna__ för att ladda upp bilderna.

    ![Kontrollen Lägg till bilder visas längst upp till vänster och som en knapp längst ned i mitten.](./media/get-started-build-detector/add-images.png)

1. Du ser dina uppladdade bilder i avsnittet **Otaggade** i användargränssnittet. Nästa steg är att manuellt tagga de objekt som du vill att detektorn ska lära sig att känna igen. Klicka på den första bilden för att öppna dialogrutan taggning. 

    ![Bilder som laddats upp i avsnittet Otaggade](./media/get-started-build-detector/images-untagged.png)

1. Klicka och dra en rektangel runt objektet i bilden. Ange sedan ett nytt taggnamn med **+** knappen eller välj en befintlig tagg i listrutan. Det är mycket viktigt att tagga varje instans av de objekt som du vill identifiera, eftersom detektorn använder det otaggade bakgrundsområdet som ett negativt exempel under träning. När du är klar med taggningen klickar du på pilen till höger för att spara taggarna och gå vidare till nästa bild.

    ![Tagga ett objekt med en rektangulär markering](./media/get-started-build-detector/image-tagging.png)

Om du vill ladda upp en annan uppsättning bilder går du tillbaka till början av det här avsnittet och upprepar stegen.

## <a name="train-the-detector"></a>Träna detektorn

Om du vill träna detektormodellen väljer du **tågknappen.** Detektorn använder alla aktuella bilder och deras taggar för att skapa en modell som identifierar varje taggat objekt.

![Tågknappen längst upp till höger i webbsidans sidhuvudsverktygsfält](./media/getting-started-build-a-classifier/train01.png)

Träningsprocessen bör bara ta några minuter. Under den här tiden visas information om utbildningsprocessen på fliken **Prestanda.**

![Webbläsarfönstret med en träningsdialogruta i huvudavsnittet](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Utvärdera detektorn

När utbildningen har slutförts beräknas modellens prestanda och visas. Tjänsten Custom Vision använder de bilder som du har skickat in för utbildning för att beräkna precision, återkallande och genomsnittlig precision. Precision och återkallande är två olika mätningar av effektiviteten hos en detektor:

- **Precision** anger den andel av identifierade klassificeringar som var korrekta. Till exempel, om modellen identifierade 100 bilder som hundar, och 99 av dem var faktiskt av hundar, då precisionen skulle vara 99%.
- **Återkallande** anger den andel av faktiska klassificeringar som har identifierats korrekt. Till exempel, om det faktiskt fanns 100 bilder av äpplen, och modellen identifierade 80 som äpplen, skulle återkallandet vara 80%.

![Träningsresultaten visar den totala precisionen och återkallandet, och genomsnittlig genomsnittlig precision.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Tröskelvärde för sannolikhet

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Hantera träningsiterationer

Varje gång du tränar din detektor skapar du en ny _iteration_ med sina egna uppdaterade prestandamått. Du kan visa alla dina iterationer i den vänstra rutan på fliken **Prestanda.** I den vänstra rutan hittar du också knappen **Ta bort,** som du kan använda för att ta bort en iteration om den är föråldrad. När du tar bort en iteration tar du bort alla bilder som är unikt associerade med den.

Se [Använda din modell med förutsägelse-API:et](./use-prediction-api.md) för att lära dig hur du kommer åt dina tränade modeller programmässigt.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar och tränar en objektdetektormodell med hjälp av webbplatsen Custom Vision. Därefter får du mer information om den iterativa processen för att förbättra din modell.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)

