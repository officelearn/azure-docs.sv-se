---
title: Tagga bilder i ett etikettprojekt
title.suffix: Azure Machine Learning
description: Lär dig hur du använder datataggningsverktygen i ett Azure Machine Learning-märkningsprojekt.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 371b99c794feb4a64eb3e9af389020e25d14eedb
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879436"
---
# <a name="tag-images-in-a-labeling-project"></a>Tagga bilder i ett etikettprojekt

När projektadministratören [har skapat ett märkningsprojekt](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) i Azure Machine Learning kan du använda etikettverktyget (offentlig förhandsversion) för att snabbt förbereda data för ett Machine Learning-projekt. I den här artikeln beskrivs:

> [!div class="checklist"]
> * Så här kommer du åt dina märkningsprojekt
> * Märkningsverktygen
> * Så här använder du verktygen för specifika märkningsuppgifter

## <a name="prerequisites"></a>Krav

* Url:en för etikettportalen för ett dataetikettprojekt som körs
* Ett [Microsoft-konto](https://account.microsoft.com/account) eller ett Azure Active Directory-konto för organisationen och projektet

> [!NOTE]
> Projektadministratören kan hitta den som anger portal-URL:en på fliken **Information på** sidan **Projektinformation.**

## <a name="sign-in-to-the-projects-labeling-portal"></a>Logga in på projektets etikettportal

Gå till den url för etikettportalen som tillhandahålls av projektadministratören. Logga in med hjälp av det e-postkonto som administratören använde för att lägga till dig i teamet. För de flesta användare är det ditt Microsoft-konto. Om märkningsprojektet använder Azure Active Directory loggar du in så här.

## <a name="understand-the-labeling-task"></a>Förstå märkningsuppgiften

När du har loggat in visas projektets översiktssida.

Gå till **Visa detaljerade instruktioner**. Dessa instruktioner är specifika för ditt projekt. De förklarar vilken typ av data du står inför, hur du ska fatta dina beslut och annan relevant information. När du har läst den här informationen går du tillbaka till projektsidan och väljer **Börja märka**.

## <a name="common-features-of-the-labeling-task"></a>Vanliga funktioner i märkningsuppgiften

I alla bildetiketteringsuppgifter väljer du en lämplig tagg eller taggar från en uppsättning som anges av projektadministratören. Du kan välja de första nio taggarna med hjälp av sifferknapparna på tangentbordet.  

I bildklassificeringsuppgifter kan du välja att visa flera bilder samtidigt. Använd ikonerna ovanför bildområdet för att välja layouten. 

Om du vill markera alla bilder som visas samtidigt använder **du Markera alla**. Om du vill markera enskilda bilder använder du den cirkulära markeringsknappen i bildens övre högra hörn. Du måste markera minst en bild om du vill använda en tagg. Om du väljer flera bilder används alla taggar som du väljer på alla markerade bilder.

Här har vi valt en två-av-två layout och är på väg att tillämpa taggen "Däggdjur" på bilderna av björnen och orca. Bilden av hajen var redan märkt som "Broskfisk", och leguanen har inte märkts ännu.

![Flera bildlayouter och markeringar](./media/how-to-label-images/layouts.png)

> [!Important] 
> Växla bara layouter när du har en ny sida med omärkta data. Om du byter layout rensas sidans pågående taggningsarbete.

Azure aktiverar **skicka-knappen** när du har taggat alla bilder på sidan. Välj **Skicka för** att spara ditt arbete.

När du har skickat taggar för de ställda data uppdaterar Azure sidan med en ny uppsättning avbildningar från arbetskön.

### <a name="assisted-machine-learning"></a>Assisterad maskininlärning 

Maskininlärningsalgoritmer kan utlösas under en klassificeringsuppgift med flera klasser eller flera etiketter. Om dessa algoritmer är aktiverade i projektet kan följande visas:

* När en viss mängd bilder har märkts kan aktiviteter **som grupperas** högst upp på skärmen bredvid projektnamnet visas.  Det innebär att bilder grupperas tillsammans för att presentera liknande bilder på samma sida.  Om så är fallet växlar du till en av de flera bildvyerna för att dra nytta av gruppningen.  

* Vid ett senare tillfälle kan aktiviteter **som är förmärkta bredvid** projektnamnet visas.  Bilder visas sedan med en föreslagen etikett som kommer från en maskininlärningsklassificeringsmodell. Ingen maskininlärningsmodell har 100% noggrannhet. Även om vi bara använder bilder som modellen är säker på, kan dessa bilder fortfarande vara felaktigt förmärkta.  När du ser dessa etiketter korrigerar du felaktiga etiketter innan du skickar in sidan.  

Särskilt tidigt i ett märkningsprojekt kan maskininlärningsmodellen bara vara tillräckligt exakt för att förmäta en liten delmängd av bilder. När dessa bilder har märkts återgår etikettprojektet till manuell märkning för att samla in mer data för nästa omgång av modellutbildningen. Med tiden kommer modellen att bli mer säker på en högre andel bilder, vilket resulterar i fler förmätbara aktiviteter senare i projektet.

## <a name="tag-images-for-multi-class-classification"></a>Tagga bilder för klassificering i flera klasser

Om projektet är av typen "Bildklassificering multiklass" tilldelar du en enda tagg till hela bilden. Om du vill granska anvisningarna när som helst går du till sidan **Instruktioner** och väljer **Visa detaljerade instruktioner**.

Om du inser att du har gjort ett misstag när du har tilldelat en tagg till en bild kan du åtgärda det. Välj "**X**" på etiketten som visas under bilden för att rensa taggen. Du kan också markera bilden och välja en annan klass. Det nyligen valda värdet ersätter den tidigare använda taggen.

## <a name="tag-images-for-multi-label-classification"></a>Tagga bilder för klassificering med flera etiketter

Om du arbetar med ett projekt av typen "Bildklassificering multietikett" ska du använda en *eller flera* taggar på en bild. Om du vill se de projektspecifika anvisningarna väljer du **Instruktioner** och går till **Visa detaljerade instruktioner**.

Markera den bild som du vill märka och markera taggen. Taggen används på alla markerade bilder och sedan avmarkeras bilderna. Om du vill använda fler taggar måste du markera bilderna igen. Följande animering visar taggning med flera etiketter:

1. **Markera alla** används för att använda taggen "Ocean".
1. En enda bild väljs och har märkts med etiketterna "Närbild".
1. Tre bilder är markerade och märkta "Vidvinkel".

![Animering visar flera etikettflöde](./media/how-to-label-images/multilabel.gif)

Om du vill rätta till ett misstag klickar du på "**X**" för att ta bort en enskild tagg eller markera bilderna och sedan markera taggen, som rensar taggen från alla markerade bilder. Det här scenariot visas här. Om du klickar på "Land" kommer taggen att rensas från de två valda bilderna.

![En skärmbild visar flera avval](./media/how-to-label-images/multiple-deselection.png)

Azure aktiverar bara **skicka-knappen** när du har använt minst en tagg på varje avbildning. Välj **Skicka för** att spara ditt arbete.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Tagga bilder och ange markeringsrutor för objektidentifiering

Om projektet är av typen "Objektidentifiering (markeringsrutor)," anger du en eller flera markeringsrutor i bilden och använder en tagg på varje ruta. Bilder kan ha flera markeringsrutor, var och en med en enda tagg. Använd **Visa detaljerade instruktioner** för att avgöra om flera markeringsrutor används i projektet.

1. Markera en tagg för den markeringsram som du planerar att skapa.
1. Markera det **rektangulära rutverktyget** ![Rektangulärt rutaverktyg](./media/how-to-label-images/rectangular-box-tool.png) eller välj "R".
3. Klicka och dra diagonalt över målet för att skapa en grov markeringsram. Om du vill justera markeringsramen drar du i kanterna eller hörnen.

![En skärmbild visar grundläggande skapande av begränsningsramar.](./media/how-to-label-images/bounding-box-sequence.png)

Om du vill ta bort en markeringsram klickar du på det X-formade mål som visas bredvid markeringsramen när den har skapats.

Du kan inte ändra taggen för en befintlig markeringsram. Om du gör ett misstag med taggtilldelning måste du ta bort markeringsramen och skapa en ny med rätt tagg.

Som standard kan du redigera befintliga markeringsrutor. Verktyget **Lås/lås upp regioner** ![Lås/lås upp regioner](./media/how-to-label-images/lock-bounding-boxes-tool.png) eller "L" växlar upp det beteendet. Om regioner är låsta kan du bara ändra formen eller platsen för en ny begränsningsram.

Använd **verktyget Regioner manipuleringsverktyget** ![](./media/how-to-label-images/regions-tool.png) Regioner eller "M" för att justera en befintlig markeringsram. Justera formen genom att dra i kanterna eller hörnen. Klicka i det inre för att kunna dra hela markeringsramen. Om du inte kan redigera en region har du förmodligen växlat mellan verktyget **Lås/lås upp regioner.**

Använd **mallverktyget** ![Mallruta](./media/how-to-label-images/template-box-tool.png) eller "T" för att skapa flera markeringsrutor av samma storlek. Om bilden inte har några begränsningsrutor och du aktiverar mallbaserade rutor kommer verktyget att producera 50 x 50-pixelrutor. Om du skapar en begränsningsram och sedan aktiverar mallbaserade rutor, kommer alla nya markeringsrutor att vara storleken på den sista rutan som du skapade. Mallbaserade rutor kan storleksas efter placering. Om du ändrar storlek på en mallbaserad ruta ändras bara den rutan.

Om du vill ta bort *alla* markeringsrutor i ![den aktuella bilden markerar du verktyget](./media/how-to-label-images/delete-regions-tool.png) **Ta bort alla regioner** Ta bort områden .

När du har skapat markeringsrutorna för en bild väljer du **Skicka** för att spara ditt arbete, annars sparas inte det pågående arbetet.

## <a name="finish-up"></a>Slutför

När du skickar en sida med taggade data tilldelar Azure nya omärkta data till dig från en arbetskö. Om det inte finns fler omärkta data tillgängliga får du ett meddelande om detta tillsammans med en länk till portalens startsida.

När du är klar med etiketterna väljer du ditt namn i det övre högra hörnet på etikettportalen och väljer sedan **ut logga ut**. Om du inte loggar ut kommer Azure så småningom att "time you out" och tilldela dina data till en annan etikett.

## <a name="next-steps"></a>Nästa steg

* Lär dig att [träna bildklassificeringsmodeller i Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

