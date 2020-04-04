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
ms.openlocfilehash: 5608683578a796ae0fffff7544a749043e8698fd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656183"
---
| Resurs | Gräns |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximalt antal skalenheter | 10 per region<sup>1</sup> |
| Cachestorlek | 5 GiB per enhet<sup>2</sup> |
| Samtidiga backend-anslutningar<sup>3</sup> per HTTP-behörighet | 2 048 per enhet<sup>4</sup> |
| Maximal cachelagd svarsstorlek | 2 MiB (2 mib) |
| Maximal principdokumentstorlek | 256 KiB<sup>5</sup> |
| Högsta anpassade gatewaydomäner per tjänstinstans<sup>6</sup> | 20 |
| Maximalt antal certifikatutfärdarcertifikat per tjänstinstans | 10 |
| Maximalt antal tjänstinstanser per prenumeration<sup>7</sup> | 20 |
| Maximalt antal prenumerationer per tjänstinstans<sup>7</sup> | 500 |
| Maximalt antal klientcertifikat per tjänstinstans<sup>7</sup> | 50 |
| Maximalt antal API:er per tjänstinstans<sup>7</sup> | 50 |
| Maximalt antal API-åtgärder per tjänstinstans<sup>7</sup> | 1,000 |
| Maximal total varaktighet för begäran<sup>7</sup> | 30 sekunder |
| Maximal buffrad nyttolaststorlek<sup>7</sup> | 2 MiB (2 mib) |
| Url-storlek för maximal begäran<sup>8</sup> | 4096 byte |

<sup>1.</sup> Skalningsgränserna beror på prisnivån. Mer om du vill se prisnivåerna och deras skalningsgränser finns i [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2.</sup> Cachestorleken per enhet beror på prisnivån. Mer om du vill se prisnivåerna och deras skalningsgränser finns i [API Management-priser](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3.</sup> Anslutningar poolas och återanvänds om de inte uttryckligen stängs av den bakre delen.<br/>
<sup>4.</sup> Den här gränsen är per enhet på nivåerna Basic, Standard och Premium. Utvecklarnivån är begränsad till 1 024. Den här gränsen gäller inte för förbrukningsnivån.<br/>
<sup>5 5</sup> Den här gränsen gäller för nivåerna Basic, Standard och Premium. På förbrukningsnivån är principdokumentstorleken begränsad till 16 KiB.<br/>
<sup>6.</sup> Den här resursen är endast tillgänglig på Premium-nivån.<br/>
<sup>7.</sup> Den här resursen gäller endast för förbrukningsnivån.<br/>
<sup>8.</sup> Gäller endast för förbrukningsnivån. Innehåller en lång frågesträng på upp till 2 2048 byte.<br/>
