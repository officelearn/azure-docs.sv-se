---
title: Funktioner för kunddatabegäran - Insikter i Azure Time Series | Microsoft-dokument
description: Läs mer om funktioner för kunddatabegärandefunktioner i Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 04/17/2020
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 3578710bf066e7745215d8efacafd2cf6c005eac
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640499"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av funktionerna för kunddatabegäran

Azure Time Series Insights är en hanterad molntjänst med lagrings-, analys- och visualiseringskomponenter som gör det enkelt att inta, lagra, utforska och analysera miljarder händelser samtidigt.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Om du vill visa, exportera och ta bort personuppgifter som kan vara föremål för en begäran om registrerad användare kan en Azure Time Series Insights-klientadministratör använda antingen Azure-portalen eller REST-API:erna. Med hjälp av Azure-portalen för att betjäna begäranden om registrerade, ger en mindre komplex metod för att utföra dessa åtgärder som de flesta användare föredrar.

## <a name="identifying-customer-data"></a>Identifiera kunddata

Azure Time Series Insights anser att personuppgifter är data som är associerade med administratörer och användare av Time Series Insights. Time Series Insights lagrar Azure Active Directory-objekt-ID för användare med åtkomst till miljön. Azure-portalen visar e-postadresser för användare, men dessa e-postadresser lagras inte i Time Series Insights, de är dynamiskt uppsökt med hjälp av Azure Active Directory-objekt-ID i Azure Active Directory.

## <a name="deleting-customer-data"></a>Ta bort kunddata

En klientadministratör kan ta bort kunddata med Azure-portalen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Innan du tar bort kunddata via portalen bör du dock ta bort användarens åtkomstprinciper från time series insights-miljön i Azure-portalen. Mer information finns i [Bevilja dataåtkomst till en Time Series Insights-miljö med Azure-portalen](time-series-insights-data-access.md).

Du kan också utföra borttagningsåtgärder på åtkomstprinciper med REST API. Mer information finns i [Åtkomstprinciper - Ta bort](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete).

Time Series Insights är integrerat med principbladet i Azure-portalen. Både Time Series Insights och policybladet gör att du kan visa, exportera och ta bort användardata som lagras i tjänsten. Alla borttagningsåtgärder som vidtas i principbladet i Azure-portalen resulterar i radering av användardata i Time Series Insights. Om en användare till exempel har en sparad personlig fråga tas frågan bort permanent från Utforskaren för Time Series Insights. Om användaren har en sparad delad fråga kvarstår frågan, men användarinformationen tas bort permanent. Följande anmärkning innehåller instruktioner om hur du utför dessa uppgifter.

## <a name="exporting-customer-data"></a>Exportera kunddata

På samma sätt som att ta bort data kan en klientadministratör visa och exportera data som lagras i Time Series Insights från principbladet i Azure-portalen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Om du är klientadministratör kan du visa principer för dataåtkomst i time series insights-miljön i Azure-portalen. Mer information finns i [Bevilja dataåtkomst till en Time Series Insights-miljö med Azure-portalen](time-series-insights-data-access.md).

Det är också möjligt att utföra exportåtgärder på åtkomstprinciper med hjälp av åtgärden "lista efter miljö" i det angivna REST API-api:et. Mer information finns i [Åtkomstprinciper - Lista efter miljö](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Så här tar du bort data som lagras i Time Series Insights

Personuppgifter kan ta sig in i Time Series Insights-lagring, ett annat scenario än användar- och administratörsdata. Om du betraktar de data som lagras i Time Series Insights som personuppgifter kan du exportera och ta bort dessa data med hjälp av följande steg:

**Visa och exportera data**

Om du vill visa och exportera data som lagras i Time Series Insights måste du söka efter dessa data. Du kan använda fråga-API:erna För tidsseriestatistik eller Time Series Insights för att visa och exportera data. Om du vill visa och exportera data med hjälp av utforskaren Time Series Insights söker du först efter användardata i fråga. När du har sökt högerklickar du på diagrammet och väljer **Utforska händelser**. Händelserutnätet visas och innehåller alternativ för att exportera data som CSV och JSON.

Mer information finns i [Utforskaren för Azure Time Series Insights](time-series-insights-explorer.md).

**Ta bort data**

För närvarande stöder Time Series Insights inte detaljerad radering av data. Time Series Insights ger dock möjlighet att ta bort kunddata som lagras i Time Series Insights genom att konfigurera bevarandeprinciper. Du kan justera kvarhållningsperioden för hela Time Series Insights-miljön till valfritt antal dagar för att stödja dina borttagningskrav.

Mer information finns [i Konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att bevilja dataåtkomst till din Azure Time Series Insights-miljö](./time-series-insights-data-access.md).

* Visa [utforskaren för Azure Time Series Insights](time-series-insights-explorer.md).

* Läs mer om [Att konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).