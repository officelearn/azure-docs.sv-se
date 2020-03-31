---
title: Felsöka HTTP-huvuden för Azure CDN från Microsoft | Microsoft-dokument
description: Felsökningscachebegäran rubriker ger ytterligare information om cacheprincipen som tillämpas på den begärda tillgången. Dessa rubriker är specifika för Azure CDN från Microsoft.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814067"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Felsök HTTP-huvudet för Azure CDN från Microsoft
Felsökningssvarshuvudet `X-Cache`innehåller information om vilket lager av CDN-stacken som innehållet har serverats från. Det här huvudet är specifikt för Azure CDN från Microsoft.

### <a name="response-header-format"></a>Format för svarsrubrik

Huvud | Beskrivning
-------|------------
X-Cache: TCP_HIT | Det här huvudet returneras när innehållet visas från CDN-edge-cachen. 
X-Cache: TCP_REMOTE_HIT | Det här huvudet returneras när innehållet visas från CDN:s regionala cache (Origin-sköldlagret)
X-Cache: TCP_MISS | Det här huvudet returneras när det finns en cachemiss och innehållet visas från Origin. 


