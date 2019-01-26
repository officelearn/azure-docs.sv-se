---
title: API-HTTP-svarskoder – QnA Maker
titleSuffix: Azure Cognitive Services
description: 'Förstå vilka HTTP-svarskoder returneras från API: er för QnA Maker. Detta hjälper dig att åtgärda eventuella fel.'
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2364c42da22c9a8871928192ea825828d7dafb60
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911112"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API HTTP-svarskoder
Den [management](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) och förutsägelse-API: er returnera HTTP-svarskoder. Svarsmeddelanden innehåller information som är specifika för en begäran, är HTTP-svarsstatuskod allmän. 

### <a name="management"></a>Hantering

|Kod|Förklaring|
|:--|--|
|2xx|Lyckades|
|400|begärans-parametrarna är felaktiga vilket innebär att de obligatoriska parametrarna är saknas, är för stor eller har en felaktig|
|400|brödtexten för begäran är felaktig JSON finns saknas, är för stor eller har en felaktig|
|401|Ogiltig nyckel|
|403|Tillåts inte - har du inte rätt behörighet|
|404|KB finns inte|