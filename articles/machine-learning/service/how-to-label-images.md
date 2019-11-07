---
title: Använda Azure Machine Learnings verktyg för data etiketter
title.suffix: Azure Machine Learning
description: Den här artikeln lär dig hur du använder verktygen för data märkning i ett Azure Machine Learning etiketting-projekt.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: af12361ed11d0a16e5a5d0cfe5989bb3918ce154
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586399"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Tagga bilder i ett etikett projekt

När projekt administratören har skapat ett etikett projekt i Azure Machine Learning Studio kan du använda etikett verktyget för att snabbt förbereda data för ett Machine Learning-projekt. 

> [!div class="checklist"]
> * Få åtkomst till dina etikett projekt
> * Etikett verktyg
> * Så här använder du verktygen för vissa etikett uppgifter

## <a name="prerequisites"></a>Nödvändiga komponenter

* Etikettens portal-URL för ett projekt som kör en data etikett
* Ett [Microsoft-konto](https://account.microsoft.com/account) eller
* Ett Azure Active Directory konto för organisationen och projektet

> [!Note]
> Project-administratören kan hitta portal-URL: en för etiketten på fliken **information** på sidan **projekt information** . 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Logga in på projektets etikett Portal

Gå till den etikett portal-URL som du fått av projekt administratören. 

Logga in med e-postkontot som administratören använde för att lägga till dig i teamet. För de flesta användare görs inloggning med Microsoft-konto. Om ett etikett projekt använder Azure Active Directory är det så här som du loggar in. 

## <a name="understanding-the-labeling-task"></a>Förstå etikettens uppgift

När du har loggat in kommer du till projektets översikts sida. 

Det första du ska göra är att **Visa detaljerade instruktioner**. Dessa instruktioner är bara relevanta för ditt projekt och förklarar vilken typ av data du har, hur du ska fatta beslut och annan relevant information. När du är klar går du tillbaka till projekt sidan och väljer **Starta etiketter**.

## <a name="common-features-of-the-labeling-task"></a>Vanliga funktioner i aktivitets etiketten

Alla bild märknings uppgifter innebär att du väljer en lämplig tagg eller taggar från en mängd som anges av projekt administratören. Du kan välja bland de första nio taggarna med hjälp av siffer tangenterna på tangent bordet.  

Med bild klassificerings aktiviteter kan du välja att presentera flera bilder samtidigt. Du kan välja olika layouter med ikonerna ovanför bild-ytan. Du kan välja alla de bilder som visas samtidigt genom att trycka på knappen **Välj alla** . Välj enskilda foton med hjälp av cirkel vals knappen i det övre högra hörnet på bildens område. Du måste välja minst en bild för att kunna använda en tagg. Om du har valt flera bilder, kommer den taggen att tillämpas på alla valda foton om du väljer en tagg.

Här har vi valt en 2x2-layout och kommer att använda taggen "däggdjur" på avbildningarna av Bearer och Orca. Bilden med hajar har redan taggats som "Cartilaginous fiskar" och Iguana har ännu inte taggats.

![Flera bildlayouter och val](media/how-to-label-images/layouts.png)

> [!Important] 
> Växla bara layouter när du har en ny sida med omärkta data. När du växlar layouter rensas sidans pågående märknings arbete. 

Azure aktiverar knappen **Skicka** när du har taggat alla bilder på sidan. Spara ditt arbete genom att klicka på **Skicka** . 

När du har skickat taggar för de data som är i handen, kommer Azure att uppdatera sidan med en ny uppsättning avbildningar från arbets kön.  

## <a name="tagging-images-for-multi-class-classification"></a>Tagga bilder för klassificering med flera klasser

Om ditt projekt är av typen "bild klassificering med flera klasser", tilldelar du en enskild tagg till hela bilden. Du kan när som helst välja sidan **instruktioner** och navigera för att **Visa detaljerade instruktioner** för att se projekt särskilda rikt linjer. 

Som tidigare nämnts kan du välja bland flera olika layouter för att presentera bilder. Om du efter att ha valt en bild och tilldelar den en tagg, inser du att du har gjort ett misstag. Om du i den etikett som visas under bilden klickar på `X` målet, raderas taggen. Eller, om du väljer avbildningen och väljer en annan klass växlar taggen till det nya värdet.

## <a name="tagging-images-for-multi-label-classification"></a>Tagga bilder för klassificering med flera etiketter

Om du arbetar med ett projekt av typen "bild klassificering med flera etiketter", använder du en _eller flera_ taggar till en bild. Du kan när som helst välja sidan **instruktioner** och navigera för att **Visa detaljerade instruktioner** för att se projekt särskilda rikt linjer. 

Välj den bild som du vill märka och klicka sedan på taggen. Om du väljer taggen tillämpas den på alla valda bilder och avmarkeras. Om du vill använda fler taggar måste du markera bilderna igen. Den här animeringen visar en tagg för flera etiketter:

* **Markera allt** används för att tillämpa taggen "oceanen"
* En enda bild markeras och märks "närbild"
* Tre bilder är markerade och taggade "bred vinkel"

![Animering som visar multilabel-flöde](media/how-to-label-images/multilabel.gif)

Om du vill korrigera ett misstag kan du antingen klicka på `X` för att ta bort enskilda taggar eller välja bilderna, och sedan välja taggen som tar bort taggen från alla markerade bilder. Det här scenariot visas här, där när du klickar på "land" raderas taggen från de två valda bilderna.

![Skärm bild som visar flera avmarkerare](media/how-to-label-images/multiple-deselection.png)

Azure aktiverar bara knappen **Skicka** när du har tillämpat minst en tagg på varje bild. Tryck på **Skicka** för att spara ditt arbete.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Tagga bilder och avgränsnings rutor för objekt identifiering

Om ditt projekt är av typen objekt identifiering (markerings rutor) ", anger du en eller flera markerings rutor i bilden och använder en tagg i rutan. Varje bild kan ha flera avgränsnings rutor, var och en med en enda tagg. Använd **Visa detaljerade instruktioner** för att avgöra om det är lämpligt att lägga till flera avgränsnings rutor för ditt projekt.

1. Välj en tagg för den markerings ruta som du vill skapa
1. Välj den **rektangulära rutan** ![rektangulärt Box-verktyget](media/how-to-label-images/rectangular-box-tool.png) eller tryck på R 
1. Klicka och dra diagonalt över ditt mål för att skapa en grov markerings ruta
    * Justera avgränsnings rutan genom att klicka och dra i rutans kanter eller hörn

![Skärm bild av en grundläggande avgränsnings ruta](media/how-to-label-images/bounding-box-sequence.png)

Om du vill ta bort markerings rutan klickar du på det X-formade mål som visas bredvid markerings rutan när du har skapat den.

Du får inte omtilldela taggen för en befintlig avgränsnings ruta. Om du gör ett misstag med tagg tilldelning måste du ta bort markerings rutan och skapa en ny med rätt tagg.

Som standard kan befintliga avgränsnings rutor redige ras. Verktyget **Lock/unlock regions** ![lås/Lås upp regioner](media/how-to-label-images/lock-bounding-boxes-tool.png) eller "L" växlar det beteendet. Om regionerna är låsta kan du bara ändra form eller plats för en ny avgränsnings ruta.

Använd ![områdes **manipulations** verktyg för att ändra en befintlig avgränsnings ruta](media/how-to-label-images/regions-tool.png) eller "M". Du kan justera formen genom att klicka och dra i kanterna eller hörnen. Om du klickar på insidan kan du dra hela avgränsnings rutan. Om du inte kan redigera en region har du förmodligen växlat **Lås/Lås upp** regions verktyget. 

Använd det **mallbaserade Box-** verktyget för ![mall](media/how-to-label-images/template-box-tool.png) eller "t" för att skapa flera avgränsnings rutor av samma storlek. Om bilden inte har några markerings rutor och du aktiverar mallbaserade rutor kommer verktyget att skapa 50x50 pixel rutor. Om du har skapat en avgränsnings ruta och sedan aktiverar mallbaserade rutor, blir nya avgränsnings rutor storleken på den sista som du har gjort. Du kan ändra storlek på mallbaserade rutor efter placering. Att ändra storlek på en mall-baserad ruta ändrar bara storlek på den aktuella rutan. 

Om du vill ta bort _alla_ avgränsnings rutor i den aktuella bilden kan du välja verktyget **ta bort alla regioner** ![ta bort regioner](media/how-to-label-images/delete-regions-tool.png). 

När du har skapat markerings rutorna för bilden trycker du på **Skicka** för att spara ditt arbete. Pågående arbete sparas inte om du inte trycker på **Skicka** -knappen. 

## <a name="finishing-up"></a>Slutför 

När du skickar en sida med taggade data tilldelar Azure dig nya omärkta data från en arbetskö. Om det inte finns några fler omärkta data visas ett meddelande om detta, med en länk tillbaka till portalens start sida. 

Om du vet att du inte ska göra fler etiketter väljer du ditt namn i det övre högra hörnet på etikett portalen och väljer sedan **Logga in**. Om du inte loggar ut så kommer Azure att "tid ut" och tilldela dina data till en annan Labeler. 

## <a name="next-steps"></a>Nästa steg

* Lär dig att [träna avbildnings klassificerings modeller i Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Läs om [objekt identifiering med Azure och den snabbare R-CNN-tekniken](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)