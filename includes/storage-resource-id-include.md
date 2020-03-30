---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249923"
---
Ett Azure AD-resurs-ID anger målgruppen för vilken en token som utfärdas kan användas för att ge åtkomst till en Azure-resurs. När det gäller Azure Storage kan resurs-ID vara specifikt för ett enda lagringskonto, eller så kan det gälla för alla lagringskonto. I följande tabell beskrivs de värden som du kan ange för resurs-ID:

|Resurs-ID  |Beskrivning  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Tjänstslutpunkten för ett visst lagringskonto. Använd det här värdet om du vill hämta en token för att godkänna begäranden till det specifika Azure Storage-kontot och tjänsten. Ersätt värdet inom parentes med namnet på ditt lagringskonto.      |
|`https://storage.azure.com/`     | Används för att hämta en token för att auktorisera begäranden till alla Azure Storage-konton.        |
