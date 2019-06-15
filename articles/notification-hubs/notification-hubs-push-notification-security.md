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
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 3f5b23028094b545262e9c01640890f2c0b989ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431248"
---
# <a name="notification-hubs-security"></a>Notification Hubs säkerhet

## <a name="overview"></a>Översikt

Det här avsnittet beskriver säkerhetsmodellen i Azure Notification Hubs.

## <a name="shared-access-signature-security-sas"></a>Säkerhet över signatur för delad åtkomst (SAS)

Notification Hubs implementerar en på entitetsnivå säkerhetsschema kallas SAS (signatur för delad åtkomst). Det här schemat kan meddelandeentiteter deklarera upp till 12 auktoriseringsregler i deras beskrivning som beviljas rättigheter på denna entitet.

Varje regel innehåller ett namn, ett nyckelvärde (delad hemlighet) och en uppsättning rättigheter, som beskrivs i [säkerhetsanspråk](#security-claims). När du skapar en Meddelandehubb, två regler skapas automatiskt: en med **lyssna** rättigheter (kamerans klienten) och en med **alla** rättigheter (som använder app-serverdel).

När du utför registreringshantering från klient apps om informationen som skickas meddelanden är inte känsliga (t.ex, väder uppdateringar), ett vanligt sätt att få åtkomst till en Meddelandehubb är att ge nyckelvärdet för regeln endast lyssna till klientapp och för att ge nyckelvärdet för regeln fullständig åtkomst till app-serverdel.

Appar bör inte bädda in nyckelvärdet i Windows Store-klientprogram, i stället har klientappen hämtar du den från appserverdelen vid start.

Nyckeln med **lyssna** åtkomst kan ett klientprogram att registrera dig för en tagg. Om din app måste begränsa registreringar till särskilda taggar för specifika klienter (till exempel när taggar representerar användar-ID), måste appens serverdel utföra registreringarna. Mer information finns i [Registreringshantering](notification-hubs-push-notification-registration-management.md). Observera att på så sätt kan klientappen inte kommer ha direkt åtkomst till Notification Hubs.

## <a name="security-claims"></a>Säkerhetsanspråk

Precis som andra entiteter, Notification Hub åtgärder tillåts för tre säkerhetsanspråk: **Lyssna**, **skicka**, och **hantera**.

| Begäran   | Beskrivning                                          | Tillåtna åtgärder |
| ------- | ---------------------------------------------------- | ------------------ |
| Lyssna  | Skapa/uppdatera, läsa och radera enda registreringar | Skapa/uppdatera registrering<br><br>Läsa registrering<br><br>Läsa alla registreringar för en referens<br><br>Ta bort registrering |
| Skicka    | Skicka meddelanden till meddelandehubben                | Skicka meddelande |
| Hantera  | CRUDs om Meddelandehubbar (inklusive uppdaterar PNS-autentiseringsuppgifter och säkerhetsnycklar) och Läs registreringar baserat på taggar |Skapa/uppdatera/läsa/ta bort notification hub<br><br>Läs registreringar efter tagg |

Notification Hubs accepterar signatur token genererades med delade nycklar inställt direkt på Meddelandehubben.

Det går inte att skicka ett meddelande till mer än ett namnområde. Namnområden är en logisk behållare för meddelandehubbar och ingår inte skicka meddelanden.
Namnområdesnivå åtkomstprinciper (autentiseringsuppgifter) kan användas för namnområdesnivån åtgärder, till exempel: visa en lista över meddelandehubbar, skapa eller ta bort notification Hub, osv. Endast åtkomstprinciper hub på servernivå skulle kan du skicka meddelanden.
