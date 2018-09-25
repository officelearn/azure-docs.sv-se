---
title: Förstå versionshantering i LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder versioner för att hantera ändringar i Språkförståelse (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5d78172ae441300cdc39df8b911fd7ecaa42df9f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032703"
---
# <a name="versions"></a>Versioner
Skapa olika modeller av samma app med [versioner](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Versions-ID
Versions-ID som består av tecken, siffror eller '.' och får inte vara längre än 10 tecken.

## <a name="initial-version"></a>Den första versionen
Den första versionen (0,1) är den aktiva standard-versionen. 

## <a name="active-version"></a>Aktiva versionen
Att [ange en version](luis-how-to-manage-versions.md#set-active-version) som aktivt innebär att det är för närvarande redigeras och testas i den [LUIS](luis-reference-regions.md) webbplats. Ange en version som aktiv för att få åtkomst till dess data bör du kontrollera uppdateringar, samt att testa och publicera den.

Namnet på den aktiva versionen visas i den övre, vänstra panelen efter appnamnet. 

[ ![Ändra active version](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

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