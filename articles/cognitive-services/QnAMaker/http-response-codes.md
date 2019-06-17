---
title: API-HTTP-svarskoder – QnA Maker
titleSuffix: Azure Cognitive Services
description: 'Förstå vilka HTTP-svarskoder returneras från API: er för QnA Maker. Detta hjälper dig att åtgärda eventuella fel.'
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: cdfa5212f321cc6ec976ea9df301243acc54a23f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794850"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API HTTP-svarskoder
Den [management](https://go.microsoft.com/fwlink/?linkid=2092179) och förutsägelse-API: er returnera HTTP-svarskoder. Svarsmeddelanden innehåller information som är specifika för en begäran, är HTTP-svarsstatuskod allmän. 

### <a name="management"></a>Hantering

|Kod|Förklaring|
|:--|--|
|2xx|Lyckades|
|400|begärans-parametrarna är felaktiga vilket innebär att de obligatoriska parametrarna är saknas, är för stor eller har en felaktig|
|400|brödtexten för begäran är felaktig JSON finns saknas, är för stor eller har en felaktig|
|401|Ogiltig nyckel|
|403|Tillåts inte - har du inte rätt behörighet|
|404|KB finns inte|
|410|Detta API är inaktuell och är inte längre tillgänglig|
