---
title: Kundinformation begära funktioner i Azure IoT Central | Microsoft Docs
description: Kundinformation begära funktioner i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: bb5e263b0332f957b4e7f4a928ccd53f639bcd9c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629413"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kunden datafunktioner för begäran

Azure IoT Central är en helt hanterad programvara som en tjänst lösning för Sakernas Internet (IoT) som gör det enkelt att ansluta, övervaka, hantera din IoT-tillgångar i skala, skapa djupa insikter från dina IoT-data och välgrundade åtgärda.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kundinformation

Azure Active Directory objekt-ID: N används för att identifiera användare och tilldela roller. Azure IoT centrala portal visar användaren e-postadresser för rolltilldelningar men endast Azure Active Directory-ID för objekt som lagras, e-postadressen efterfrågas dynamiskt från Azure Active Directory. Azure IoT Central administratörer kan visa, exportera och ta bort användare i avsnittet användare av en Azure IoT centralt program.

E-postadresser kan konfigureras för att ta emot aviseringar i programmet. I det här fallet lagras i IoT Central e-postadresser och måste hanteras från sidan för administration av app-konto.

Om enheter, Microsoft har ingen information och har inte åtkomst till data som gör att enheter kan användare korrelation. Många av de enheter som hanteras i Azure IoT centrala är inte personliga enheter, till exempel en Varuautomat eller kaffe maker. Kunder kan emellertid tänka vissa enheter ska vara identifierbar och deras gottfinnande behålla sina egna tillgång eller inventering spårningssystem som koppla enheter till enskilda användare. Azure IoT Central hanterar och lagrar alla data som associeras med enheter som om det vore personliga data.

När du använder Microsoft-företagstjänster genererar Microsoft viss information kallas systemgenererade loggar. Dessa loggar utgöra faktiska åtgärder i tjänsten och diagnostikdata som rör enskilda enheter och inte är relaterade till användaraktivitet. Azure IoT Central systemgenererade loggar är inte tillgänglig eller exportera av administratörer.

## <a name="deleting-customer-data"></a>Om du tar bort kundinformation

Möjligheten att ta bort användardata finns bara via sidan IoT Central administration. Administratörer kan välja vilken användare som ska tas bort och klickar på **ta bort** i det övre högra hörnet för att ta bort posten. Administratörer kan också ta bort enskilda konton som inte längre kopplat till det aktuella programmet.

När en användare har tagits bort kan inga nya aviseringar via e-post till dem. Dock måste deras e-postadress vara individuellt bort från varje konfigurerade avisering.

Mer information finns i [konfigurerar regler och åtgärder för din enhet](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Exportera kundinformation

Möjligheten att exportera data finns bara via sidan IoT Central administration. Kundinformation, inklusive tilldelade roller kan valts, kopieras och klistras in av en administratör i programmet.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Läs mer om kontot administration, inklusive rolldefinitioner, [hur man administrerar programmet](howto-administer.md).
