---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480371"
---
**Fråga**: ska du använda en hierarkisk entitet eller ett mönster med en enkel enhet med roller? 

**Svar**: Det beror. Mönster och exempel yttranden är jämförbara i att de representerar en användares uttryck och är specifika för en avsikt.  

Om talade inte har en tydlig mönstret kan Använd hierarkisk entiteter. 

|hierarkisk entiteter|Enkel enhet med roller|
|--|--|
|måste ha exempel yttranden med underordnade entiteter med etiketten i avsikter|måste ha exempel yttranden **roller kan inte märkas i avsikter**|
|kan använda i mönster|**måste** använda i mönster|
|kan behöva **mer** exempel yttranden i avsikt|kanske behöver ** färre exempel yttranden i avsikt|