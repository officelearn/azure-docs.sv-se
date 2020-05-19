---
title: Uppgradera sammansatt entitet – LUIS
description: Uppgradera den sammansatta entiteten till enheten med en uppgraderings process i LUIS-portalen.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 9bbb03a2009bdcb6e2fa5d20aefadd4c7c99f025
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599429"
---
# <a name="upgrade-composite-entity-to-machine-learned-entity"></a>Uppgradera sammansatt entitet till enhets medveten entitet

Uppgradera den sammansatta entiteten till den enhet som har lärts till enheten för att bygga en entitet som tar emot mer kompletta förutsägelser med bättre dekomposition för fel sökning av entiteten.

## <a name="current-version-model-restrictions"></a>Aktuella versions modell begränsningar

Uppgraderings processen skapar enhets identifierade entiteter, baserat på befintliga sammansatta entiteter som finns i din app, i en ny version av din app. Detta omfattar underordnade och roller för sammansatta entiteter. Processen växlar också etiketterna i yttranden till exempel för att använda den nya entiteten.

## <a name="upgrade-process"></a>Uppgraderings process

Uppgraderings processen:
* Skapar ny enhets medveten entitet för varje sammansatt entitet.
* Underordnade entiteter:
    * Om den underordnade entiteten endast används i den sammansatta entiteten läggs den bara till i den enhet som har lärts.
    * Om den underordnade entiteten används i sammansatt form _och_ som en separat entitet (med etikett i exemplet yttranden), kommer den att läggas till i versionen som en entitet och som en underordnad enhet till den nya enheten som har lärts.
    * Om den underordnade entiteten använder en roll, kommer varje roll att konverteras till en underordnad till en underordnad till samma namn.
    * Om den underordnade entiteten är en icke-domänansluten entitet (reguljärt uttryck, lista entitet eller en fördefinierad entitet) skapas en ny underordnad enhet med samma namn, och den nya underentiteten har en funktion som använder den icke-enhets medveten entiteten med den nödvändiga funktionen tillagd.
* Namnen bevaras men måste vara unika på samma underentiteter/samma nivå. Referera till [unika namngivnings gränser](luis-boundaries.md#name-uniqueness).
* Etiketter i exempel yttranden växlas till den nya enheten med underentiteter.

Använd följande diagram för att förstå hur din modell ändras:

|Gammalt objekt|Nytt objekt|Anteckningar|
|--|--|--|
|Sammansatt entitet|Enhet som har lärts med struktur|Båda objekten är överordnade objekt.|
|Sammansatt entitets underordnade entitet är **enkel entitet**|underentitet|Båda objekten är underordnade objekt.|
|Sammansatt entitets underordnade entitet är en **inbyggd entitet** , till exempel Number|underentiteten med namnet på den fördefinierade entiteten, till exempel Number, och underentiteten har _funktionen_ för fördefinierad nummer entitet med begränsnings alternativet inställt på _True_.|underentiteten innehåller en funktion med begränsning på underenhets nivå.|
|Sammansatt entitets underordnade entitet är en **inbyggd entitet** , till exempel tal, och en fördefinierad entitet har en **roll**|underentitet med namnet på rollen och underentiteten har funktionen för fördefinierad nummer entitet med begränsnings alternativ inställt på True.|underentiteten innehåller en funktion med begränsning på underenhets nivå.|
|Roll|underentitet|Roll namnet blir namnet på underentiteten. Underentiteten är en direkt underordnad till den enhet som har registrerats av enheten.|

## <a name="begin-upgrade-process"></a>Påbörja uppgraderings processen

Innan du uppdaterar bör du se till att:

* Ändra versioner om du inte har rätt version för att uppgradera


1. Påbörja uppgraderings processen från meddelandet eller vänta till nästa schemalagda prompt.

    > [!div class="mx-imgBorder"]
    > ![Påbörja uppgradering från meddelanden](./media/update-composite-entity/notification-begin-update.png)

1. I popup-fönstret väljer du **Uppgradera nu**.

1. Granska **vad som händer när du uppgraderar** informationen och välj sedan **Fortsätt**.

1. Välj de sammansatta entiteter som ska uppgraderas i listan och välj sedan **Fortsätt**.

1. Du kan flytta eventuella intränade ändringar från den aktuella versionen till den uppgraderade versionen genom att markera kryss rutan.

1. Välj **Fortsätt** för att påbörja uppgraderings processen.

1. Förlopps indikatorn visar status för uppgraderings processen.

1. När processen är färdig finns du med i en ny utbildad version med de nya enheter som har lärts från enheten. Välj **testa dina nya entiteter** för att se den nya entiteten.

    Om uppgraderingen eller träningen misslyckades för den nya entiteten ger ett meddelande mer information.

1. På sidan entitets lista markeras de nya entiteterna med **ny** bredvid typnamn.

## <a name="next-steps"></a>Nästa steg

* [Författare och medarbetare](luis-how-to-collaborate.md)