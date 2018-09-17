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
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637592"
---
**Fråga**: ska du använda en hierarkisk entitet eller ett mönster med en enkel enhet med roller? 

**Svar**: Det beror. Mönster och exempel yttranden är jämförbara i att de representerar en användares uttryck och är specifika för en avsikt.  

Om talade inte har en tydlig mönstret kan Använd hierarkisk entiteter. 

|hierarkisk entiteter|Enkel enhet med roller|
|--|--|
|måste ha exempel yttranden med underordnade entiteter med etiketten i avsikter|måste ha exempel yttranden **roller kan inte märkas i avsikter**|
|kan använda i mönster|**måste** använda i mönster|
|kan behöva **mer** exempel yttranden i avsikt|kanske behöver ** färre exempel yttranden i avsikt|