---
title: Designa Azure-funktioner för identiska indata
description: Bygga Azure-funktioner som ska vara idempotenta
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226862"
---
# <a name="designing-azure-functions-for-identical-input"></a>Designa Azure-funktioner för identiska indata

Verkligheten för händelsedriven och meddelandebaserad arkitektur dikterar behovet av att acceptera identiska begäranden samtidigt som dataintegritet och systemstabilitet bevaras.

För att illustrera, överväga en hiss samtalsknapp. När du trycker på knappen tänds den och en hiss skickas till ditt golv. En stund senare kommer någon annan till dig i lobbyn. Den här personen ler mot dig och trycker på den upplysta knappen en andra gång. Du ler tillbaka och skrattar till dig själv som du påminns om att kommandot för att ringa en hiss är idempotent.

Om du trycker på en hissanropsknapp en andra, tredje eller fjärde gång har den ingen betydelse för slutresultatet. När du trycker på knappen, oavsett hur många gånger, skickas hissen till ditt golv. Idempotenta system, som hissen, resulterar i samma resultat oavsett hur många gånger identiska kommandon utfärdas.

När det gäller att skapa program bör du tänka på följande scenarier:

- Vad händer om ditt lagerkontrollprogram försöker ta bort samma produkt mer än en gång?
- Hur fungerar ditt personalprogram om det finns fler än en begäran om att skapa en medarbetarpost för samma person?
- Vart tar pengarna vägen om din bankapp får 100 förfrågningar om att göra samma uttag?

Det finns många sammanhang där begäranden till en funktion kan ta emot identiska kommandon. Vissa situationer är:

- Försök igen principer som skickar samma begäran många gånger
- Cachelagrade kommandon som spelas upp i programmet
- Programfel som skickar flera identiska begäranden

För att skydda dataintegritet och systemhälsa innehåller ett idempotent program logik som kan innehålla följande beteenden:

- Verifiera förekomsten av data innan du försöker köra en borttagning
- Kontrollera om det redan finns data innan du försöker köra en skapa-åtgärd
- Stämma av logik som skapar eventuell konsekvens i data
- Samtidighetskontroller
- Identifiering av duplicering
- Validering av datas fräschör
- Skydda logik för att verifiera indata

I slutändan uppnås idempotency genom att säkerställa en viss åtgärd är möjlig och endast utförs en gång.
