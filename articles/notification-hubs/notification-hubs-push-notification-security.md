---
title: Notification Hubs säkerhet
description: I det här avsnittet beskrivs säkerheten för Azure Notification Hub.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 753493100bbdb34255574656a47217560e2d321a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213053"
---
# <a name="notification-hubs-security"></a>Notification Hubs säkerhet

## <a name="overview"></a>Översikt

I det här avsnittet beskrivs säkerhets modellen för Azure Notification Hubs.

## <a name="shared-access-signature-security-sas"></a>Signatur säkerhet för delad åtkomst (SAS)

Notification Hubs implementerar ett säkerhets schema på enhets nivå som kallas SAS (signatur för delad åtkomst). Varje regel innehåller ett namn, ett nyckel värde (delad hemlighet) och en uppsättning rättigheter, enligt beskrivningen i [säkerhets anspråk](#security-claims). När du skapar en Notification Hub skapas två regler automatiskt: en med **avlyssnings** rättigheter (som klient programmet använder) och en med **alla** rättigheter (som används av appens Server del).

När du utför registrerings hantering från-klient program, om informationen som skickas via meddelanden inte är känslig (t. ex. väder uppdateringar), är ett vanligt sätt att komma åt en Notification Hub att ge nyckelvärdet för regeln lyssnings åtkomst till klient programmet. och för att ge nyckel värdet för regeln fullständig åtkomst till appens Server del.

Appar ska inte bädda in nyckelvärdet i Windows Store-klientprogram, i stället måste klient programmet hämta det från appens Server del vid start.

Med den här nyckeln med **avlyssnings** åtkomst kan en klient app registrera sig för alla Taggar. Om din app måste begränsa registreringen till vissa taggar till vissa klienter (till exempel när taggar representerar användar-ID), måste appens Server del utföra registreringarna. Mer information finns i [registrerings hantering](notification-hubs-push-notification-registration-management.md). Observera att på det här sättet kommer klient programmet inte att ha direkt åtkomst till Notification Hubs.

## <a name="security-claims"></a>Säkerhets anspråk

På samma sätt som andra entiteter tillåts Notification Hub-åtgärder för tre säkerhets anspråk: **Lyssna**, **Skicka**och **Hantera**.

| Begäran   | Beskrivning                                          | Tillåtna åtgärder |
| ------- | ---------------------------------------------------- | ------------------ |
| Lyssna  | Skapa/uppdatera, läsa och ta bort enstaka registreringar | Skapa/uppdatera registrering<br><br>Läs registrering<br><br>Läs alla registreringar för en referens<br><br>Ta bort registrering |
| Skicka    | Skicka meddelanden till Notification Hub                | Skicka meddelande |
| Hantera  | CRUDs på Notification Hubs (inklusive uppdatering av PNS-autentiseringsuppgifter och säkerhets nycklar) och Läs registreringar baserat på Taggar |Skapa/uppdatera/läsa/ta bort Notification Hub<br><br>Läs registreringar efter tagg |

Notification Hubs accepterar token för signaturer som genereras med delade nycklar som kon figurer ATS direkt i Notification Hub.

Det går inte att skicka ett meddelande till fler än ett namn område. Namn områden är logiska behållare för Notification Hub och är inte involverade för att skicka meddelanden.
Åtkomst principer för namn områdes nivå (autentiseringsuppgifter) kan användas för åtgärder på namn områdes nivå, till exempel: Visa meddelande hubbar, skapa eller ta bort Notification Hub osv. Du kan bara skicka meddelanden med åtkomst principerna på NAV-nivå.
