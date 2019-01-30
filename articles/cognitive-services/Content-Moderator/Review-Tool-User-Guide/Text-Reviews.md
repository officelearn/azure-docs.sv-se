---
title: Granska text i Azure Content Moderator - Content Moderator
description: Lär dig att granska text i Content Moderator att se dess poäng och identifierade taggar. Använd informationen om du vill veta om innehållet är lämpligt.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 896956653ed924b7e30e63c1ae5cfa90dd0cc94d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219795"
---
# <a name="let-human-reviewers-review-text"></a>Låt mänsklig granskare granska text

Du kan använda Azure Content Moderator för att granska text med hjälp av poäng och upptäcks taggar. Använd informationen för att fastställa om innehåll är lämpligt. 

## <a name="select-or-enter-the-text-to-review"></a>Välj eller ange vilken text som ska granska

I Content Moderator, väljer du den **försök** fliken. Välj den **Text** alternativet om du vill gå på startskärmen för moderering av text. Ange valfri text eller skicka standard exempeltext för automatiserade textmoderering. Du kan ange högst 1 024 tecken.

## <a name="get-ready-to-review-results"></a>Gör dig redo att granska resultatet

Granskningsverktyget anropar först Moderering av Text-API. Sedan genererar den textomdömen med hjälp av de identifierade taggarna. Granskningsverktyget matchar poängresultat för ditt team uppmärksamhet.

## <a name="review-text-results"></a>Granska resultatet från text

Detaljerade resultat visas i windows. Bland resultaten återfinns identifierade taggar och villkor som returnerades av Moderering av Text-API. Välj taggen för att växla status för en tagg markering. Du kan också arbeta med alla anpassade taggar som du har skapat.

![Skärmbild av granska verktyget visar flaggade text i ett webbläsarfönster för Chrome](../images/reviewresults_text.png)
