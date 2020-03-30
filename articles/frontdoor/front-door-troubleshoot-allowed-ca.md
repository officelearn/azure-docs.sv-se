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
ms.openlocfilehash: cac6bc9895f2b8778f2b27cc6b1dff4d4b898ae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471531"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Tillåten certifikatutfärdar för att aktivera anpassad HTTPS på Azure Front Door

För en anpassad Azure Front Door-domän måste du använda en tillåten certifikatutfärdarcertifikat när du [aktiverar HTTPS-funktionen med ditt eget certifikat.](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) Annars, om du använder en icke-tillåten certifikatutfärdar eller ett självsignerat certifikat, kommer din begäran att avvisas.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
