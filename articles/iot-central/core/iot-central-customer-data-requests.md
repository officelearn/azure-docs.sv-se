---
title: Funktioner för kunddatabegärande i Azure IoT Central | Microsoft-dokument
description: I den här artikeln beskrivs hur du identifierar, tar bort och exporterar kunddata i Azure IoT Central-programmet.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3cf88b4d3c4c74493235d2997db4d464bb055b81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023760"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av funktionerna för kunddatabegäran

Azure IoT Central är en fullständigt hanterad IoT-lösning (Internet of Things) som gör det enkelt att ansluta, övervaka och hantera dina IoT-tillgångar i stor skala, skapa djupa insikter från dina IoT-data och vidta välgrundade åtgärder.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kunddata

Azure Active Directory Object-ID:er används för att identifiera användare och tilldela roller. Azure IoT Central-portalen visar e-postadresser för användare för rolltilldelningar, men endast Azure Active Directory Object-ID lagras, e-postadressen efterfrågas dynamiskt från Azure Active Directory. Azure IoT Central-administratörer kan visa, exportera och ta bort programanvändare i avsnittet användaradministration i ett Azure IoT Central-program.

I programmet kan e-postadresser konfigureras för att ta emot aviseringar. I det här fallet lagras e-postadresser inom IoT Central och måste hanteras från sidan administration av konton i appen.

När det gäller enheter har Microsoft ingen information och har ingen åtkomst till data som gör det möjligt för enheten att göra med användarkorrelation. Många av de enheter som hanteras i Azure IoT Central är inte personliga enheter, till exempel en varuautomat eller kaffebryggare. Kunder kan dock betrakta vissa enheter som personligt identifierbara och efter eget gottfinnande kan behålla sina egna tillgångs- eller lagerspårningssystem som binder enheter till individer. Azure IoT Central hanterar och lagrar alla data som är associerade med enheter som om det vore personuppgifter.

När du använder Microsofts företagstjänster genererar Microsoft viss information, så kallade systemgenererade loggar. Dessa loggar utgör faktiska åtgärder som utförs inom tjänsten och diagnostiska data relaterade till enskilda enheter, och är inte relaterade till användaraktivitet. Azure IoT Central systemgenererade loggar är inte tillgängliga eller kan exporteras av programadministratörer.

## <a name="deleting-customer-data"></a>Ta bort kunddata

Möjligheten att ta bort användardata tillhandahålls endast via administrationssidan för IoT Central. Programadministratörer kan välja vilken användare som ska tas bort och välja **Ta bort** i det övre högra hörnet i programmet för att ta bort posten. Programadministratörer kan också ta bort enskilda konton som inte längre är associerade med programmet i fråga.

När en användare har tagits bort skickas inga ytterligare aviseringar till dem via e-post. Deras e-postadress måste dock tas bort individuellt från varje konfigurerad avisering.

## <a name="exporting-customer-data"></a>Exportera kunddata

Möjligheten att exportera data tillhandahålls endast via administrationssidan för IoT Central. Kunddata, inklusive tilldelade roller, kan väljas, kopieras och klistras in av en programadministratör.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Mer information om kontoadministration, inklusive rolldefinitioner, finns i Så här [administrerar](howto-administer.md)du programmet .
