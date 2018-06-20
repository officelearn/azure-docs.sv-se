---
title: Förstå THOMAS app samarbete - Azure | Microsoft Docs
description: THOMAS appar kräver en enda ägare och valfria medarbetare.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 51b3958f83cd110ace27f6ee42571c05843f5aa2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265073"
---
# <a name="collaborating"></a>Samarbeta

THOMAS ger samarbete för att tillåta en grupp människor att skapa en app.

## <a name="luis-account"></a>THOMAS konto
En THOMAS konto är kopplat till en enda [Microsoft Live](https://login.live.com/) konto. Varje THOMAS konto ges ett kostnadsfritt [redigering nyckeln](luis-concept-keys.md#authoring-key) ska användas för redigering av alla appar som THOMAS kontot har åtkomst till. 

Ett THOMAS konto kan ha många THOMAS appar.

## <a name="luis-app-owner"></a>THOMAS app ägare
Det konto som skapar en app är ägare. Varje app har en enda ägare. Ägaren finns på app  **[inställningar](luis-how-to-collaborate.md)**. Detta är det konto som kan ta bort appen. Detta är det konto som får e-postmeddelande när slutpunkten kvoten når 75% av den månatliga begränsningen. 

## <a name="transfer-ownership"></a>Överför ägarskap
THOMAS ger inte äganderätten, men alla medarbetare kan exportera appen och sedan skapa en app genom att importera den. Tänk på den nya appen har ett annat App-ID. Ny app som behöver tränats, publiceras och ny slutpunkt som används.

## <a name="luis-app-collaborators"></a>THOMAS app medarbetare
En app ägare kan lägga till medarbetare till en app. Ägaren måste lägga till den deltagare e-postadress på app  **[inställningar](luis-how-to-collaborate.md)**. Deltagare har fullständig åtkomst till appen. Om medarbetare tar bort appen, appen tas bort från den deltagare konto men finns kvar i ägarens konto. 

Om du vill dela flera appar med medarbetare måste varje app den deltagare e-post lagts till. 

## <a name="managing-multiple-authors"></a>Hantera flera författare
Den [THOMAS] [ LUIS] webbplats för närvarande inte tillhandahåller transaktionsnivå redigering. Du kan tillåta författare ska fungera på oberoende versioner från en grundläggande version. I följande avsnitt beskrivs två olika metoder.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Hantera flera versioner i samma app
Börja med att [kloning](luis-how-to-manage-versions.md#clone-a-version), från en grundläggande version för varje författare. 

Varje författare ändrar sin egen version av appen. När varje författare är nöjd med modellen kan du exportera de nya versionerna till JSON-filer.  

Exporterade appar är JSON-formaterade filer som kan användas vid ändringar. Kombinera filer om du vill skapa en JSON-fil för den nya versionen. Ändra den **versionId** egenskap i JSON för att ange den nya sammanfogade versionen att. Importera den här versionen till den ursprungliga app. 

Den här metoden kan du ha en aktiv version, en steg-version och en publicerad version. Du kan jämföra resultatet i fönstret interaktiva tester mellan tre versioner.

### <a name="manage-multiple-versions-as-apps"></a>Hantera flera versioner som appar
[Exportera](luis-how-to-manage-versions.md#export-version) basversionen. Varje författare importerar versionen. Den person som importerar appen är ägare till versionen. När de är klar ändra appen, exportera versionen. 

Exporterade appar är JSON-formaterade filer som kan jämföras med grundläggande exporten för att ändringarna. Kombinera filer om du vill skapa en JSON-fil för den nya versionen. Ändra den **versionId** egenskap i JSON för att ange den nya sammanfogade versionen att. Importera den här versionen till den ursprungliga app.

## <a name="next-steps"></a>Nästa steg

Förstå [versionshantering](luis-concept-version.md) begrepp. 

Se [Appinställningar](luis-how-to-collaborate.md) att lära dig hur du hanterar medarbetare i din app THOMAS.

Se [lägga till e-post till åtkomstlista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) med API: er redigering.

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website