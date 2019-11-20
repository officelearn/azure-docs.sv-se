---
title: Tillåten certifikat utfärdare för att aktivera anpassad HTTPS i Azure-tjänsten för front dörr
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
ms.openlocfilehash: 62420889d9a4cb1e9d1c570a0845c704fca56cb3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184587"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Tillåtna certifikat utfärdare för att aktivera anpassad HTTPS i Azures frontend-tjänst

När du [aktiverar https-funktionen med hjälp av ditt eget certifikat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)för en anpassad domän för Azure-tjänsten för front dörr måste du använda en tillåten certifikat utfärdare (ca) för att skapa SSL-certifikatet. Annars, om du använder en icke-tillåten CA eller ett självsignerat certifikat, kommer din begäran att avvisas.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
