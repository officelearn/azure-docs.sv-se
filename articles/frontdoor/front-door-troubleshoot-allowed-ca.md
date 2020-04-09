---
title: Tillåten certifikatutfärdar för att aktivera anpassad HTTPS på Azure Front Door
description: Om du använder ditt eget certifikat för att aktivera HTTPS på en anpassad domän måste du använda en tillåten certifikatutfärdarmyndighet för att skapa den.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874678"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Tillåten certifikatutfärdar för att aktivera anpassad HTTPS på Azure Front Door

För en anpassad Azure Front Door-domän måste du använda en tillåten certifikatutfärdarcertifikat när du [aktiverar HTTPS-funktionen med hjälp av ditt eget certifikat.](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) Annars, om du använder en icke-tillåten certifikatutfärdar eller ett självsignerat certifikat, kommer din begäran att avvisas.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
