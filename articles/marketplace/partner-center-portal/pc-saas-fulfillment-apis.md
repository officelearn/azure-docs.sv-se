---
title: 'SaaS uppfyllelse API: er | Azure Marketplace'
description: Introducerar versionerna av uppfyllelse-API:erna som gör att du kan integrera dina SaaS-erbjudanden med Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275738"
---
# <a name="saas-fulfillment-apis"></a>API:er för uppfyllnad av SaaS

SaaS Uppfyllelse API:er gör det möjligt för oberoende programvaruleverantörer (ISV: er) att integrera sina SaaS-program med Azure Marketplace. Dessa API:er gör det möjligt för ISV-program att delta i alla handelsaktiverade kanaler: direkt, partnerledd (återförsäljare) och fältledd.  De är ett krav för att lista transactable SaaS-erbjudanden på Azure Marketplace.

> [!WARNING]
> Den aktuella versionen av detta API är version 2, som bör användas för alla nya SaaS-erbjudanden.  Version 1 av API:et är inaktuell och underhålls för att stödja befintliga erbjudanden.


## <a name="business-model-support"></a>Support för affärsmodeller

Det här API:et stöder följande funktioner för affärsmodeller. Du kan:

* Ange flera planer för ett erbjudande. Dessa planer har olika funktionalitet och kan prissättas på olika sätt.
* Ge ett erbjudande på en per plats eller per användare faktureringsmodell basis.
* Ange faktureringsalternativ för månads- och årsfakturer (betald i förskott).
* Ge privat prissättning till en kund baserat på ett förhandlat affärsavtal.


## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det registrerar du ditt SaaS-program i [Azure-portalen](https://ms.portal.azure.com) enligt beskrivningen i [Registrera ett Azure AD-program](./pc-saas-registration.md).  Använd därför den senaste versionen av det här gränssnittet för utveckling: [SaaS Fulfillment API Version 2](./pc-saas-fulfillment-api-v2.md).
