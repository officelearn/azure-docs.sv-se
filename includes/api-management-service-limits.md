---
title: ta med fil
description: ta med fil
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 2d4c8c2c831bd6ef16f60c34a6353f4a742798f7
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76159672"
---
| Resurs | Gräns |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximalt antal skalnings enheter | 10 per region<sup>1</sup> |
| Cachestorlek | 5 GiB per enhet<sup>2</sup> |
| Samtidiga Server dels anslutningar<sup>3</sup> per http-utfärdare | 2 048 per enhet<sup>4</sup> |
| Maximal storlek för cachelagrat svar | 2 MiB |
| Högsta princip dokument storlek | 256 KiB<sup>5</sup> |
| Maximalt antal anpassade Gateway-domäner per tjänst instans<sup>6</sup> | 20 |
| Maximalt antal CA-certifikat per tjänst instans | 10 |
| Maximalt antal tjänst instanser per prenumeration<sup>7</sup> | 20 |
| Maximalt antal prenumerationer per tjänst instans<sup>7</sup> | 500 |
| Maximalt antal klient certifikat per tjänst instans<sup>7</sup> | 50 |
| Maximalt antal API: er per tjänst instans<sup>7</sup> | 50 |
| Maximalt antal API-åtgärder per tjänst instans<sup>7</sup> | 1,000 |
| Högsta total varaktighet för begäran<sup>7</sup> | 30 sekunder |
| Maximal buffrad nytto Last storlek<sup>7</sup> | 2 MiB |
| Maximal URL-storlek<sup>8</sup> för begäran | 4096 byte |

<sup>1</sup> Skalnings gränser beror på pris nivån. Se [API Management priser](https://azure.microsoft.com/pricing/details/api-management/)för att se pris nivåerna och deras skalnings gränser.<br/>
<sup>2</sup> Storleken per enhets cacheminne beror på pris nivån. Se [API Management priser](https://azure.microsoft.com/pricing/details/api-management/)för att se pris nivåerna och deras skalnings gränser.<br/>
<sup>3</sup> Anslutningar i pooler och återanvänds, om de inte uttryckligen stängs av Server delen.<br/>
<sup>4</sup> Den här gränsen är per enhet för nivåerna Basic, standard och Premium. Developer-nivån är begränsad till 1 024. Den här gränsen gäller inte för förbruknings nivån.<br/>
<sup>5</sup> Den här gränsen gäller för nivåerna Basic, standard och Premium. I förbruknings nivån är princip dokument storleken begränsat till 4 KiB.<br/>
<sup>6</sup> Den här resursen är bara tillgänglig på Premium-nivån.<br/>
<sup>7</sup> Den här resursen gäller endast för förbruknings nivån.<br/>
<sup>8</sup> Gäller endast för förbruknings nivån. Innehåller en sträng med upp till 2048 byte lång frågesträng.<br/>
