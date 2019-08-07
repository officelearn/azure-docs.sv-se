---
title: Felsöka HTTP-huvuden för Azure CDN från Microsoft | Microsoft Docs
description: I debug-begärandehuvuden finns ytterligare information om den cachelagrade princip som tillämpas på den begärda till gången. De här rubrikerna är bara för Azure CDN från Microsoft.
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
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814067"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Felsöka HTTP-huvud för Azure CDN från Microsoft
Rubriken för fel söknings `X-Cache`svar, innehåller information om vilket lager i CDN-stacken som innehållet betjänades från. Den här rubriken är unik för Azure CDN från Microsoft.

### <a name="response-header-format"></a>Format för svars huvud

Huvud | Beskrivning
-------|------------
X-cache: TCP_HIT | Den här rubriken returneras när innehållet hanteras från CDN Edge-cachen. 
X-cache: TCP_REMOTE_HIT | Den här rubriken returneras när innehållet hanteras från CDN regional cache (ursprungs skikt)
X-cache: TCP_MISS | Den här rubriken returneras när det finns ett cache-innehåll och innehållet betjänas från ursprunget. 


