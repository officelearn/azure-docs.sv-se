---
title: 'API: er för SaaS-utförande | Azure Marketplace'
description: 'Introducerar de versioner av API: erna för utförande som gör att du kan integrera dina SaaS-erbjudanden med Azure Marketplace.'
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275738"
---
# <a name="saas-fulfillment-apis"></a>API:er för uppfyllnad av SaaS

API: erna för SaaS-utförande gör det möjligt för oberoende program varu leverantörer (ISV) att integrera sina SaaS-program med Azure Marketplace. Dessa API: er gör det möjligt för ISV-program att delta i alla Commerce-aktiverade kanaler: direkt, partner lampa (åter försäljare) och fält-ledde.  De är ett krav för att kunna registrera transactable SaaS-erbjudanden på Azure Marketplace.

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
