---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: dee31fca841ea309128681637cc41fa0fb1e7aea
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480619"
---
## <a name="roles-versus-hierarchical-entities"></a>Roller jämfört med hierarkisk entiteter

Ska du använda en hierarkisk entitet eller ett mönster med en enkel enhet med roller? 

Det beror. Mönster och exempel yttranden är jämförbara i att de representerar en användares uttryck och är specifika för en avsikt.  

Om talade inte har en tydlig mönstret kan Använd hierarkisk entiteter. 

|hierarkisk entiteter|Enkel enhet med roller|
|--|--|
|Tillgängliga i avsikts exempel yttranden och mönster|Endast tillgängligt i mönster|
|Måste ha exempel yttranden i avsikten med underordnade entiteter är märkt|Roller kan inte märkas i exempel yttranden i avsikt|
|Kan behöva **mer** exempel yttranden i avsikt att extrahera entitet|Behöver **färre** exempel yttranden i avsikt|
