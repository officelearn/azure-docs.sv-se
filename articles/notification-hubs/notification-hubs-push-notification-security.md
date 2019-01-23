---
title: Notification Hubs säkerhet
description: Det här avsnittet beskriver säkerhet för Azure notification hub.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bd9df12cbe941b868c769daccd02c1d81b39f7bd
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465368"
---
# <a name="security-model-of-azure-notification-hubs"></a>Säkerhetsmodellen i Azure Notification Hubs

## <a name="overview"></a>Översikt

Det här avsnittet beskriver säkerhetsmodellen i Azure Notification Hubs. Eftersom Meddelandehubbar är en Service Bus-entiteten, implementera de samma säkerhetsmodell som Service Bus. Mer information finns i den [Service Bus-autentisering](https://msdn.microsoft.com/library/azure/dn155925.aspx) ämnen.

## <a name="shared-access-signature-security-sas"></a>Säkerhet över signatur för delad åtkomst (SAS)

Notification Hubs implementerar en på entitetsnivå säkerhetsschema kallas SAS (signatur för delad åtkomst). Det här schemat kan meddelandeentiteter deklarera upp till 12 auktoriseringsregler i deras beskrivning som beviljas rättigheter på denna entitet.

Varje regel innehåller ett namn, ett nyckelvärde (delad hemlighet) och en uppsättning rättigheter, enligt beskrivningen i avsnittet ”säkerhetsanspråk”. När du skapar en Meddelandehubb, två regler skapas automatiskt: en med behörighet att lyssna (kamerans klienten) och en med alla rättigheter (som appserverdelen använder).

När du utför registreringshantering från klient apps om informationen som skickas meddelanden är inte känsliga (t.ex, väder uppdateringar), ett vanligt sätt att få åtkomst till en Meddelandehubb är att ge nyckelvärdet för regeln endast lyssna till klientapp och för att ge nyckelvärdet för regeln fullständig åtkomst till app-serverdel.

Vi rekommenderar inte att du bäddar in nyckelvärdet i Windows Store-klientappar. Ett sätt att undvika att bädda in nyckelvärdet är att ha klientappen hämtar du den från appserverdelen vid start.

Det är viktigt att förstå att nyckeln med lyssna-åtkomst tillåter ett klientprogram att registrera dig för en tagg. Om din app måste begränsa registreringar till särskilda taggar för specifika klienter (till exempel när taggar representerar användar-ID), måste appens serverdel utföra registreringarna. Mer information finns i Registreringshantering. Observera att på så sätt kan klientappen inte kommer ha direkt åtkomst till Notification Hubs.

## <a name="security-claims"></a>Säkerhetsanspråk

Precis som andra entiteter, Notification Hub åtgärder tillåts för tre säkerhetsanspråk: Lyssna, skicka och hantera.

| Begäran   | Beskrivning                                          | Tillåtna åtgärder |
| ------- | ---------------------------------------------------- | ------------------ |
| Lyssna  | Skapa/uppdatera, läsa och radera enda registreringar | Skapa/uppdatera registrering<br><br>Läsa registrering<br><br>Läsa alla registreringar för en referens<br><br>Ta bort registrering |
| Skicka    | Skicka meddelanden till meddelandehubben                | Skicka meddelande |
| Hantera  | CRUDs om Meddelandehubbar (inklusive uppdaterar PNS-autentiseringsuppgifter och säkerhetsnycklar) och Läs registreringar baserat på taggar |Skapa/uppdatera/läsa/ta bort notification hub<br><br>Läs registreringar efter tagg |

Meddelandehubbar accepterar anspråk som beviljas genom Microsoft Azure Access Control-token och signaturtoken som genereras med delade nycklar inställt direkt på Meddelandehubben.
