---
title: Hantera team och undergrupper i Content Moderator-API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder team och undergrupper i Content Moderator-API för Cognitive Services.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e45e06bdcc69dc88d8c979bfbb5a0974f7284b22
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266237"
---
# <a name="manage-review-teams-and-subteams"></a>Hantera granskning team och undergrupper

Du måste skapa ett Team innan du använder Content Moderator. När du registrerar dig och namnge ditt team blir teamet standard-teamet. Du kan bara ha ett team i granskningsverktyget. Du kan dock skapa flera undergrupper. Undergrupper är användbara för att skapa eskalering team eller team som är dedikerade för att granska specifika kategorier av innehåll. Exempelvis kan du skicka vuxet innehåll till ett annat team för mer granskning.

Det här avsnittet förklarar hur du skapar undergrupper och snabbt tilldela granskningar i farten. Du kan dock använda [arbetsflöden](workflows.md) tilldela granskningar baserat på specifika villkor.

## <a name="go-to-the-teams-setting"></a>Gå till teamen för inställningen

Om du vill komma igång med att skapa en undergrupper, Välj den **team** under inställningarna.

![Inställningar för team](images/0-teams-1.png)

## <a name="create-subteams"></a>Skapa undergrupper

Standard-teamet innehåller alla potentiella granskare; undergrupper är delmängder till standard-teamet. Du kan inte tilldelas någon en undergrupper om de inte redan medlemmar i standard-teamet så att du måste lägga till alla granskare till standard-teamet. Klicka på inbjudan på lagsidan.

![Bjuda in användare](images/invite-users.png)

### <a name="create-a-subteam"></a>Skapa en undergrupper
Rulla nedåt sidan Team och undergrupper-avsnittet. Klicka på knappen Lägg till undergrupper. 

![Lägg till undergrupper](images/1-teams-1.png)

### <a name="name-your-subteam"></a>Namnge din undergrupper
Ange namnet på din undergrupper i dialogrutan och sedan klicka på Spara.

![Undergrupper namn](images/1-Teams-2.PNG)

### <a name="assign-members-from-your-default-team"></a>Tilldela medlemmar från standard-teamet.
Klicka på knappen Lägg till medlem för att tilldela medlemmar från din standard-grupp till en eller flera undergrupper. Du kan bara lägga till befintliga användare till en undergrupper. För att lägga till nya användare som inte ingår i Verktyg för granskning, att bjuda in dem med hjälp av knappen ”Bjud in” på sidan Inställningar för Team.

![Tilldela undergrupper medlemmar](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Tilldela din undergrupper granskningar

När du har din undergrupper som skapats och gruppmedlemmar som tilldelats, du kan börja tilldela bild och text kodgranskningar till dessa undergrupper. Detta görs i fönstret granska.
Om du vill tilldela en enskild avbildning till en undergrupper klickar du på ellipsen i det övre högra hörnet av avbildningen, Välj flytten till och välj en undergrupper.

![Tilldela bild granskning till subteam](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Växla mellan undergrupper att granska tilldelat innehåll

Om du är medlem i en eller flera undergrupper kan växla du mellan dessa undergrupper från granska verktyget instrumentpanelen. Välj Välj undergrupper öppnar du fliken om du vill se alla för aktuellt väntande granskningar som hör till en undergrupper.

![Växla mellan undergrupper](images/3-review-image-subteam-2.png)
