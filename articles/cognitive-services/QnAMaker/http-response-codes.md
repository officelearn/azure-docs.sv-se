---
title: API-HTTP-svarskoder – QnA Maker
titleSuffix: Azure Cognitive Services
description: 'Förstå vilka HTTP-svarskoder returneras från API: er för QnA Maker. Detta hjälper dig att åtgärda eventuella fel.'
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9eb5f1aefc8a36727e837af79f553108f624fc70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207980"
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
