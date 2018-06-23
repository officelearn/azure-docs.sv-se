---
title: Grupper och subteams i innehåll kontrollant API | Microsoft Docs
description: Lär dig hur du använder grupper och subteams i innehåll kontrollant API för kognitiva Services.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: 161c7cd8bac07d5ffc138297d98a40317a8d88fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351540"
---
# <a name="team-and-subteams"></a>Teamet och Subteams #

Innan du använder innehåll kontrollant måste du skapa ett Team. När du registrerar dig och namnge ditt team blir teamet din standard-teamet. Du kan bara ha ett team i verktyget granska. Du kan emellertid skapa flera subteams. Subteams är användbara för att skapa eskalering team eller team som är dedikerad till att granska specifika kategorier av innehåll. Du kanske till exempel skicka vuxet innehåll till ett annat team för flera granskning.

Det här avsnittet beskriver hur du skapar subteams och snabbt tilldela granskningar direkt. Du kan dock använda [arbetsflöden](workflows.md) tilldela granskningar baserat på särskilda kriterier.

## <a name="go-to-the-teams-setting"></a>Gå till team inställningen ##

Om du vill komma igång med att skapa en undergrupper, Välj den **team** alternativ under inställningar.

![Inställningar för team](images/0-teams-1.png)

## <a name="create-subteams"></a>Skapa subteams ##

Standard-teamet innehåller alla potentiella granskare; subteams är delar av standard-teamet. Du kan inte tilldelas någon en undergrupper om de inte redan medlemmar i gruppen standard, så du måste lägga till en granskare till standard-teamet. Klicka på knappen inbjudan på sidan Team.

![Bjuda in användare](images/invite-users.png)

### <a name="1-create-a-subteam"></a>1. Skapa en undergrupper.
Rulla nedåt sidan grupp till avsnittet undergrupper. Klicka på knappen Lägg till undergrupper. 

![Lägg till undergrupper](images/1-teams-1.png)

### <a name="2-name-your-subteam"></a>2. Namnge din undergrupper.
Ange ditt undergrupper namn i dialogrutan och klicka på Spara.

![Undergrupper namn](images/1-Teams-2.PNG)

### <a name="3-assign-members-from-your-default-team"></a>3. Tilldela medlemmar från standard-teamet.
Klicka på knappen Lägg till medlem om du vill tilldela en eller flera subteams medlemmar från standard-teamet. Du kan bara lägga till befintliga användare till en undergrupper. För att lägga till nya användare som inte är i verktyget granskning, att bjuda in dem med hjälp av knappen ”inbjudan” på inställningssidan för teamet.

![Tilldela undergrupper medlemmar](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Tilldela din subteams granskningar ##

När du har skapat din subteams skapas och gruppmedlemmar som tilldelats, kan du börja tilldela bilden och texten granskar till dessa subteams. Detta görs i fönstret granska.
Om du vill tilldela en enskild avbildning till en undergrupper, klicka på knappen i det övre högra hörnet bildens, väljer Flytta till och välj en undergrupper.

![Tilldela avbildningen granska till subteam](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Växla mellan subteams att granska tilldelade innehåll ##

Om du är medlem i en eller flera subteams kan växla du mellan dessa subteams från granska verktyg instrumentpanelen. Välj fliken Välj undergrupper om du vill visa alla aktuella väntande granskningar som hör till en undergrupper.

![Växla mellan subteams](images/3-review-image-subteam-2.png)
