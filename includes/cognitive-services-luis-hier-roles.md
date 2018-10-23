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
ms.openlocfilehash: 1d7723f356274bbd18b1ea08e34046da82a1057c
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646359"
---
**Fråga**: ska du använda en hierarkisk entitet eller ett mönster med en enkel enhet med roller? 

**Svar**: Det beror. Mönster och exempel yttranden är jämförbara i att de representerar en användares uttryck och är specifika för en avsikt.  

Om talade inte har en tydlig mönstret kan Använd hierarkisk entiteter. 

|hierarkisk entiteter|Enkel enhet med roller|
|--|--|
|måste ha exempel yttranden med underordnade entiteter med etiketten i avsikter|måste ha exempel yttranden **roller kan inte märkas i avsikter**|
|kan använda i mönster|**måste** använda i mönster|
|kan behöva **mer** exempel yttranden i avsikt|kan behöva **färre** exempel yttranden i avsikt|
