---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903671"
---
## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

> [!NOTE]
> Om du har registrerat dig för att använda delade bildgallerier `Microsoft.Compute` under förhandsgranskningen kan du behöva registrera leverantören igen. Öppna [Cloud Shell](https://shell.azure.com/bash) och skriv:`az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Skapa ett bildgalleri

Ett bildgalleri är den primära resurs som används för att aktivera bilddelning. Tillåtna tecken för Gallerinamn är versaler eller gemener, siffror, punkter och punkter. Gallerinamnet får inte innehålla streck.  Gallerinamn måste vara unika i din prenumeration. 

I följande exempel skapas ett galleri med namnet *myGallery* i resursgruppen *myGalleryRG.*

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Använd typen **Delat bildgalleri** i sökrutan och välj **Delat bildgalleri** i resultaten.
1. Klicka på **Skapa**på sidan **Delat bildgalleri.**
1. Välj rätt prenumeration.
1. Välj **Skapa nytt** i **resursgruppen**och skriv *myGalleryRG* för namnet.
1. Skriv *myGallery* för namnet på galleriet i **Namn.**
1. Lämna standardvärdet för **Region**.
1. Du kan skriva en kort beskrivning av galleriet, till *exempel Mitt bildgalleri för testning.* och klicka sedan på **Granska + skapa**.
1. När valideringen har gått väljer du **Skapa**.
1. När distributionen är klar väljer du **Gå till resurs**.
   
## <a name="create-an-image-definition"></a>Skapa en bilddefinition 

Bilddefinitioner skapar en logisk gruppering för bilder. De används för att hantera information om de bildversioner som skapas i dem. Bilddefinitionsnamn kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. Mer information om de värden du kan ange för en bilddefinition finns i [Bilddefinitioner](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Skapa bilddefinitionen för galleriet i galleriet. I det här exemplet heter galleribilden *myImageDefinition*.

1. På sidan för det nya bildgalleriet väljer du **Lägg till en ny bilddefinition** högst upp på sidan. 
1. Skriv *myImageDefinition*för **namn på Bilddefinition**.
1. För **operativsystem**väljer du rätt alternativ baserat på käll-VM.
1. För **generering av virtuella datorer**väljer du alternativet baserat på käll-VM. I de flesta fall kommer detta att vara *Gen 1*. Mer information finns i [Stöd för virtuella datorer för generation 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. För **operativsystemtillstånd**väljer du alternativet baserat på käll-VM. Mer information finns i [Generaliserad och specialiserad](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. Skriv *myPublisher*för **Publisher**. 
1. För **Erbjudande,** skriv *myOffer*.
1. För **SKU**skriver du *mySKU*.
1. När du är klar väljer du **Granska + skapa**.
1. När bilddefinitionen har godkänts väljer du **Skapa**.
1. När distributionen är klar väljer du **Gå till resurs**.


## <a name="create-an-image-version"></a>Skapa en bildversion

Skapa en bildversion från en hanterad avbildning. I det här exemplet är avbildningsversionen *1.0.0* och den replikeras till både datacenter i *Västra centrala USA* och södra centrala *USA.* När du väljer målområden för replikering bör du komma ihåg att du också måste inkludera *källregionen* som ett mål för replikering.

Tillåtna tecken för bildversion är siffror och punkter. Talen måste ligga inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Patch*.

Stegen för att skapa en bildversion är något olika, beroende på om källan är en generaliserad avbildning eller en ögonblicksbild av en specialiserad virtuell dator. 

### <a name="option-generalized"></a>Alternativ: Generaliserad

1. På sidan för bilddefinitionen väljer du **Lägg till version** högst upp på sidan.
1. I **Region**väljer du den region där den hanterade avbildningen lagras. Avbildningsversioner måste skapas i samma region som den hanterade avbildningen som de skapas från.
1. För **Namn**skriver du *1.0.0*. Namnet på bildversionen ska följa *större*. *mindre*. *korrigeringsformat* med heltal. 
1. Välj källhanterad bild i listrutan **i källbilden.**
1. Lämna standardvärdet *no.* i **Uteslut från senaste**.
1. För **slutdatum väljer**du ett datum i kalendern som är ett par månader i framtiden.
1. Lämna **standardreplikeringsantalet** som 1 i **Replikering.** Du måste replikera till källregionen, så lämna den första repliken som standard och välj sedan en andra replikregion som *östra USA*.
1. När du är klar väljer du **Granska + skapa**. Azure validerar konfigurationen.
1. När bildversionen godkänns väljer du **Skapa**.
1. När distributionen är klar väljer du **Gå till resurs**.

Det kan ta ett tag att replikera avbildningen till alla målområden.

### <a name="option-specialized"></a>Alternativ: Specialiserad

1. På sidan för bilddefinitionen väljer du **Lägg till version** högst upp på sidan.
1. I **Region**väljer du den region där ögonblicksbilden lagras. Bildversioner måste skapas i samma region som källan de skapas från.
1. För **Namn**skriver du *1.0.0*. Namnet på bildversionen ska följa *större*. *mindre*. *korrigeringsformat* med heltal. 
1. I **ögonblicksbild av OS-disk**väljer du ögonblicksbilden från källdatorns dator i listrutan. Om källdatorn hade en datadisk som du vill ta med väljer du rätt **LUN-nummer** i listrutan och väljer sedan ögonblicksbilden av datadisken för ögonblicksbild av **datadisken**. 
1. Lämna standardvärdet *no.* i **Uteslut från senaste**.
1. För **slutdatum väljer**du ett datum i kalendern som är ett par månader i framtiden.
1. Lämna **standardreplikeringsantalet** som 1 i **Replikering.** Du måste replikera till källregionen, så lämna den första repliken som standard och välj sedan en andra replikregion som *östra USA*.
1. När du är klar väljer du **Granska + skapa**. Azure validerar konfigurationen.
1. När bildversionen godkänns väljer du **Skapa**.
1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="share-the-gallery"></a>Dela galleriet

Vi rekommenderar att du delar åtkomst på bildgallerinivå. Följande går igenom att dela galleriet som du just skapade.

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**på menyn till vänster . 
1. Välj **myGalleryRG**i listan över resursgrupper . Bladet för resursgruppen öppnas.
1. I menyn till vänster på **myGalleryRG-sidan** väljer du **Access control (IAM).** 
1. Under **Lägg till en rolltilldelning**väljer du Lägg **till**. Fönstret **Lägg till en rolltilldelning** öppnas. 
1. Under **Roll**väljer du **Läsare**.
1. Under **tilldela åtkomst till**lämnar du standardvärdet för Azure **AD-användare, grupp eller tjänsthuvudnamn**.
1. Skriv in e-postadressen till den person som du vill bjuda in under **Välj.**
1. Om användaren befinner sig utanför organisationen visas meddelandet **Den här användaren får ett e-postmeddelande som gör att de kan samarbeta med Microsoft.** Markera användaren med e-postadressen och klicka sedan på **Spara**.

Om användaren befinner sig utanför organisationen får de en e-postbjudan för att gå med i organisationen. Användaren måste acceptera inbjudan, då de kommer att kunna se galleriet och alla bilddefinitioner och versioner i sin lista över resurser.

