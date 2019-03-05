---
title: Kundinformation begär funktioner i Azure IoT Central | Microsoft Docs
description: Kundinformation begär funktioner i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 2952008ca788a620f2b558d5997aeebd59196b7a
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314576"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av kundens datafunktioner

Azure IoT Central är en fullständigt hanterad programvara-som-tjänst-lösning för Internet of Things (IoT) som gör det enkelt att ansluta, övervaka, och hantera IoT-tillgångar i skala, skapa djupa insikter från dina IoT-data och fatta välgrundade beslut.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kunddata

Azure Active Directory objekt-ID: N används för att identifiera användare och tilldela roller. Azure IoT Central portal Visar användarens e-postadresser för rolltilldelningar men endast i Azure Active Directory-objekt-ID lagras, e-postadressen efterfrågas dynamiskt från Azure Active Directory. Azure IoT Central-administratörer kan visa, exportera och ta bort användare i avsnittet användare administration av ett program med Azure IoT Central.

I programmet, kan e-postadresser konfigureras för att ta emot aviseringar. I det här fallet e-postadresser lagras i IoT Central och måste hanteras från sidan för administration av app-konto.

Om enheter, Microsoft har ingen information och har ingen åtkomst till data som gör användaren korrelation enheten. Många av de enheter som hanteras i Azure IoT Central är inte personliga enheter, till exempel en Varuautomat eller kaffe maker. Kunder kan dock tänka på vissa enheter är personligt identifierbar och deras godtycke kan underhålla sina egna tillgång eller inventeringssamling system som koppla enheter till enskilda användare. Azure IoT Central hanterar och lagrar alla data kopplade till enheter som om det vore personliga data.

När du använder Microsoft-företagstjänster, genererar Microsoft viss information som kallas systemgenererade loggar. Dessa loggar utgör faktiska åtgärder i tjänsten och diagnostiska data som är relaterade till enskilda enheter och inte är relaterade till användaraktivitet. Azure IoT Central systemgenererade loggar är inte tillgänglig eller exportera av administratörer.

## <a name="deleting-customer-data"></a>Tar bort kunddata

Möjligheten att ta bort användardata är endast tillgängligt via sidan IoT Central administration. Administratörer kan välja vilken användare som ska tas bort och välj **ta bort** i det övre högra hörnet av programmet för att ta bort posten. Administratörer kan också ta bort enskilda konton som inte längre kopplad till programmet i fråga.

När en användare har tagits bort, inga nya aviseringar via e-post till dem. Dock måste sin e-postadress vara individuellt bort från varje konfigurerade avisering.

Mer information finns i [konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Exportera kunddata

Möjligheten att exportera data är endast tillgängligt via sidan IoT Central administration. Kundinformation, inklusive tilldelade roller kan valts, kopieras och klistras in av en administratör i programmet.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Läs mer om administrationen, inklusive rolldefinitioner, [hur du administrerar programmets](howto-administer.md).
