---
title: Cloud Partner Portal API-referens | Azure Marketplace
description: Beskrivning av, krav som ska användas och lista över Marketplace API-åtgärder.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b6591e1780d03cbfaff70fbd19ec3dfd274fae79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819632"
---
<a name="cloud-partner-portal-api-reference"></a>Cloud Partner Portal API-referens
==================================

Cloud Partner Portal REST-API: er tillåter programmerings hämtning och manipulering av arbets belastningar, erbjudanden och utgivar profiler. API: erna använder rollbaserad åtkomst kontroll (RBAC) för att genomdriva korrekta behörigheter vid bearbetnings tiden.

Den här referensen ger teknisk information för Cloud Partner Portal REST-API: er. Nytto Last exemplen i det här dokumentet är endast för referens och kan komma att ändras när nya funktioner läggs till.


<a name="prerequisites-and-considerations"></a>Krav och överväganden
-------------------------------

Innan du använder API: erna bör du granska:

- I [krav](./cloud-partner-portal-api-prerequisites.md) artikeln får du lära dig hur du lägger till ett huvud namn för tjänsten i ditt konto och hur du får en Azure Active Directory (Azure AD)-åtkomsttoken för autentisering. 
- Den två [samtidiga kontrollen](./cloud-partner-portal-api-concurrency-control.md).
strategier som är tillgängliga för att anropa dessa API: er.
- Ytterligare API- [överväganden](./cloud-partner-portal-api-considerations.md), till exempel versions hantering och fel hantering.


<a name="common-tasks"></a>Vanliga åtgärder
------------
Denna referens information innehåller API: er för att utföra följande vanliga uppgifter.


### <a name="offers"></a>Erbjudande

-   [Hämta alla erbjudanden](./cloud-partner-portal-api-retrieve-offers.md)
-   [Hämta ett Special erbjudande](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Hämta erbjudandestatus](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Skapa ett erbjudande](./cloud-partner-portal-api-creating-offer.md)
-   [Publicera ett erbjudande](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Åtgärder

-   [Hämta åtgärder](./cloud-partner-portal-api-retrieve-operations.md)
-   [Avbryt åtgärder](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicera en app

-   [Sätt igång](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Andra uppgifter

-   [Ange prissättning för erbjudanden för virtuella datorer](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Felsökning

-   [Felsöka autentiseringsfel](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
