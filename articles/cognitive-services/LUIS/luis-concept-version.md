---
title: Versionshantering
titleSuffix: Language Understanding - Azure Cognitive Services
description: Versioner i LUIS, liknar versioner i traditionella programmering. Varje version är en ögonblicksbild i gång av appen. Skapa en ny version innan du gör ändringar i appen. Det är lättare att gå tillbaka till exakt appen, sedan att försöka unpeel och appens avsikt och yttranden till ett tidigare tillstånd.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: diberry
ms.openlocfilehash: 9da79e5b744f8ba70c0e265f0d1f0126b37eba49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509689"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Förstå hur och när du använder en LUIS-version

Versioner i LUIS, liknar versioner i traditionella programmering. Varje version är en ögonblicksbild i gång av appen. Skapa en ny version innan du gör ändringar i appen. Det är enklare att gå tillbaka till den exakta versionen och försök att ta bort avsikter och yttranden till ett tidigare tillstånd.

Skapa olika modeller av samma app med [versioner](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Version ID
Versions-ID som består av tecken, siffror eller '.' och får inte vara längre än 10 tecken.

## <a name="initial-version"></a>Den första versionen
Den första versionen (0,1) är den aktiva standard-versionen. 

## <a name="active-version"></a>Aktiva versionen
Att [ange en version](luis-how-to-manage-versions.md#set-active-version) som aktivt innebär att det är för närvarande redigeras och testas i den [LUIS](luis-reference-regions.md) webbplats. Ange en version som aktiv för att få åtkomst till dess data bör du kontrollera uppdateringar, samt att testa och publicera den.

Namnet på den aktiva versionen visas i den övre, vänstra panelen efter appnamnet. 

[![Ändra active version](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versioner och publicera platser
Du publicerar till antingen steg och produkten platser. Varje plats kan ha en annan version eller samma version. Detta är användbart för att verifiera ändringar mellan modellversioner via den slutpunkt som är tillgänglig för robotar eller andra LUIS anropa program. 

## <a name="clone-a-version"></a>Klona en version
Klona en version för att skapa en kopia av en befintlig version och spara den som en ny version. Klona en version för att använda samma innehållet i den befintliga versionen som utgångspunkt för den nya versionen. När du klonar en version, den nya versionen blir den **active** version. 

## <a name="import-and-export-a-version"></a>Importera och exportera en version
Du kan importera en version på appnivå. Den här versionen blir den aktiva versionen och används versions-ID i egenskapen ”versionId” för app-filen. Du kan även importera på versionsnivån till en befintlig app. Den nya versionen blir den aktiva versionen. 

Du kan exportera en version på appnivå eller du kan exportera en version på versionsnivån. Den enda skillnaden är att den exporterade appnivå-versionen är den aktiva versionen på versionsnivå kan du välja en version så här exporterar du på den **[inställningar](luis-how-to-manage-versions.md)** sidan. 

Den exporterade filen innehåller inte datorn lärt dig information eftersom appen är modellkomponenten när den har importerats. Den exporterade filen innehåller inte medarbetare – du måste lägga till dessa tillbaka när versionen har importerats till den nya appen.

## <a name="export-each-version-as-app-backup"></a>Exportera varje version som säkerhetskopia av app
För att kunna säkerhetskopiera LUIS-appen kan du exportera varje version på den **[inställningar](luis-how-to-manage-versions.md)** sidan.

## <a name="delete-a-version"></a>Ta bort en version
Du kan ta bort alla versioner utom den aktiva versionen från listan över versioner på sidan Inställningar. 

## <a name="version-availability-at-the-endpoint"></a>Version tillgänglighet på slutpunkten
Tränade versioner är inte automatiskt tillgängliga i din app [endpoint](luis-glossary.md#endpoint). Du måste [publicera](luis-how-to-publish-app.md) eller publicera en version för att den ska vara tillgänglig i app-slutpunkten. Du kan publicera till **mellanlagring** och **produktion**, vilket ger dig upp till två versioner av appen som är tillgängliga på slutpunkten. Om du behöver flera versioner av appen på en slutpunkt kan du exportera versionen och importera till en ny app. Den nya appen har en annan app-ID.

## <a name="collaborators"></a>Medarbetare
Ägare och alla [medarbetare](luis-how-to-collaborate.md) har fullständig åtkomst till alla versioner av appen.

## <a name="next-steps"></a>Nästa steg

Se hur du lägger till [versionshantering](luis-how-to-manage-versions.md) på inställningssidan. 

Lär dig hur du utformar [avsikter](luis-concept-intent.md) i modellen.
