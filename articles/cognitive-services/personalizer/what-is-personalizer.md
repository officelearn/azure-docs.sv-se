---
title: Vad är Personalizer
titleSuffix: Azure Cognitive Services
description: Azure Personalizer är en molnbaserad API-tjänst som gör det möjligt att välja den bästa upplevelsen ska visas för användarna, utbildning från beteendet i realtid.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c969029bcc0412267507efe81549ec6f8b2988ce
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027119"
---
# <a name="what-is-personalizer"></a>Vad är Personalizer?

Azure Personalizer är en molnbaserad API-tjänst som gör det möjligt att välja den bästa upplevelsen ska visas för användarna, utbildning från beteendet i realtid.

* Ange information om dina användare och innehåll och få den översta åtgärden för att visa dina användare. 
* Behöver inte rensa och märka data innan du använder Personalizer.
* Ge återkoppling till Personalizer när det passar dig. 
* Visa analys i realtid. 
* Verifiera befintliga experiment med hjälp av Personalizer som en del av en större data science ansträngning.

## <a name="how-does-personalizer-work"></a>Hur fungerar Personalizer?

Personalizer använder machine learning-modeller för att identifiera vilken åtgärd som ska rangordning högst upp i en kontext. Klientprogrammet innehåller en lista över möjliga åtgärder, med information om dem. och information om kontext som kan innehålla information om användare, enhet, osv. Personalizer anger åtgärden som ska vidtas. När ditt klientprogram använder den valda åtgärden, ger feedback till Personalizer i form av en utmärkelse poäng. När feedbackloopen är klar, uppdateras Personalizer automatiskt sin egen modellen som används för framtida rangordningar.

## <a name="how-do-i-use-the-personalizer"></a>Hur kan jag använda Personalizer?

![Med hjälp av Personalizer för att välja vilka videon ska visas för en användare](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Välj en upplevelse i din app för att anpassa.
1. Skapa och konfigurera tjänsten personanpassning i Azure portal
1. Använd SDK för att anropa Personalizer med information (_funktioner_) om användarna och innehållet (_åtgärder_). Du behöver inte ange rensa, märkta data innan du använder Personalizer. 
1. I klientprogrammet, visar du den åtgärd som valts av Personalizer.
1. Använd SDK för att ge återkoppling till Personalizer som anger om användaren har valt Personalizers åtgärd. Det här är en _belöna poäng_, vanligtvis mellan 1 och 1.
1. Visa analys i Azure portal för att utvärdera hur systemet fungerar och hur dina data hjälper anpassning.

## <a name="where-can-i-use-personalizer"></a>Var kan jag använda Personalizer?

Klientprogrammet kan till exempel lägga till Personalizer till:

* Anpassa vilka artikeln är markerad på en webbplats med nyheter.    
* Optimera ad placering på en webbplats.
* Visa en anpassad ”rekommenderade objekt” på en perioder webbplats.
* Föreslå element för användargränssnitt, till exempel filter som används i ett specifikt foto.
* Välj en chattrobot svar på förtydliga användaravsikt eller föreslå en åtgärd.
* Prioritera förslag på vad en användare gör som nästa steg i en affärsprocess.

## <a name="personalization-for-developers"></a>Anpassningar för utvecklare

Personalizer tjänsten har två API: er:

* Skicka information (_funktioner_) om användarna och innehållet (_åtgärder_) att anpassa. Personalizer svarar med den översta åtgärden.
* Skicka feedback till Personalizer om hur bra rangordning fungerade som ett tal vanligtvis mellan 0 och 1 (föregående avsnitt SA -1 och 1). 

![Grundläggande sekvens av händelser för anpassning](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa en feedbackloop iC#](csharp-quickstart-commandline-feedback-loop.md)
