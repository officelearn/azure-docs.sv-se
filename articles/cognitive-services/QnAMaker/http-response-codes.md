---
title: API-HTTP-svarskoder – QnA Maker
titleSuffix: Azure Cognitive Services
description: 'Förstå vilka HTTP-svarskoder returneras från API: er för QnA Maker. Detta hjälper dig att åtgärda eventuella fel.'
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d41f434b4e90c3f3850b8cb2e2d5c09fd23fc2cf
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085442"
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