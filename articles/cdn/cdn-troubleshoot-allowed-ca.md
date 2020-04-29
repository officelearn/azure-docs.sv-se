---
title: Tillåten certifikat utfärdare för att aktivera anpassad HTTPS på Azure CDN
description: Om du använder ett eget certifikat för att aktivera HTTPS på en anpassad domän, måste du använda en tillåten certifikat utfärdare (CA) för att skapa det.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: faf51dbb1f1c3c0346b1ae9104494538efcc2ee7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259982"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Tillåtna certifikat utfärdare för att aktivera anpassad HTTPS på Azure CDN

Du måste uppfylla specifika certifikat krav när du [aktiverar https-funktionen genom att använda ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) för en anpassad Azure Content Delivery Network-domän (CDN). **Azure CDN Standard från Microsoft** Profile kräver ett certifikat från en av de godkända certifikat utfärdarna (ca) i följande lista. Om ett certifikat från en certifikat utfärdare som inte är godkänt eller om ett självsignerat certifikat används, avvisas begäran. **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -profiler accepterar alla giltiga certifikat från valfri giltig certifikat utfärdare.

> [!NOTE]
> Alternativet att använda ditt eget certifikat för att aktivera HTTPS-funktionen för anpassade domäner är *inte* tillgänglig för **Azure CDN Standard från Akamai** -profiler. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

