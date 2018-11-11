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
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208312"
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
