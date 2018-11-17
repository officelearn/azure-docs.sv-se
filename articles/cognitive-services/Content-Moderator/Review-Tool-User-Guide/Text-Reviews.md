---
title: Granska text i Azure Content Moderator | Microsoft Docs
description: Lär dig att granska text i Content Moderator att se dess poäng och identifierade taggar. Använd informationen om du vill veta om innehållet är lämpligt.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: 60a8d03a6562c9a66c2f6781ebbc330c989b343e
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852103"
---
# <a name="review-text"></a>Granska text

Du kan använda Azure Content Moderator för att granska text med hjälp av poäng och upptäcks taggar. Använd informationen för att fastställa om innehåll är lämpligt. 

## <a name="select-or-enter-the-text-to-review"></a>Välj eller ange vilken text som ska granska

I Content Moderator, väljer du den **försök** fliken. Välj den **Text** alternativet om du vill gå på startskärmen för moderering av text. Ange valfri text eller skicka standard exempeltext för automatiserade textmoderering. Du kan ange högst 1 024 tecken.

## <a name="get-ready-to-review-results"></a>Gör dig redo att granska resultatet

Granskningsverktyget anropar först Moderering av Text-API. Sedan genererar den textomdömen med hjälp av de identifierade taggarna. Granskningsverktyget matchar poängresultat för ditt team uppmärksamhet.

## <a name="review-text-results"></a>Granska resultatet från text

Detaljerade resultat visas i windows. Bland resultaten återfinns identifierade taggar och villkor som returnerades av Moderering av Text-API. Välj taggen för att växla status för en tagg markering. Du kan också arbeta med alla anpassade taggar som du har skapat.

![Skärmbild av granska verktyget visar flaggade text i ett webbläsarfönster för Chrome](../images/reviewresults_text.png)
