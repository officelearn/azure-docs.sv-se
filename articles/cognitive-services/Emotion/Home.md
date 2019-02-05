---
title: Vad är Känslo-API?
titlesuffix: Azure Cognitive Services
description: Den molnbaserade igenkänningsalgoritmen används för att bygga mer personligt anpassade appar.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 555b03be679b0d1ea61371d22ec9865e0e72b558
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215800"
---
# <a name="what-is-the-emotion-api"></a>Vad är Känslo-API?

> [!IMPORTANT]
> Känslo-API:et kommer att bli inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [Ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/). 

Välkommen till Microsoft Känslo-API, där du kan bygga personligt anpassade appar med Microsofts molnbaserade algoritm för känsloigenkänning.

### <a name="emotion-recognition"></a>Känsloigenkänning

Betaversionen av Känslo-API:et utgår från indata i form av ett ansiktsuttryck i en bild och returnerar en rad olika känslouppsättningar för varje ansikte i bilden, liksom en avgränsningsruta för ansiktet från Ansikts-API:et. De identifierade känslorna är glädje, sorg, förvåning, ilska, rädsla, förakt, avsky eller neutral. Känslorna uttrycks i många olika kulturer och globalt via samma grundläggande ansiktsuttryck, som identifieras av Känslo-API:et.

**Tolka resultaten:**

När du tolkar resultaten från Känslo-API:et ska den identifierade känslan tolkas som känslan med den högsta poängen, eftersom poängsiffrorna normaliseras och summeras till en. Användare kan välja att ange en högre relevanströskel i sina program, beroende på deras behov.

Mer information om känsloigenkänning finns i API-referensen:
  * Grundläggande: En användare som redan har anropat Ansikts-API:et kan skicka ansiktsrektangeln som indata och använda basnivån. [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Om en användare inte skickar in någon ansiktsrektangel bör standardläget användas.  [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Ett exempel på hur du tolkar direktuppspelat videoinnehåll med Känslo-API finns i [Så analyserar du videor i realtid](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
