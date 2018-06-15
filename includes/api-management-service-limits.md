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
ms.openlocfilehash: b40e2ba448236bcef72ffe501b4b14fa0dd64e1c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31434947"
---
| Resurs | Gräns |
| --- | --- |
| Enheter av skala | 10 per region<sup>1</sup> |
| Cache | 5 GB per enhet<sup>1</sup> |
| Samtidiga backend anslutningar<sup>2</sup> per http-utfärdare | 2048 per enhet<sup>3</sup> |
| Maximal svarsstorlek för cachelagrade | 10MB |
| Princip för maximal storlek | 256KB |
| Maximal anpassade gateway-domäner | 20 per tjänstinstans<sup>4</sup> |


<sup>1</sup>API Management-gränserna är olika för varje prisnivå. Se prissättning nivåer och deras skala gränser går du till [priser för API Management](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> anslutningar i pooler och används igen, om inte uttryckligen har stängts av serverdelen.
<sup>3</sup> per enhet för nivåerna Basic, Standard och Premium. Utvecklare nivån är begränsad till 1024.
<sup>4</sup> tillgänglig i Premium-nivån.


