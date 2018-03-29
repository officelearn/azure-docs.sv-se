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
ms.openlocfilehash: bee289da3f18edd0cb425f3d9acde084567a3b13
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
| Resurs | Gräns |
| --- | --- |
| Enheter av skala | 10 per region<sup>1</sup> |
| Cache | 5 GB per enhet<sup>1</sup> |
| Samtidiga backend anslutningar<sup>2</sup> per http-utfärdare | 2048 per enhet<sup>3</sup> |
| Maximal svarsstorlek för cachelagrade | 10MB |
| Maximal anpassade gateway-domäner | 20 per tjänstinstans<sup>4</sup> |


<sup>1</sup>API Management-gränserna är olika för varje prisnivå. Se prissättning nivåer och deras skala gränser går du till [priser för API Management](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> anslutningar i pooler och används igen, om inte uttryckligen har stängts av serverdelen.
<sup>3</sup> per enhet för nivåerna Basic, Standard och Premium. Utvecklare nivån är begränsad till 1024.
<sup>4</sup> tillgänglig i Premium-nivån.


