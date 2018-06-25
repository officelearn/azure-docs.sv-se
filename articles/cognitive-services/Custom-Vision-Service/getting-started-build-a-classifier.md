---
title: Skapa en klassificerare med anpassade Vision Service - kognitiva Azure-tjänster | Microsoft Docs
description: Lär dig mer om att använda anpassade Vision tjänsten för att skapa en klassificerare som kan urskilja objekt i foton.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 6dc271c13f53a445c7d1101f5264d890208bd03c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352908"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Hur du skapar en klassificerare med anpassade Vision

Om du vill använda anpassade Vision tjänsten måste du först skapa en klassificerare. I det här dokumentet beskrivs hur du skapar en klassificerare via webbläsaren.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en klassificerare, måste du:

- En giltig [Microsoft-konto](https://account.microsoft.com/account) eller ett Azure Active Directory OrgID (”arbets- eller skolkonto konto”), så att du kan logga in på customvision.ai och komma igång.

    > [!IMPORTANT] 
    > OrgID-inloggning för användare i Azure Active Directory (AD Azure) från [nationella moln](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) stöds inte för närvarande.

- En serie bilder att träna din klassificerare (med minst 30 bilder per tagg).

- Några bilder att testa din klassificerare när klassificeraren tränats.

- Valfritt: En Azure-prenumeration som är associerade med din Account eller OrgID. Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

    > [!IMPORTANT]
    > Utan en Azure-prenumeration, du kan bara skapa __begränsad utvärderingsversion__ projekt. Om du har en Azure-prenumeration uppmanas du att skapa anpassade Vision Service utbildning och förutsägelse resurser i den [Azure-portalen](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) under projektet har skapats.   

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

Om du vill skapa ett nytt projekt, använder du följande steg:

1. I din webbläsare, navigerar du till den [anpassad Vision webbsida](https://customvision.ai). Välj __inloggning__ kan börja använda tjänsten.

    ![Bild av sidan logga in](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > När du loggar in på anpassad Vision tjänsten visas en lista över projekt. Utanför två ”begränsad utvärderingsversion” projekt för att testa projekt som är kopplade till en Azure-resurs. Om du är en Azure användare visas alla projekt som är associerade med [Azure-resurser](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) som du har åtkomst till. 

2. För att skapa ditt första projekt, Välj **nytt projekt**. För ditt första projekt uppmanas du att samtycker till användarvillkoren. Markera kryssrutan och välj sedan den **acceptera** knappen. Den **nytt projekt** dialogrutan visas.

    ![Dialogrutan Nytt projekt har fälten för namn, beskrivning och domäner.](./media/getting-started-build-a-classifier/new-project.png)

3. Ange ett namn och en beskrivning för projektet. Välj sedan något av de tillgängliga domänerna. Varje domän optimerar klassificerare för vissa typer av avbildningar, enligt beskrivningen i följande tabell:

    |Domän|Syfte|
    |---|---|
    |__Generisk__| Optimerad för ett brett spektrum av avbildningen klassificering uppgifter. Om ingen av de andra domänerna är lämpligt, eller du är osäker på vilken domän som du väljer, Välj den allmänna domänen. |
    |__Mat__|Optimerad för fotografier av disk som du skulle se dem på en restaurang-meny. Om du vill klassificera fotografier av enskilda frukter eller grönsaker Använd mat-domänen.|
    |__Landmärken__|Optimerad för att identifiera landmärken, både fysiska och artificiella. Den här domänen fungerar bäst när Landmärke tydligt visas i fotot. Den här domänen fungerar även om Landmärke något hindras av personer framför det.|
    |__Butik__|Optimerad för avbildningar som finns i en i katalogen eller Shopping. Om du vill hög precision klassificera mellan klänningar, byxor och skjortor kan du använda den här domänen.|
    |__Vuxen__|Optimerad för att definiera en bättre vuxet innehåll och icke-vuxen innehåll. Till exempel om du vill blockera avbildningar av personer i badning passar kan den här domänen du skapa en anpassad klassificerare för att göra det.|
    |__Compact domäner__| Optimerad för begränsningar i realtid klassificering på mobila enheter. Modeller som genererats av compact domäner kan exporteras om du vill köra lokalt.|

    Du kan ändra domänen senare om du vill.

4. Välj en resursgrupp. Resursgruppen listrutan visas alla dina Azure-resursgrupper som innehåller en anpassad Vision tjänstresurs. Du kan också skapa väljer __begränsad utvärderingsversion__. Begränsad utvärderingsversion transaktionen är en Azure-användare kommer att kunna välja endast resursgruppen.

    För att skapa projektet, Välj __skapa projekt__.

## <a name="upload-and-tag-images"></a>Ladda upp och tagg bilder

1. Om du vill lägga till avbildningar i klassificeraren använder den __lägga till bilder__ och välj sedan __Bläddra lokala filer__. Välj __öppna__ att flytta till Taggning.

    > [!TIP]
    > När du har valt bilder, måste du tagga dem. Taggen tillämpas grupp med bilder som du har valt att ladda upp, så det kan vara enklare att ladda upp bilder efter taggar som du tänker använda. Du kan också ändra taggen för valda avbildningar efter att de har taggats och har överförts.

    > [!TIP]
    > Ladda upp bilder med olika kameravinklar, ljus, bakgrund, typer, formatmallar, grupper, storlek och så vidare. Använda en mängd olika fototyper så att din klassificerare inte prioriterar och kan också generalisera.

    Anpassade Vision tjänsten accepterar utbildning bilder i JPG-, PNG- och BMP-format, upp till 6 MB per bild. (Förutsägelse avbildningar kan vara upp till 4 MB per bild.) Vi rekommenderar att bilder är 256 bildpunkter på kortaste kant. Alla avbildningar som är kortare än 256 bildpunkter på kortaste kant skalas av anpassade Vision-tjänsten.

    ![Lägg till bilder kontrollen visas i det övre vänstra hörnet och en knapp med längst ned i mitten.](./media/getting-started-build-a-classifier/add-images01.png)

    ![Bläddringsknappen lokala filer visas nästan längst ned i mitten.](./media/getting-started-build-a-classifier/add-images02.png)

    >[!NOTE] 
    > REST-API kan användas för att läsa in utbildning bilder från URL: er.

2. Om du vill ställa in taggen ange text i den __Mina taggar__ fältet och sedan använda den __+__ knappen. Ladda upp bilder och tagga dem genom att använda den __ladda upp filer [nummer]__ knappen. Du kan lägga till mer än en etikett avbildningar. 

    > [!NOTE]
    > Tid varierar beroende på antalet och storleken på bilder som du har valt.

    ![Bild av sidan taggen och ladda upp](./media/getting-started-build-a-classifier/add-images03.png)

3. Välj __klar__ när bilderna som har överförts.

    ![Förloppsindikatorn visar alla aktiviteter har slutförts.](./media/getting-started-build-a-classifier/add-images04.png)

4. Gå tillbaka till steg 1 för att ladda upp en annan uppsättning avbildningar. Om du vill att skilja mellan hund och ponnyer, ladda upp och tagga avbildningar av ponnyer.

## <a name="train-and-evaluate-the-classifier"></a>Träna och utvärdera klassificeraren

För att träna klassificeraren, Välj den **träna** knappen.

![Knappen tåget är rätt överst i webbläsarfönstret.](./media/getting-started-build-a-classifier/train01.png)

Det tar bara några minuter för att träna klassificeraren. Under denna tid visas information om processen för utbildning.

![Knappen tåget är rätt överst i webbläsarfönstret.](./media/getting-started-build-a-classifier/train02.png)

Efter utbildning, den __prestanda__ visas. Precision och återkalla indikatorer berättar hur bra din klassificerare, baserat på automatisk testning. Anpassade Vision tjänsten använder de avbildningar som du skickade för utbildning för att beräkna dessa siffror med hjälp av en process som kallas [k-vikning mellan validering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![Utbildning resultatet visar övergripande precision och återkallande och precisionen och återkalla för varje tagg i klassificeraren.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Varje gång du väljer den **Train** knapp och du skapar en ny iteration av din klassificerare. Du kan visa alla gamla iterationer i den **prestanda** , och du kan ta bort de som kan vara inaktuell. När du tar bort en iteration avslutas tar bort eventuella avbildningar som är unikt kopplade till den.

Klassificeraren använder alla avbildningar för att skapa en modell som identifierar varje tagg. Om du vill testa kvaliteten på modellen försöker klassificeraren varje avbildning på modellen finns modellen hittar.

Samma egenskaper som klassificerare resultat visas.

|Period|Definition|
|---|---|
|__Precision__|När du klassificerar en avbildning, hur troligt är din klassificerare korrekt klassificera bilden? Slut på alla bilder som används för att träna klassificeraren (hund och ponnyer), hur många procent modellen fick korrekt? 99 rätt taggar utanför 100 bilder ger en precision 99%.|
|__Återkalla__|Slut på alla bilder som bör klassificeras på rätt sätt, hur många din klassificerare identifieras korrekt? Återkalla 100% innebär att om det finns 38 hund bilder i bilder som användes för att träna klassificeraren, klassificerare hittas 38 hund.|

## <a name="next-steps"></a>Nästa steg

[Testa och träna om modellen](test-your-model.md)

