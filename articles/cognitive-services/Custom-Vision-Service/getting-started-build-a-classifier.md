---
title: Skapa en klassificerare med Custom Vision Service – Azure Cognitive Services | Microsoft Docs
description: Lär dig hur du använder Custom Vision Service för att skapa en klassificerare som kan urskilja objekt i fotografier.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: c5183078d2f9d5eb16abef4f5df240f77eea6b8b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223377"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Hur du skapar en klassificerare med anpassad visuellt innehåll

Om du vill använda Custom Vision Service, måste du först skapa en klassificerare. I det här dokumentet lär du dig hur du skapar en klassificerare via webbläsaren.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en klassificerare, måste du först ha:

- En giltig [microsoftkonto](https://account.microsoft.com/account) eller ett Azure Active Directory OrgID (”arbets- eller skolkonto konto”), så att du kan logga in på customvision.ai och komma igång.

    > [!IMPORTANT] 
    > OrgID-inloggning för Azure Active Directory (Azure AD)-användare från [nationella moln](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) stöds inte för närvarande.

- En serie med bilder för att lära din klassificerare (med minst 30 bilder per tagg).

- Några bilder för att testa din klassificerare när klassificeraren har tränats.

- Valfritt: En Azure-prenumeration som är associerade med din Account eller OrgID. Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

    > [!IMPORTANT]
    > Utan någon Azure-prenumeration kommer du bara att kunna skapa __begränsad utvärderingsversion__ projekt. Om du har en Azure-prenumeration kan du uppmanas att skapa Custom Vision Service Inlärnings- och Förutsägelsetransaktioner resurser i den [Azure-portalen](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) när projektet skapades.   

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

Använd följande steg för att skapa ett nytt projekt:

1. I din webbläsare, navigerar du till den [Custom Vision-webbsida](https://customvision.ai). Välj __logga in__ att börja använda tjänsten.

    ![Bild av sidan logga in](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > När du loggar in på Custom Vision Service, visas en lista över projekt. Utanför två ”begränsad utvärderingsversion” projekt för att testa projekt som är associerade med en Azure-resurs. Om du är en Azure-användare, visas alla projekt som är associerade med [Azure-resurser](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) som du har åtkomst till. 

2. För att skapa ditt första projekt, Välj **nytt projekt**. För ditt första projekt uppmanas du att samtycker till användarvillkoren. Markera kryssrutan och välj sedan den **jag godkänner** knappen. Den **nytt projekt** dialogrutan visas.

    ![Dialogrutan Nytt projekt har fält för namn, beskrivning och domäner.](./media/getting-started-build-a-classifier/new-project.png)

3. Ange ett namn och en beskrivning för projektet. Välj sedan en av de tillgängliga domänerna. Varje domän optimerar klassificerare för vissa typer av bilder, enligt beskrivningen i följande tabell:

    |Domän|Syfte|
    |---|---|
    |__Generisk__| Optimerat för en mängd olika aktiviteter för klassificering av avbildning. Om ingen av de andra domänerna är lämpligt, eller du är osäker på vilken domän som du väljer, väljer du den allmänna domänen. |
    |__Mat__|Optimerat för fotografier av disk som du ser dem på en restaurang-meny. Om du vill klassificera fotografier av enskilda frukter och grönsaker använda mat domänen.|
    |__Landmärken__|Optimerat för identifierbara landmärken, både naturliga och konstgjorda. Den här domänen fungerar bäst när landmärken syns tydligt i fotot. Den här domänen fungerar även om landmärken något hindras av personer framför den.|
    |__Detaljhandel__|Optimerat för avbildningar som finns i en i katalogen eller Shopping. Om du vill hög precision klassificera mellan klänningar, byxor och skjortor, Använd den här domänen.|
    |__Vuxen__|Optimerad för att definiera en bättre vuxet innehåll och inte är vuxen innehåll. Till exempel om du vill blockera bilder av personer i badning passar kan den här domänen du skapa en anpassad klassificerare för att göra detta.|
    |__Compact domäner__| Optimerat för begränsningar i realtid klassificeringen på mobila enheter. Modeller som genererats av compact domäner kan exporteras för att köras lokalt.|

    Du kan ändra domänen senare om du vill.

4. Välj en resursgrupp. Resursgrupp listrutan visar alla dina Azure-resursgrupper som innehåller en resurs för Custom Vision Service. Du kan också skapa väljer __begränsad utvärderingsversion__. Begränsad utvärderingsversion posten är den enda resursgruppen som en icke-Azure-användare kommer att kunna välja mellan.

    För att skapa projektet, Välj __skapa projekt__.

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

1. Om du vill lägga till bilder i klassificeraren använder den __lägga till avbildningar__ knappen och välj sedan __Bläddra bland lokala filer__. Välj __öppna__ att flytta till Taggning.

    > [!TIP]
    > När du har valt bilder, måste du tagga dem. Taggen tillämpas på grupp med bilder som du har valt att ladda upp, så det kan vara enklare att överföra avbildningar efter taggar som du tänker använda. Du kan också ändra taggen för valda avbildningar när de har taggats och laddat upp.

    > [!TIP]
    > Ladda upp bilder med olika kameravinklar, belysning, bakgrund, typ, format, grupper, storlekar, osv. Använda en mängd olika fototyper av för att se till att din klassificerare inte prioriterar och kan också generalisera.

    Custom Vision Service accepterar inlärningsbilder i .jpg, .png och .bmp format, upp till 6 MB per bild. (Förutsägelse avbildningar kan vara upp till 4 MB per bild). Vi rekommenderar att avbildningar är 256 bildpunkter på kortaste gränsen. Alla avbildningar som är kortare än 256 bildpunkter på den kortaste kanten skalas med Custom Vision Service.

    ![Lägg till bilder kontrollen visas i det övre vänstra hörnet och som en knapp på längst ned i mitten.](./media/getting-started-build-a-classifier/add-images01.png)

    >[!NOTE] 
    > REST API kan användas för att läsa in inlärningsbilder från URL: er.

2. Om du vill ange taggen anger du texten i den __Mina taggar__ fältet och sedan använda den __+__ knappen. Ladda upp avbildningar och tagga dem genom att använda den __ladda upp filer [antal]__ knappen. Du kan lägga till mer än en etikett till bilderna. 

    > [!NOTE]
    > Ladda upp tiden varierar beroende på antalet och storleken på bilder som du har valt.

    ![Bild av sidan tagg och ladda upp](./media/getting-started-build-a-classifier/add-images03.png)

3. Välj __klar__ när de har överförts.

    ![Förloppsindikatorn visar alla aktiviteter har slutförts.](./media/getting-started-build-a-classifier/add-images04.png)

4. Gå tillbaka till steg 1 för att ladda upp en annan uppsättning bilder. Om du vill att skilja mellan hundar och ponnyer, ladda upp och tagga bilder av ponnyer.

## <a name="train-and-evaluate-the-classifier"></a>Träna och utvärdera klassificeraren

För att träna klassificeraren, Välj den **träna** knappen.

![Knappen träna närmar sig överst till höger i webbläsarfönstret.](./media/getting-started-build-a-classifier/train01.png)

Det tar bara några minuter att träna klassificeraren. Under tiden visas information om processen för utbildning.

![Knappen träna närmar sig överst till höger i webbläsarfönstret.](./media/getting-started-build-a-classifier/train02.png)

Efter utbildning, den __prestanda__ visas. Precision och återkallande indikatorer berättar hur bra din klassificerare, baseras på automatisk testning. Custom Vision Service använder de avbildningar som du skickade för utbildning för att beräkna dessa tal, med hjälp av en process som kallas [k-vikning mellan verifiering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![Utbildning resultatet visar övergripande precision och återkallande och precision och återkalla för varje tagg i klassificeraren.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Varje gång som du väljer den **träna** knapp, skapar du en ny iteration av din klassificerare. Du kan visa din gamla iterationer i den **prestanda** fliken och du kan ta bort alla som kan vara inaktuell. När du tar bort en iteration hamnar du tar bort eventuella avbildningar som är unikt associerade med den.

Klassificeraren använder alla avbildningar för att skapa en modell som identifierar varje tagg. Om du vill testa modellens kvalitet, försöker klassificeraren varje avbildning på modellen för att se modellen söker efter.

Egenskaper klassificerare resultat visas.

|Period|Definition|
|---|---|
|__Precision__|När du klassificerar en avbildning, hur sannolikt är din klassificerare att klassificera bilden korrekt? Utanför alla bilder som används för att träna klassificerare (hundar och ponnyer), hur många procent modellen fick korrekt? 99 rätt taggar från 100 bilder ger en noggrannhet på 99%.|
|__Återkallande__|Från alla avbildningar som bör har klassificerats korrekt, hur många din klassificerare identifieras korrekt? En träffsäkerhet på 100% innebär att om det finns 38 hund bilder i avbildningarna som användes för att träna klassificeraren, klassificeraren finns 38 hundar.|

## <a name="next-steps"></a>Nästa steg

[Testa och träna modellen](test-your-model.md)

