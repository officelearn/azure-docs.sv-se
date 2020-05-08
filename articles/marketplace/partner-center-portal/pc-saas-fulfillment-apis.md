---
title: 'API: er för SaaS-uppfyllelse i Microsofts kommersiella marknads platser'
description: 'En introduktion till uppfyllande API: er som gör att du kan integrera dina SaaS-erbjudanden i Microsoft AppSource och Azure Marketplace.'
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858088"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>API: er för SaaS-uppfyllelse i Microsofts kommersiella marknads platser

API: erna för SaaS-utförande gör det möjligt för oberoende program varu leverantörer (ISV) att integrera sina SaaS-program i Microsoft AppSource och Azure Marketplace. Dessa API: er gör det möjligt för ISV-program att delta i alla Commerce-aktiverade kanaler: direkt, partner lampa (åter försäljare) och fält indikator. De krävs för att visa en lista över transactable SaaS-erbjudanden i Microsoft AppSource och Azure Marketplace.

> [!WARNING]
> Den aktuella versionen av detta API är version 2, som ska användas för alla nya SaaS-erbjudanden.  Version 1 av API: et är inaktuell och underhålls för att stödja befintliga erbjudanden.

## <a name="business-model-support"></a>Stöd för affärs modell

Detta API stöder följande funktioner för affärs modell. Du kan:

* Ange flera planer för ett erbjudande. Dessa planer har olika funktioner och kan prisas annorlunda.
* Tillhandahålla ett erbjudande på en per plats eller per användares fakturerings modell.
* Ange fakturerings alternativ per månad och år (betald uppstart).
* Tillhandahålla privat prissättning till en kund baserat på ett förhandlat affärs avtal.


## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, registrera ditt SaaS-program i [Azure Portal](https://ms.portal.azure.com) enligt beskrivningen i [Registrera ett Azure AD-program](./pc-saas-registration.md).  Sedan använder du den mest aktuella versionen av det här gränssnittet för utveckling: [SaaS uppfyllelse API version 2](./pc-saas-fulfillment-api-v2.md).
