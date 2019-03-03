---
title: ta med fil
description: ta med fil
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 609eaa25640e74ffe3b39606051edd7048f72497
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251857"
---
| Resurs | Gräns |
| --- | --- |
| Maximalt antal skalningsenheter | 10 per region<sup>1</sup> |
| Cachestorlek | 5 GB per enhet<sup>2</sup> |
| Samtidiga serverdel anslutningar<sup>3</sup> per HTTP-utfärdare | 2048 per enhet<sup>4</sup> |
| Maximal cachelagrade svarsstorlek | 2MB |
| Princip för maximal storlek | 256KB<sup>5</sup> | 
| Maximal anpassade gateway domäner per tjänstinstans<sup>6</sup> | 20 |
| Maximalt antal certifikatutfärdarcertifikat per instans | 10 | 
| Maximalt antal instanser av tjänsten per prenumeration<sup>7</sup> | 20 | 
| Högsta antalet prenumerationer per tjänstinstans<sup>7</sup> | 500 |
| Maximalt antal klientcertifikat per tjänstinstans<sup>7</sup> | 50 | 
| Maximalt antal API: er per tjänstinstans<sup>7</sup> | 50 | 
| Maximalt antal API-åtgärder per tjänstinstans<sup>7</sup> | 1000 | 
| Maximal total varaktighet för begäran<sup>7</sup> | 30 sekunder | 
| Maximalt buffras nyttolast<sup>7</sup> | 2MB | 


<sup>1</sup> skalning gränserna beror på prisnivå. Om du vill se prissättningen nivåerna och deras skalningsgränser går till [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> per enhet cache storleken beror på prisnivå. Om du vill se prissättningen nivåerna och deras skalningsgränser går till [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> anslutningar i pooler och används igen, om inte explicit avslutades av serverdelen.<br/>
<sup>4</sup> per enhet för nivåerna Basic, Standard och Premium. Developer-nivån är begränsad till 1024. Gäller inte för förbrukning-nivå.<br/> 
<sup>5</sup> nivåerna i Basic, Standard och Premium. Nivåstorlek princip för dokumentet är begränsad till 4KB för förbrukningen.<br/>
<sup>6</sup> tillgänglig på Premium-nivån.<br/>
<sup>7</sup> gäller till förbrukning-nivån.<br/>



