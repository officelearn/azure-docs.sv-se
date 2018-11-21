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
ms.openlocfilehash: 676bd3b3a369e3f834016f626a8e69f6b41d9b23
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52170708"
---
| Resurs | Gräns |
| --- | --- |
| Enheter av skala | 10 per region<sup>1</sup> |
| Cache | 5 GB per enhet<sup>1</sup> |
| Samtidiga serverdel anslutningar<sup>2</sup> per HTTP-utfärdare | 2048 per enhet<sup>3</sup> |
| Maximal cachelagrade svarsstorlek | 2MB |
| Princip för maximal storlek | 256KB |
| Maximal anpassade gateway-domäner | 20 per tjänstinstans<sup>4</sup> |


<sup>1</sup>API Management-gränserna är olika för varje prisnivå. Om du vill se prissättningen nivåerna och deras skalningsgränser går till [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> anslutningar i pooler och används igen, om inte explicit avslutades av serverdelen.
<sup>3</sup> per enhet för nivåerna Basic, Standard och Premium. Developer-nivån är begränsad till 1024.
<sup>4</sup> tillgänglig på Premium-nivån.


