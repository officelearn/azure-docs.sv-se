---
title: API-referens för molnpartnerportal | Azure Marketplace
description: Beskrivning av förutsättningar att använda och lista över MARKETPLACE API-åtgärder.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288554"
---
<a name="cloud-partner-portal-api-reference"></a>API-referens för Molnpartnerportal
==================================

Rest-API:erna för Cloud Partner Portal tillåter programmatisk hämtning och manipulering av arbetsbelastningar, erbjudanden och utgivarprofiler. API:erna använder rollbaserad åtkomstkontroll (RBAC) för att framtvinga korrekta behörigheter vid bearbetningstiden.

Den här referensen innehåller teknisk information för REST-API:erna för Cloud Partner Portal. Nyttolastexemplen i det här dokumentet är endast till referens och kan komma att ändras när nya funktioner läggs till.


<a name="prerequisites-and-considerations"></a>Förutsättningar och överväganden
-------------------------------

Innan du använder API:erna bör du granska:

- I artikeln [Förutsättningar](./cloud-partner-portal-api-prerequisites.md) får du lära dig hur du lägger till ett tjänsthuvudnamn i ditt konto och får en Azure Active Directory-åtkomsttoken (Azure AD) för autentisering. 
- De två [samtidighetskontroll](./cloud-partner-portal-api-concurrency-control.md).
strategier för att anropa dessa API:er.
- Ytterligare [API-överväganden](./cloud-partner-portal-api-considerations.md), till exempel versionshantering och felhantering.


<a name="common-tasks"></a>Vanliga åtgärder
------------
Den här referensinformationen api:er för att utföra följande vanliga uppgifter.


### <a name="offers"></a>Erbjudanden

-   [Hämta alla erbjudanden](./cloud-partner-portal-api-retrieve-offers.md)
-   [Hämta ett specifikt erbjudande](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Hämta erbjudandestatus](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Skapa ett erbjudande](./cloud-partner-portal-api-creating-offer.md)
-   [Publicera ett erbjudande](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Åtgärder

-   [Hämta åtgärder](./cloud-partner-portal-api-retrieve-operations.md)
-   [Avbryt åtgärder](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicera en app

-   [Sätt igång](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Andra uppgifter

-   [Ange priser för erbjudanden för virtuella datorer](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Felsökning

-   [Felsöka autentiseringsfel](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
