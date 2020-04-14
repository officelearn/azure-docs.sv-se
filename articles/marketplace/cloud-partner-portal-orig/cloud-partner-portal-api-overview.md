---
title: API-referens för molnpartnerportal | Azure Marketplace
description: Beskrivning av förutsättningar att använda och lista över MARKETPLACE API-åtgärder.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256424"
---
# <a name="cloud-partner-portal-api-reference"></a>API-referens för Cloud Partner Portal

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna av [CPP API:er](#changes-to-cpp-apis-after-the-migration-to-partner-center) som visas i det här dokumentet för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Rest-API:erna för Cloud Partner Portal tillåter programmatisk hämtning och manipulering av arbetsbelastningar, erbjudanden och utgivarprofiler. API:erna använder rollbaserad åtkomstkontroll (RBAC) för att framtvinga korrekta behörigheter vid bearbetningstiden.

Den här referensen innehåller teknisk information för REST-API:erna för Cloud Partner Portal. Nyttolastexemplen i det här dokumentet är endast till referens och kan komma att ändras när nya funktioner läggs till.

## <a name="prerequisites-and-considerations"></a>Förutsättningar och överväganden

Innan du använder API:erna bör du granska:

- I artikeln [Förutsättningar](./cloud-partner-portal-api-prerequisites.md) får du lära dig hur du lägger till ett tjänsthuvudnamn i ditt konto och får en Azure Active Directory-åtkomsttoken (Azure AD) för autentisering.
- De två [strategier för samtidighetskontroll](./cloud-partner-portal-api-concurrency-control.md) som finns tillgängliga för att anropa dessa API:er.
- Ytterligare [API-överväganden](./cloud-partner-portal-api-considerations.md), till exempel versionshantering och felhantering.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Ändringar i CPP API:er efter migreringen till Partner Center

| **Api** | **Ändra beskrivning** | **Påverkan** |
| ------- | ---------------------- | ---------- |
| PUBLICERA, GoLive, Avbryt | För migrerade erbjudanden har svarshuvudet ett annat format men fortsätter att fungera på samma sätt och anger en relativ sökväg för att hämta åtgärdsstatusen. | När du skickar någon av motsvarande POST-begäranden för ett erbjudande har platshuvudet ett av två format beroende på erbjudandets migreringsstatus:<ul><li>Ej migrerade erbjudanden<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrerade erbjudanden<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| FÅ-åtgärd | För erbjudandetyper som tidigare stödde fältet meddelande-e-post i svaret kommer det här fältet att vara inaktuellt och inte längre returnerat för migrerade erbjudanden. | För migrerade erbjudanden skickar vi inte längre meddelanden till listan över e-postmeddelanden som anges i begärandena. Api-tjänsten justeras i stället med e-postprocessen för meddelanden i Partner Center för att skicka e-postmeddelanden. Meddelanden skickas specifikt till den e-postadress som anges i avsnittet Kontaktinformation för säljare i dina kontoinställningar i Partnercenter för att meddela dig om driftens förlopp.<br><br>Läs e-postadressen i avsnittet Kontaktinformation för säljare i dina [kontoinställningar](https://partner.microsoft.com/dashboard/account/management) i Partnercenter för att säkerställa att rätt e-post tillhandahålls för meddelanden.  |

## <a name="common-tasks"></a>Vanliga åtgärder

Den här referensinformationen api:er för att utföra följande vanliga uppgifter.

### <a name="offers"></a>Erbjudanden

- [Hämta alla erbjudanden](./cloud-partner-portal-api-retrieve-offers.md)
- [Hämta ett specifikt erbjudande](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Hämta erbjudandestatus](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Skapa ett erbjudande](./cloud-partner-portal-api-creating-offer.md)
- [Publicera ett erbjudande](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Åtgärder

- [Hämta åtgärder](./cloud-partner-portal-api-retrieve-operations.md)
- [Avbryt åtgärder](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicera en app

- [Sätt igång](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Andra uppgifter

- [Ange priser för erbjudanden för virtuella datorer](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Felsökning

- [Felsöka autentiseringsfel](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
