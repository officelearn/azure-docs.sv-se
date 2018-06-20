---
title: Förstå versionshantering i THOMAS - Azure | Microsoft Docs
description: Lär dig hur du använder versioner för att hantera ändringar i språk förstå (THOMAS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/13/2018
ms.author: v-geberr
ms.openlocfilehash: dabe7def2766770b686be3c43d4af4f331dd9577
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266081"
---
# <a name="versions"></a>Versioner
Skapa olika modeller av samma app med [versioner](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Versions-ID
Versions-ID som består av tecken, siffror eller '.' och får inte vara längre än 10 tecken.

## <a name="initial-version"></a>Ursprunglig version
Den första versionen (0,1) är den aktiva standardversionen. 

## <a name="active-version"></a>Aktiva versionen
Att [ange en version](luis-how-to-manage-versions.md#set-active-version) eftersom aktiva innebär det för tillfället redigeras och testas i den [THOMAS] [ LUIS] webbplats. Ange en version som aktiv för att komma åt data, göra uppdateringar, samt för att testa och publicera den.

Namnet på den aktiva versionen visas i den övre, vänstra panelen efter namnet på appen. 

[ ![Ändra active version](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versioner och publicera platser
Du publicerar de steg och produkt platserna. För varje plats kan ha en annan version eller samma version. Detta är användbart för att verifiera ändringar mellan modellversioner via den slutpunkt som är tillgänglig för robotar eller andra THOMAS anropar program. 

## <a name="clone-a-version"></a>Klona en version
Klona en version för att skapa en kopia av en befintlig version och spara den som en ny version. Klona en version för att använda samma innehållet i den befintliga versionen som en startpunkt för den nya versionen. När du klonar en version blir den nya versionen av **active** version. 

## <a name="import-and-export-a-version"></a>Importera och exportera en version
Du kan importera en version på app-nivå. Den här versionen blir den aktiva versionen och används versions-ID i egenskapen ”versionId” i app-filen. Du kan också importera på versionsnivån till en befintlig app. Den nya versionen blir den aktiva versionen. 

Du kan exportera en version på app-nivå eller så kan du exportera en version på versionsnivån. Den enda skillnaden är att den exporterade app-nivå-versionen är den aktiva versionen medan på versionsnivån kan du välja en version för att exportera på den **[inställningar](luis-how-to-manage-versions.md)** sidan. 

Den exporterade filen innehåller inte datorn lärt dig information eftersom appen retrained när den har importerats. Den exporterade filen innehåller inte medarbetare – du måste lägga till dessa tillbaka när versionen har importerats till den nya appen.

## <a name="export-each-version-as-app-backup"></a>Exportera varje version som appen säkerhetskopiering
För att kunna säkerhetskopiera din app THOMAS exportera varje version på den **[inställningar](luis-how-to-manage-versions.md)** sidan.

## <a name="delete-a-version"></a>Ta bort en version
Du kan ta bort alla versioner utom den aktiva versionen från listan över versioner på inställningssidan. 

## <a name="version-availability-at-the-endpoint"></a>Tillgänglig på slutpunkten
Tränade versioner är inte tillgängliga på din app automatiskt [endpoint](luis-glossary.md#endpoint). Du måste [publicera](PublishApp.md) eller publicera en version för att den ska vara tillgänglig i app-slutpunkten. Du kan publicera till **mellanlagring** och **produktion**, ger upp till två versioner av appen som är tillgängliga på slutpunkten. Om flera versioner av appen på en slutpunkt måste du exportera versionen och importera till en ny app. Den nya appen har en annan app-ID.

## <a name="collaborators"></a>Medarbetare
Ägare och alla [medarbetare](luis-how-to-collaborate.md) har fullständig åtkomst till alla versioner av appen.

## <a name="next-steps"></a>Nästa steg

Se hur du lägger till [versionshantering](luis-how-to-manage-versions.md) på inställningssidan app. 

Lär dig hur du utformar [intents](luis-concept-intent.md) i modellen.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions