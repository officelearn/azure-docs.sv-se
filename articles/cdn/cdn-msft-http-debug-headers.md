---
title: Felsöka HTTP-huvuden för Azure CDN från Microsoft | Microsoft-dokument
description: Felsökningscachebegäran rubriker ger ytterligare information om cacheprincipen som tillämpas på den begärda tillgången. Dessa rubriker är specifika för Azure CDN från Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260422"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Felsök HTTP-huvudet för Azure CDN från Microsoft
Felsökningssvarshuvudet `X-Cache`innehåller information om vilket lager av CDN-stacken som innehållet har serverats från. Det här huvudet är specifikt för Azure CDN från Microsoft.

### <a name="response-header-format"></a>Format för svarsrubrik

Huvud | Beskrivning
-------|------------
X-Cache: TCP_HIT | Det här huvudet returneras när innehållet visas från CDN-edge-cachen. 
X-Cache: TCP_REMOTE_HIT | Det här huvudet returneras när innehållet visas från CDN:s regionala cache (Origin-sköldlagret)
X-Cache: TCP_MISS | Det här huvudet returneras när det finns en cachemiss och innehållet visas från Origin. 


