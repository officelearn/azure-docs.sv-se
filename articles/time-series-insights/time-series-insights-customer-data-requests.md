---
title: Begär kundinformation i Azure Time Series Insights - kunddata begär funktioner i Azure Time Series Insights | Microsoft Docs
description: Sammanfattning av Azure Time Series Insights-kund datafunktioner.
author: ashannon7
ms.author: anshan
manager: cshankar
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: c749a61fa26b13ffe67c5913d1a11b6c1f0e186b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276068"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kundens datafunktioner

Azure Time Series Insights är en hanterad molntjänst med komponenter för lagring, analys och visualisering som gör det enkelt att mata in, lagra, utforska och analysera miljarder händelser samtidigt.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Om du vill visa, exportera och ta bort personliga data som kan vara föremål för en begäran om ämne, kan en Azure Time Series Insights-klientadministratör använda Azure portal eller REST-API: er. Med Azure portal till DSR-begäranden för tjänsten ger en mindre komplex metod för att utföra dessa åtgärder som de flesta användare föredrar.

## <a name="identifying-customer-data"></a>Identifiera kunddata

Azure Time Series Insights överväger personliga data för att data som hör till administratörer och användare för Time Series Insights. Time Series Insights lagrar Azure Active Directory-objekt-ID för användare med åtkomst till miljön. Azure-portalen Visar användarens e-postadresser, men dessa e-postadresser lagras inte i Time Series Insights, de slås dynamiskt upp med hjälp av Azure Active Directory-objekt-ID i Azure Active Directory.

## <a name="deleting-customer-data"></a>Tar bort kunddata

En Innehavaradministratör kan ta bort kunddata i Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Innan du tar bort kunddata via portalen, bör du dock ta bort användarens åtkomstprinciper från Time Series Insights-miljön i Azure-portalen. Mer information finns i [bevilja åtkomst till en Time Series Insights-miljö med Azure-portalen](time-series-insights-data-access.md).

Du kan också utföra delete-åtgärder på principer för åtkomst med hjälp av REST-API. Mer information finns i [åtkomstprinciper - ta bort](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

Time Series Insights är integrerad med principbladet i Azure-portalen. Både Time Series Insights och principbladet låter dig visa, exportera och ta bort data som lagras i tjänsten. Någon ta bort åtgärder som vidtagits i principbladet av Azure portal resulterar i borttagningen av användardata i Time Series Insights. Till exempel om en användare har en sparad personlig fråga kan raderas frågan från Time Series Insights explorer. Om användaren har en sparad delade fråga, kvarstår frågan, men användarinformationen tas bort permanent. Följande anmärkning innehåller anvisningar om hur du utför dessa uppgifter.

## <a name="exporting-customer-data"></a>Exportera kunddata

På liknande sätt att ta bort data, kan en Innehavaradministratör visa och exportera data som lagras i Time Series Insights från bladet principen i Azure-portalen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Om du är en Innehavaradministratör kan visa du principerna för dataåtkomst i Time Series Insights-miljön i Azure-portalen. Mer information finns i [bevilja åtkomst till en Time Series Insights-miljö med Azure-portalen](time-series-insights-data-access.md).

Det är också möjligt att utföra exportåtgärder på principer för åtkomst med hjälp av ”lista efter miljö”-åtgärd i den tillhandahållna REST-API. Mer information finns i [åtkomstprinciper - lista med miljö](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Att ta bort data som lagras i Time Series Insights

Personliga data kan vara väg till Time Series Insights-lagring, ett annat scenario från användar- och administratör. Om du överväger att data som lagras i Time Series Insights som personliga data och kan du exportera och ta bort dessa data med hjälp av följande steg:

**Visa och exportera data**

Om du vill visa och exportera data som lagras i Time Series Insights, måste du söka efter dessa data. Du kan använda Time Series Insights explorer eller API: er för Time Series Insights-fråga för att visa och exportera data. Om du vill visa och exportera data med hjälp av Time Series Insights explorer måste du först söka för att hitta användardata i fråga. När du söker, högerklickar du på diagrammet och välj **utforska händelser**. Rutnätet händelser visas och anger alternativ för att exportera data som CSV och JSON.

Mer information finns i [Azure Time Series Insights explorer](time-series-insights-explorer.md).

**Ta bort data**

Time Series Insights stöder för närvarande inte detaljerade borttagning av data. Time Series Insights ger dock möjlighet att ta bort kunddata lagras i Time Series Insights genom att konfigurera principer för kvarhållning. Du kan justera kvarhållningsperioden i hela Time Series Insights-miljön till valfritt antal dagar för dina krav för borttagning.

Mer information finns i [konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).