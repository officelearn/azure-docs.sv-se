---
title: Talare Recognition API | Microsoft Docs
description: Använd avancerade algoritmer för talare verifiering och talare identifiering med talare Recognition API i kognitiva Services.
services: cognitive-services
author: dwlin
manager: zhang
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: article
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 6d5e4e4bbe0cb5e57d2556f680ffcf8d16ee1818
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352167"
---
# <a name="speaker-recognition-api"></a>Talarigenkännings-API

Välkommen till Microsoft talare Recognition-API: er. API: er för talarverifiering Recognition är molnbaserad API: er som ger de mest avancerade algoritmerna för talare verifiering och talare identifiering. Talare Recognition kan delas in i två kategorier: talare verifiering och talare identifiering.


## <a name="speaker-verification"></a>Talarverifiering


Röst har unika egenskaper som kan användas för att identifiera en person, precis som ett fingeravtryck.  Med röst som en signal för scenarier med åtkomst kontroll och verifiering är här som en ny innovativa verktyg – i princip erbjuder en nivå i säkerhet som förenklar autentisering för kunder.

API: er för talaridentifiering kan automatiskt kontrollera och autentisera användare som använder sin röst eller tal.

### <a name="enrollment"></a>Registrering

Registreringen för talare verifiering är text-beroende vilket innebär högtalare måste du välja en specifik lösenordet ska användas under både registrering och verifiering. 

I registreringen på högtalaren röst registreras om en specifik fras, och sedan ett antal funktioner har extraherats och valda frasen känns. Både extraherade funktioner och valt fras tillsammans formuläret en unik röst-signatur.

### <a name="verification"></a>Verifiering
###
Verifieringen, en inkommande röst och frasen jämförs registreringen röst signatur och frasen – för att kontrollera huruvida de är från samma person, och om de säger rätt frasen.

Mer information om verifiering av talare finns i API: et [talare - verifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Talaridentifiering

API: er för talaridentifiering kan automatiskt identifiera tal i en ljudfil, anges en grupp av potentiella högtalare. Inkommande ljuduppspelningen paras ihop mot den angivna gruppen högtalare och om det finns en matchning hittas på högtalaren identitet returneras.

Alla högtalare ska gå igenom en registreringsprocess först få rösten registreras i systemet och har en röst utskrift skapas.


### <a name="enrollment"></a>Registrering

Registreringen för identifiering av talare är text oberoende, vilket innebär att det inte finns några begränsningar på högtalaren står i ljuduppspelningen. På högtalaren röst registreras och ett antal funktioner extraheras för att skapa en unik röst-signatur. 


### <a name="recognition"></a>Bekräftelse

Ljud av okänd högtalaren tillsammans med talar, potentiell grupp anges under igenkänning. Inkommande röst jämförs i alla högtalare för att kunna fastställa vars röst är det, och om det finns en matchning hittas på högtalaren identitet returneras.


Mer information om identifiering av talare finns i API: et [talare - identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
