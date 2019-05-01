---
title: Cloud Partner Portal API-referens | Azure Marketplace
description: Beskrivning av förutsättningar för att använda och lista över marketplace API-åtgärder.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 9626fdb5dbdf720a65217421565fc559a0cee684
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935463"
---
<a name="cloud-partner-portal-api-reference"></a>Cloud Partner Portal API-referens
==================================

I Cloud Partner Portal REST API: er kan programmässiga hämtning och bearbetning av arbetsbelastningar, erbjudanden och utgivare profiler. API: erna använda rollbaserad åtkomstkontroll (RBAC) för att tillämpa rätt behörigheter på bearbetningstid.

Referensen innehåller teknisk information för den Cloud Partner Portal REST API: er. Nyttolast-exemplen i det här dokumentet är endast för referens och kan ändras när nya funktioner har lagts till.


<a name="prerequisites-and-considerations"></a>Krav och överväganden
-------------------------------

Innan du använder API: er, bör du granska:

- Den [krav](./cloud-partner-portal-api-prerequisites.md) artikel om du vill veta hur du lägger till ett huvudnamn för tjänsten till ditt konto och hämta en Azure Active Directory (Azure AD) åtkomsttoken för autentisering. 
- Två [samtidighetskontroll](./cloud-partner-portal-api-concurrency-control.md).
strategier som kan användas för att anropa API: erna.
- Ytterligare API [överväganden](./cloud-partner-portal-api-considerations.md), till exempel versionshantering och felhantering.


<a name="common-tasks"></a>Vanliga åtgärder
------------
Den här referensen detaljerad information om API: er för att utföra följande vanliga aktiviteter.


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

-   [Ange priser för erbjudanden som virtuell dator](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Felsökning

-   [Felsöka autentiseringsfel](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
