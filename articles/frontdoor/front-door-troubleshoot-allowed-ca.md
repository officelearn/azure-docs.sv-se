---
title: Tillåten certifikat utfärdare för att aktivera anpassad HTTPS på Azures front dörr
description: Om du använder ett eget certifikat för att aktivera HTTPS på en anpassad domän, måste du använda en tillåten certifikat utfärdare (CA) för att skapa det.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80874678"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Tillåtna certifikat utfärdare för att aktivera anpassad HTTPS på Azures front dörr

När du [aktiverar https-funktionen med hjälp av ditt eget certifikat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)för en anpassad domän för Azure-hemdörr måste du använda en tillåten certifikat utfärdare (ca) för att skapa ett TLS/SSL-certifikat. Annars, om du använder en icke-tillåten CA eller ett självsignerat certifikat, kommer din begäran att avvisas.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
