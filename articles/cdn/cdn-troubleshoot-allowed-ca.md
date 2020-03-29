---
title: Tillåten certifikatutfärdar för att aktivera anpassad HTTPS på Azure CDN
description: Om du använder ditt eget certifikat för att aktivera HTTPS på en anpassad domän måste du använda en tillåten certifikatutfärdarmyndighet för att skapa den.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919982"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Tillåten certifikatutfärdar för att aktivera anpassad HTTPS på Azure CDN

Du måste uppfylla specifika certifikatkrav när du [aktiverar HTTPS-funktionen med hjälp av ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) för en anpassad domän för Azure Content Delivery Network (CDN). **Azure CDN Standard från Microsoft-profilen** kräver ett certifikat från en av de godkända certifikatutfärdarna i följande lista. Om ett certifikat från en icke godkänd certifikatutfärdar eller om ett självsignerat certifikat används avvisas begäran. **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon-profiler** accepterar alla giltiga certifikat från alla giltiga certifikatutfärdare.

> [!NOTE]
> Alternativet att använda ditt eget certifikat för att aktivera den anpassade domän-HTTPS-funktionen är *inte* tillgängligt för **Azure CDN Standard från Akamai-profiler.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

