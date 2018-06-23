---
title: Granska texten i Azure Content kontrollant | Microsoft Docs
description: Lär dig hur du granskar text innehåll kontrollant att visa dess poäng och har hittat taggar. Använd informationen om du vill veta om innehållet är lämpligt.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: cb8774395b7374677e8de3b80630a2d4abf490f9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351516"
---
# <a name="review-text"></a>Granska text

Du kan använda Azure innehåll kontrollant för att granska text med hjälp av resultat och identifierat taggar. Använd informationen för att avgöra om innehåll är lämpligt. 

## <a name="select-or-enter-the-text-to-review"></a>Välj eller ange text för att granska

Innehåll kontrollant, Välj den **försök** fliken. Markera den **Text** alternativet för att gå till text avbrottsmoderering startskärmen. Ange text eller skicka exempeltexten standard för automatisk text måtta. Du kan ange högst 1 024 tecken.

## <a name="get-ready-to-review-results"></a>Dags att granska resultaten

Granska verktyget anropar först Text Avbrottsmoderering API. Den genererar sedan text granskningar med hjälp av de identifierade taggarna. Granska verktyget matchar poängresultat för din grupps uppmärksamhet.

## <a name="review-text-results"></a>Granska resultatet från text

Detaljerade resultat visas i windows. Resultatet är identifierade taggar och termer som returnerades av Text Avbrottsmoderering API. Välj taggen för att växla status för en tagg markering. Du kan också fungera med alla anpassade taggar som du har skapat.

![Granska resultatet från text](images/3-review-text-2.png)
