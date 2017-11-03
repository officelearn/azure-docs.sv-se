---
title: "Säkerhet för Notification hub"
description: "Det här avsnittet beskriver security för Azure notification hubs."
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 7c3283799806135060bb8ca57ea398c93d1106bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="security"></a>Säkerhet
## <a name="overview"></a>Översikt
Det här avsnittet beskriver säkerhetsmodellen i Azure Notification Hubs. Eftersom Meddelandehubbar är en Service Bus-entitet, implementeras som i samma säkerhetsmodell som Service Bus. Mer information finns i [Service Bus autentisering](https://msdn.microsoft.com/library/azure/dn155925.aspx) avsnitt.

## <a name="shared-access-signature-security-sas"></a>Säkerhet för delad åtkomst signatur (SAS)
Notification Hubs implementerar en på entitetsnivå säkerhetsprogram kallas SAS (signatur för delad åtkomst). Det här schemat kan meddelandeentiteter att deklarera upp till 12 auktoriseringsregler i deras beskrivning som ger rättigheter på denna enhet.

Varje regel innehåller ett namn, ett nyckelvärde (delad hemlighet) och en uppsättning rättigheter, som beskrivs i avsnittet ”säkerhetsanspråk”. När du skapar en Meddelandehubb skapas två regler automatiskt: en med lyssna-behörighet (med klientappen) och en med alla rättigheter (som använder appens serverdel).

När du utför registreringen management från klientappar om informationen som skickas meddelanden är inte känslig (till exempel väder uppdateringar), ett vanligt sätt att komma åt en Meddelandehubb är att ge nyckelvärdet för regeln endast lyssna-åtkomst till klientappen och ge nyckelvärdet för regeln fullständig åtkomst till appens serverdel.

Det rekommenderas inte att bädda in värdet för nyckeln i Windows Store-appar för klienten. Ett sätt att undvika att bädda in värdet för nyckeln är att ha klientappen hämta från appens serverdel vid start.

Det är viktigt att förstå att nyckeln med lyssna åtkomst tillåter ett klientprogram att registrera dig för en tagg. Om din app måste begränsa registreringar till särskilda taggar till specifika klienter (till exempel när taggar representerar användar-ID), måste din Apps serverdel utföra registreringar. Mer information finns i hantering av registreringen. Observera att på så sätt kan klientappen inte kommer har direkt åtkomst till Notification Hubs.

## <a name="security-claims"></a>Säkerhetsmålen
Precis som andra entiteter, Notification Hub tillåts för tre säkerhetsanspråk: lyssna, skicka och hantera.

| Begär | Beskrivning | Tillåtna åtgärder |
| --- | --- | --- |
| Lyssna |Skapa eller uppdatera, läsa och ta bort enda registreringar |Skapa eller uppdatera registrering<br><br>Läs registrering<br><br>Läsa alla registreringar för en referens<br><br>Ta bort registrering |
| Skicka |Skicka meddelanden till meddelandehubben |Skicka meddelande |
| Hantera |CRUDs om Notification Hubs (inklusive uppdaterar PNS-autentiseringsuppgifter och säkerhetsnycklar) och Läs registreringar baserat på taggar |Skapa/uppdatera och läsa/ta bort notification hub<br><br>Läs registreringar efter tagg |

Notification Hubs godkänna anspråk som beviljas av Microsoft Azure Access Control-token och av signatur-token som genereras med delade nycklar direkt på Meddelandehubben.

