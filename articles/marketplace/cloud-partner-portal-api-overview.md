---
title: Cloud Partner Portal API-referens – Microsoft Commercial Marketplace
description: Beskrivning av, krav som ska användas och lista över Marketplace API-åtgärder.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 2f79dea4baaa2589333af4e15aece06c1d86b9c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516330"
---
# <a name="cloud-partner-portal-api-reference"></a>API-referens för Cloud Partner Portal

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska [ändringarna i cpp-API: erna](#changes-to-cpp-apis-after-the-migration-to-partner-center) som anges i det här dokumentet för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Cloud Partner Portal REST-API: er tillåter programmerings hämtning och manipulering av arbets belastningar, erbjudanden och utgivar profiler. API: erna använder rollbaserad åtkomst kontroll (RBAC) för att genomdriva korrekta behörigheter vid bearbetnings tiden.

Den här referensen ger teknisk information för Cloud Partner Portal REST-API: er. Nytto Last exemplen i det här dokumentet är endast för referens och kan komma att ändras när nya funktioner läggs till.

## <a name="prerequisites-and-considerations"></a>Krav och överväganden

Innan du använder API: erna bör du granska:

- I [krav](./cloud-partner-portal-api-prerequisites.md) artikeln får du lära dig hur du lägger till ett huvud namn för tjänsten i ditt konto och hur du får en Azure Active Directory (Azure AD)-åtkomsttoken för autentisering.
- De två [samtidighets kontroll](./cloud-partner-portal-api-concurrency-control.md) strategierna som är tillgängliga för anrop av dessa API: er.
- Ytterligare API- [överväganden](./cloud-partner-portal-api-considerations.md), till exempel versions hantering och fel hantering.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Ändringar i CPP-API: er efter migrering till Partner Center

| **API** | **Ändra beskrivning** | **Påverkan** |
| ------- | ---------------------- | ---------- |
| Publicera, GoLive, Avbryt | För migrerade erbjudanden har svars huvudet ett annat format men fortsätter att fungera på samma sätt, vilket anger en relativ sökväg för att hämta åtgärds statusen. | När du skickar någon av motsvarande POST-förfrågningar för ett erbjudande får plats huvudet ett av två format beroende på status för migreringen:<ul><li>Ej migrerade erbjudanden<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrerade erbjudanden<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Hämta åtgärd | För erbjudande typer som tidigare stödde fältet notification-email i svaret, är det här fältet inaktuellt och inte längre returnerat för migrerade erbjudanden. | För migrerade erbjudanden skickar vi inte längre meddelanden till listan över e-postmeddelanden som anges i förfrågningarna. I stället justeras API-tjänsten med e-postprocessen för meddelanden i Partner Center för att skicka e-postmeddelanden. Mer specifikt skickas meddelanden till den e-postadress som anges i avsnittet säljare kontakt information i dina konto inställningar i Partner Center för att meddela dig om åtgärds förlopp.<br><br>Kontrol lera e-postadressen som angetts i avsnittet om kontakt information för säljaren i [konto inställningarna](https://partner.microsoft.com/dashboard/account/management) i Partner Center för att säkerställa att rätt e-postadress tillhandahålls för meddelanden.  |

## <a name="common-tasks"></a>Vanliga åtgärder

Denna referens information innehåller API: er för att utföra följande vanliga uppgifter.

### <a name="offers"></a>Erbjudanden

- [Hämta alla erbjudanden](./cloud-partner-portal-api-retrieve-offers.md)
- [Hämta ett Special erbjudande](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Hämta erbjudandestatus](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Skapa ett erbjudande](./cloud-partner-portal-api-creating-offer.md)
- [Publicera ett erbjudande](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Åtgärder

- [Hämta åtgärder](./cloud-partner-portal-api-retrieve-operations.md)
- [Avbryt åtgärder](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicera en app

- [Sätt igång](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Andra uppgifter

- [Ange prissättning för erbjudanden för virtuella datorer](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Felsökning

- [Felsöka autentiseringsfel](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
