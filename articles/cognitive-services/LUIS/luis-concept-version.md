---
title: Versions hantering – LUIS
titleSuffix: Azure Cognitive Services
description: Versioner, i LUIS, liknar versioner i traditionell programmering. Varje version är en ögonblicks bild i appens tid. Innan du gör ändringar i appen skapar du en ny version. Det är enklare att gå tillbaka till den exakta appen, och sedan prova att avskala och appars avsikts-och yttranden till ett tidigare tillstånd.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2e13efa70d0344defeb306a92ac405439635e929
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619697"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Förstå hur och när du ska använda en LUIS-version

Versioner, i LUIS, liknar versioner i traditionell programmering. Varje version är en ögonblicks bild i appens tid. Innan du gör ändringar i appen skapar du en ny version. Det är enklare att gå tillbaka till den exakta versionen och sedan försöka ta bort intentor och yttranden till ett tidigare tillstånd.

Skapa olika modeller av samma app med [versioner](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Versions-ID
Versions-ID: t består av tecken, siffror eller "." och får inte vara längre än 10 tecken.

## <a name="initial-version"></a>Ursprunglig version
Den första versionen (0,1) är den aktiva standard versionen. 

## <a name="active-version"></a>Aktiv version
Om du vill [Ange en version](luis-how-to-manage-versions.md#set-active-version) som aktiv betyder det att den är för närvarande redige rad och testad på [Luis](luis-reference-regions.md) webbplats. Ange en version som aktiv för att komma åt dess data, gör uppdateringar och testa och publicera den.

Namnet på den aktuella aktiva versionen visas i den övre vänstra panelen efter appens namn. 

[![Ändra aktiv version](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versioner och publicerings platser
Du publicerar på antingen scenen och produkt platserna. Varje plats kan ha en annan version eller samma version. Detta är användbart för att verifiera ändringar mellan modell versioner via slut punkten, som är tillgänglig för robotar eller andra LUIS som anropar program. 

## <a name="clone-a-version"></a>Klona en version
Klona en version för att skapa en kopia av en befintlig version och spara den som en ny version. Klona en version för att använda samma innehåll i den befintliga versionen som en start punkt för den nya versionen. När du klonar en version blir den nya versionen den **aktiva** versionen. 

## <a name="import-and-export-a-version"></a>Importera och exportera en version
Du kan importera en version på App-nivå. Den versionen blir den aktiva versionen och använder versions-ID: t i egenskapen "versionId" för app-filen. Du kan också importera på versions nivå till en befintlig app. Den nya versionen blir den aktiva versionen. 

Du kan exportera en version på App-nivå eller så kan du exportera en version på versions nivå. Den enda skillnaden är att den version som exporter ATS på program nivå är den för närvarande aktiva versionen på versions nivå. du kan välja vilken version du vill exportera på sidan **[Inställningar](luis-how-to-manage-versions.md)** . 

Den exporterade filen innehåller ingen information som är känd för datorn eftersom appen omtränas när den har importer ATS. Den exporterade filen innehåller inga samarbets partners – du måste lägga till dem igen när versionen har importer ATS till den nya appen.

## <a name="export-each-version-as-app-backup"></a>Exportera varje version som säkerhets kopiering av app
För att kunna säkerhetskopiera LUIS-appen exporterar du varje version på sidan **[Inställningar](luis-how-to-manage-versions.md)** .

## <a name="delete-a-version"></a>Ta bort en version
Du kan ta bort alla versioner utom den aktiva versionen från listan versioner på sidan Inställningar. 

## <a name="version-availability-at-the-endpoint"></a>Versions tillgänglighet vid slut punkten
Utbildade versioner är inte automatiskt tillgängliga i din app- [slutpunkt](luis-glossary.md#endpoint). Du måste [publicera](luis-how-to-publish-app.md) eller publicera om en version för att den ska vara tillgänglig på din app-slutpunkt. Du kan publicera till **mellanlagring** och **produktion**, vilket ger dig upp till två versioner av appen som är tillgängliga vid slut punkten. Om du behöver fler versioner av appen tillgänglig vid en slut punkt bör du exportera versionen och importera den till en ny app. Den nya appen har ett annat app-ID.

## <a name="collaborators"></a>Medarbetare
Ägaren och alla [medarbetare](luis-how-to-collaborate.md) har fullständig åtkomst till alla versioner av appen.

## <a name="next-steps"></a>Nästa steg

Se hur du lägger till [versions hantering](luis-how-to-manage-versions.md) på sidan appinställningar. 

Lär dig hur du [](luis-concept-intent.md) utformar avsikter i modellen.
