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
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553880"
---
| Resurs | Gräns |
| --- | --- |
| Maximalt antal skalningsenheter | 10 per region<sup>1</sup> |
| Cachestorlek | 5 GB per enhet<sup>2</sup> |
| Anslutningar till backend-<sup>3</sup> per HTTP-utfärdare | 2 048 per enhet<sup>4</sup> |
| Maximal cachelagrade svarsstorlek | 2 MB |
| Princip för maximal storlek | 256 KB<sup>5</sup> | 
| Maximal anpassade gateway domäner per tjänstinstans<sup>6</sup> | 20 |
| Maximalt antal certifikatutfärdarcertifikat per instans | 10 | 
| Maximalt antal instanser av tjänsten per prenumeration<sup>7</sup> | 20 | 
| Högsta antalet prenumerationer per tjänstinstans<sup>7</sup> | 500 |
| Maximalt antal klientcertifikat per tjänstinstans<sup>7</sup> | 50 | 
| Maximalt antal API: er per tjänstinstans<sup>7</sup> | 50 | 
| Maximalt antal API-åtgärder per tjänstinstans<sup>7</sup> | 1,000 | 
| Maximal total varaktighet för begäran<sup>7</sup> | 30 sekunder | 
| Maximalt buffras nyttolast<sup>7</sup> | 2 MB | 


<sup>1</sup>skalning gränserna beror på prisnivå. Prisnivåerna och deras skalningsgränser finns i [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>per enhet cache storleken beror på prisnivå. Prisnivåerna och deras skalningsgränser finns i [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>anslutningar i pooler och återanvändas om inte explicit avslutades av backend-servern.<br/>
<sup>4</sup>den här gränsen är per enhet för nivåerna Basic, Standard och Premium. Developer-nivån är begränsad till 1 024. Den här gränsen gäller inte för förbrukning-nivå.<br/> 
<sup>5</sup>den här begränsningen gäller för nivåerna Basic, Standard och Premium. Consumption-nivån är principen dokumentstorlek begränsad till 4 KB.<br/>
<sup>6</sup>resursen är tillgänglig på Premium-nivån.<br/>
<sup>7</sup>den här resursen är kopplad till förbrukning-nivån.<br/>



