---
title: Kundinformation begära funktioner i Azure tid serien insikter
description: Översikt över funktioner kundens data.
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.openlocfilehash: fbbb203d4ad0568a061043317e0444d2551d1aef
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652142"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kunden datafunktioner för begäran

Azure tid serien Insights är en hanterad molntjänst med lagring, analys och visualisering komponenter som gör det lättare att mata in, lagra, utforska och analysera händelser miljarder samtidigt.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Om du vill visa, exportera och ta bort personliga data som kan vara föremål för en begäran om data ämne, kan en Innehavaradministratör med Azure tid serien Insights använda Azure portal eller REST-API: er. Med hjälp av Azure portal och tjänstbegäranden data ämne, ger en mindre komplex metod för att utföra dessa åtgärder som de flesta användare föredrar.

## <a name="identifying-customer-data"></a>Identifiera kundinformation

Azure tid serien insikter anser personuppgifter till att data som är associerade med administratörer och användare över tid serien insikter. Tid serien insikter lagrar Azure Active Directory-objekt-ID för användare med åtkomst till miljön. Azure-portalen Visar användarens e-postadresser, men dessa e-postadresser lagras inte i tid serien insikter, de slås dynamiskt upp med hjälp av Azure Active Directory-objekt-ID i Azure Active Directory.

## <a name="deleting-customer-data"></a>Om du tar bort kundinformation

En Innehavaradministratör kan ta bort kundens data med Azure-portalen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Innan du tar bort kundinformation via portalen ska du dock ta bort användarens åtkomstprinciper från tid serien insikter miljön i Azure-portalen. Mer information finns i [bevilja åtkomst till data till en tid serien insikter med hjälp av Azure portal](time-series-insights-data-access.md).

Du kan också utföra delete-åtgärder på principer för åtkomst med hjälp av REST-API. Mer information finns i [åtkomstprinciper - ta bort](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

Tid serien insikter är integrerad med principbladet i Azure-portalen. Både tid serien insikter och principbladet kan du visa, exportera och ta bort data som lagras i tjänsten. Någon ta bort åtgärd i principbladet av Azure portal resultaten i borttagningen av användardata i tid serien insikter. Till exempel om en användare har en sparad personliga fråga, är frågan bort permanent från Utforskaren tid serien insikter. Om användaren har en delad fråga, kvarstår frågan, men användarinformationen bort permanent. Följande anmärkning innehåller instruktioner om hur du utför dessa uppgifter.

## <a name="exporting-customer-data"></a>Exportera kundinformation

På liknande sätt att ta bort data, kan en Innehavaradministratör visa och exportera data som lagras i tid serien insikter från bladet med säkerhetsprinciper i Azure-portalen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Om du är en Innehavaradministratör kan visa du principerna dataåtkomst i tid serien insikter miljön i Azure-portalen. Mer information finns i [bevilja åtkomst till data till en tid serien insikter med hjälp av Azure portal](time-series-insights-data-access.md).

Det är också möjligt att utföra exportåtgärder på principer för åtkomst med hjälp av ”lista av miljö”-åtgärd i det angivna REST-API. Mer information finns i [åtkomstprinciper - lista av miljön](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Ta bort data som lagras i tid serien insikter

Personuppgifter kan göra väg tid serien insikter lagring, ett annat scenario från användar- och admin-data. Om du anser att data som lagras i tid serien insikter som personliga data som kan du exportera och ta bort dessa data med hjälp av följande steg:

**Visa och exportera data**

Om du vill visa och exportera data som lagras i tid serien insikter, måste du söka efter data. Du kan använda den tid serien insikter explorer eller tid serien insikter fråga API: er för att visa och exportera data. Om du vill visa och exportera data med hjälp av Utforskaren tid serien insikter du först söka efter användardata i fråga. När du söker efter, högerklickar du på diagrammet och välj **utforska händelser**. Händelser rutnätet visas och anger alternativ för att exportera data som CSV och JSON.

Mer information finns i [Azure tid serien Insights explorer](time-series-insights-explorer.md).

**Ta bort data**

Tid serien insikter stöder för närvarande inte detaljerade borttagning av data. Dock ger tid serien insikter möjlighet att ta bort kundinformation som lagras i tid serien insikter genom att konfigurera bevarandeprinciper. Du kan justera kvarhållningsperioden för hela tiden serien insikter miljön till valfritt antal dagar som ska tas bort krav.

Mer information finns i [konfigurera bevaring i tid serien insikter](time-series-insights-how-to-configure-retention.md).