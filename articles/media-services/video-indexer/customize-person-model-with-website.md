---
title: Anpassa en personmodell med videoindexer webbplats
titleSuffix: Azure Media Services
description: Läs om hur du anpassar en personmodell med webbplatsen Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499977"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Anpassa en personmodell med webbplatsen Video Indexer

Video Indexer stöder kändisigenkänning för videoinnehåll. Funktionen kändisigenkänning täcker cirka en miljon ansikten baserat på vanliga datakällor som IMDB, Wikipedia och de främsta LinkedIn-influencers. En detaljerad översikt finns i [Anpassa en personmodell i Video Indexer](customize-person-model-overview.md).

Du kan använda webbplatsen Video Indexer för att redigera ansikten som har upptäckts i en video, enligt beskrivningen i det här avsnittet. Du kan också använda API:et enligt beskrivningen i [Anpassa en personmodell med API:er](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Central hantering av personmodeller i ditt konto

1. Om du vill visa, redigera och ta bort personmodellerna i ditt konto bläddrar du till videoindexerarens webbplats och loggar in.

2. Välj anpassningsknappen för innehållsmodell längst upp till höger på sidan.

    ![Anpassning av innehållsmodell](./media/customize-face-model/content-model-customization.png)

3. Välj fliken Kontakter.

    Du ser standardmodellen person i ditt konto. Standardpersonmodellen innehåller alla ansikten som du kan ha redigerat eller ändrat i statistiken för dina videor som du inte angav en anpassad personmodell för under indexeringen.

    Om du har skapat andra personmodeller visas de också på den här sidan.

    ![Anpassning av innehållsmodell](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Skapa en ny personmodell

1. Välj knappen **+ Lägg till modell.**

    ![Lägga till en ny personmodell](./media/customize-face-model/add-new-person.png)

2. Ange namnet på modellen och välj kontrollknappen bredvid namnet.

    ![Lägga till en ny personmodell](./media/customize-face-model/add-new-person2.png)

    Du har skapat en ny personmodell. Nu kan du lägga till ansikten i den nya personmodellen.

3. Välj listmenyknappen och välj **+ Lägg till person**.

    ![Lägga till en ny personmodell](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Lägga till en ny person i en personmodell

> [!NOTE]
> Med Video Indexer kan du lägga till flera personer med samma namn i en personmodell. Det rekommenderas dock att du ger unika namn till varje person i din modell för användbarhet och tydlighet.

1. Om du vill lägga till ett nytt ansikte i en personmodell markerar du listmenyknappen bredvid den personmodell som du vill lägga till ansiktet i.

1. Välj **+ Lägg till person** på menyn.

    ![Lägga till ett nytt ansikte till personen](./media/customize-face-model/add-new-face.png)

    Ett popup-fönster kommer att uppmana dig att fylla i personens uppgifter. Skriv in personens namn och välj checkknappen.

    ![Lägga till ett nytt ansikte till personen](./media/customize-face-model/add-new-face2.png)

    Du kan sedan välja från utforskaren eller dra och släppa ansiktsbilderna i ansiktet. Video Indexer tar alla standardbildfiltyper (t.ex. JPG, PNG med mera).

    Video Indexer kan identifiera förekomster av den här personen i framtida videor som du indexerar och de aktuella videorna som du redan har indexerat, med hjälp av den personmodell som du har lagt till det nya ansiktet till. Det kan ta lite tid att få en viss tid att få en tolkning av personen i dina aktuella videor, eftersom det här är en batchprocess.

## <a name="rename-a-person-model"></a>Byta namn på en personmodell

Du kan byta namn på vilken personmodell som helst i ditt konto, inklusive standardmodellen person. Även om du byter namn på din standardpersonmodell fungerar den fortfarande som standardpersonmodell i ditt konto.

1. Välj listmenyknappen bredvid den personmodell som du vill byta namn på.
2. Välj **Byt namn** på menyn.

    ![Byta namn på en personmodell](./media/customize-face-model/rename-person.png)

3. Välj det aktuella namnet på modellen och skriv in ditt nya namn.

    ![Byta namn på en personmodell](./media/customize-face-model/rename-person2.png)

4. Välj kontrollknappen för att din modell ska byta namn.

## <a name="delete-a-person-model"></a>Ta bort en personmodell

Du kan ta bort alla personmodeller som du har skapat i ditt konto. Du kan dock inte ta bort standardpersonmodellen.

1. Välj **Ta bort** på menyn.

    ![Ta bort en personmodell](./media/customize-face-model/delete-person.png)

    Ett popup-fönster visas och meddelar dig att den här åtgärden tar bort personmodellen och alla personer och filer som den innehåller. Den här åtgärden kan inte ångras.

    ![Ta bort en personmodell](./media/customize-face-model/delete-person2.png)

1. Om du är säker väljer du ta bort igen.

> [!NOTE]
> De befintliga videorna som indexerades med den här (nu borttagna) personmodellen stöder inte möjligheten för dig att uppdatera namnen på ansiktena som visas i videon. Du kan bara redigera namnen på ansiktena i dessa videor när du har indexera om dem med en annan personmodell. Om du indexerar om utan att ange en personmodell används standardmodellen.

## <a name="manage-existing-people-in-a-person-model"></a>Hantera befintliga personer i en personmodell

Om du vill titta på innehållet i någon av dina personmodeller väljer du pilen bredvid namnet på personmodellen. Listrutan visar dig alla människor i just personmodellen. Om du väljer listmenyknappen bredvid var och en av personerna visas alternativ för att hantera, byta namn på och ta bort.  

![Lägga till ett nytt ansikte till personen](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Byta namn på en person

1. Om du vill byta namn på en person i personmodellen markerar du listmenyknappen och väljer **Byt namn** på listmenyn.
1. Välj det aktuella namnet på personen och skriv in ditt nya namn.
1. Markera kontrollknappen så byter personen namn.

### <a name="delete-a-person"></a>Ta bort en person

1. Om du vill ta bort en person från personmodellen markerar du listmenyknappen och väljer **Ta bort** på listmenyn.
1. Ett popup-fönster visar att den här åtgärden kommer att ta bort personen och att den här åtgärden inte kan ångras.
1. Välj **Ta bort** igen och detta tar bort personen från personmodellen.

### <a name="manage-a-person"></a>Hantera en person

Om du väljer **Hantera**visas alla ansikten som den här personmodellen tränas från. Dessa ansikten kommer från förekomster av den personen i videor som använder den här personmodellen eller från bilder som du har laddat upp manuellt.

Du kan lägga till fler ansikten till personen genom att välja **Lägg till bilder**.

Du kan använda hanteringsfönstret för att byta namn på personen och ta bort personen från personmodellen.

## <a name="use-a-person-model-to-index-a-video"></a>Använda en personmodell för att indexera en video

Du kan använda en personmodell för att indexera din nya video genom att tilldela personmodellen under överföringen av videon.

Så här använder du din personmodell på en ny video:

1. Välj **knappen Ladda upp** högst upp på sidan.

    ![Ladda upp en personmodell](./media/customize-face-model/upload.png)

1. Släpp videofilen i cirkeln eller bläddra efter filen.
1. Välj pilen **Avancerade alternativ.**

    ![Ladda upp en personmodell](./media/customize-face-model/upload2.png)

1. Markera listrutan och välj den personmodell som du har skapat.

    ![Ladda upp en personmodell](./media/customize-face-model/upload3.png)

1. Välj alternativet **Ladda upp** längst ned på sidan så indexeras den nya videon med din personmodell.

Om du inte anger en personmodell under uppladdningen indexerar Video Indexer videon med standardmodellen För person i ditt konto.

## <a name="use-a-person-model-to-reindex-a-video"></a>Använda en personmodell för att indexera om en video

Om du vill använda en personmodell för att indexera om en video i samlingen går du till dina kontovideor på startsidan för Video Indexer och hovrar över namnet på videon som du vill indexera om.

Du ser alternativ för att redigera, ta bort och indexera om videon.

1. Välj alternativet om du vill indexera om videon.

    ![Använda personmodell för att indexera om en video](./media/customize-face-model/reindex.png)

    Nu kan du välja den personmodell som videon ska indexeras om med.
1. Markera listrutan och välj den personmodell som du vill använda.

    ![Använda personmodell för att indexera om en video](./media/customize-face-model/reindex2.png)

1. Välj knappen **Indexera om** så indexerar du om videon med din personmodell.

Alla nya redigeringar som du gör i ansikten som identifieras och känns igen i videon som du just indexerade om sparas i den personmodell som du använde för att indexera om videon.

## <a name="managing-people-in-your-videos"></a>Hantera personer i dina videoklipp

Du kan hantera ansikten som identifieras och personer som känns igen i de videor som du indexerar genom att redigera och ta bort ansikten.

Om du tar bort ett ansikte tas ett visst ansikte bort från videons insikter.

När du redigerar ett ansikte byter du namn på ett ansikte som identifieras och eventuellt känns igen i videon. När du redigerar ett ansikte i videon sparas det namnet som en personpost i personmodellen som tilldelades videon under uppladdning och indexering.

Om du inte tilldelar en personmodell till videon under överföringen sparas redigeringen i kontots standardpersonmodell.

### <a name="edit-a-face"></a>Redigera ett ansikte

> [!NOTE]
> Om en personmodell har två eller flera olika personer med samma namn kan du inte tagga det namnet i videorna som använder den personmodellen. Du kan bara göra ändringar för personer som delar namnet på fliken Kontakter på anpassningssidan för innehållsmodell i Video Indexer. Därför rekommenderar vi att du ger unika namn till varje person i din personmodell.

1. Gå till Video Indexer-webbplatsen och logga in.
1. Sök efter en video som du vill visa och redigera i ditt konto.
1. Om du vill redigera ett ansikte i videon går du till fliken Insikter och väljer pennikonen längst upp till höger i fönstret.

    ![Redigera ett ansikte i videon](./media/customize-face-model/edit-face.png)

1. Välj något av de identifierade ansiktena och ändra deras namn från "Okänd #X" (eller namnet som tidigare tilldelats ansiktet).
1. När du har skrivit in det nya namnet markerar du kontrollikonen bredvid det nya namnet. Den här åtgärden sparar det nya namnet och känner igen och namnger alla förekomster av det här ansiktet i dina andra aktuella videor och i framtida videor som du laddar upp. Det kan ta lite tid att få effekt när det här är en batchprocess.

Om du namnger ett ansikte med namnet på en befintlig person i personmodellen som videon använder, slås de identifierade ansiktsbilderna från den här videon på den personen samman med det som redan finns i modellen. Om du namnger ett ansikte med ett nytt namn skapas en ny personpost i den personmodell som videon använder.

![Redigera ett ansikte i videon](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Ta bort ett ansikte

Om du vill ta bort ett upptäckt ansikte i videon går du till fönstret Statistik och väljer pennikonen längst upp till höger i fönstret. Välj alternativet **Ta bort** under namnet på ansiktet. Den här åtgärden tar bort det identifierade ansiktet från videon. Personens ansikte kommer fortfarande att upptäckas i de andra videorna där det visas, men du kan ta bort ansiktet från dessa videor också efter att de har indexerats.

Personen, om personen hade namngetts, kommer också att finnas kvar i personmodellen som användes för att indexera videon som du har tagit bort ansiktet från om du inte specifikt tar bort personen från personmodellen.

![Ta bort ett ansikte i videon](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Nästa steg

[Anpassa personmodell med API:er](customize-person-model-with-api.md)
