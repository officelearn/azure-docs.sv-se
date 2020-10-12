---
title: inkludera fil
description: inkludera fil
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694473"
---
## <a name="lifecycle"></a>Livscykel

På fliken **livs cykel** anger du när en användares tilldelning till åtkomst paketet upphör att gälla. Du kan också ange om användarna ska kunna utöka sina tilldelningar.

1. I avsnittet **förfallo** datum går det att ange **åtkomst paket tilldelningar förfaller** till **på datum**, **antal dagar**eller **aldrig**.

    För **på datum**väljer du ett förfallo datum i framtiden.

    Ange ett tal mellan 0 och 3660 dagar i **antal dagar**.

    Baserat på ditt val förfaller en användares tilldelning till åtkomst paketet ett visst datum, ett visst antal dagar efter att de har godkänts eller aldrig.

1. Klicka på **Visa avancerade inställningar för förfallo datum** för att visa ytterligare inställningar.

    ![Åtkomst paket – livs cykelns förfallo inställningar](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Om du vill tillåta att användare utökar sina tilldelningar ställer du in **Tillåt att användare utökar åtkomsten** till **Ja**.

    Om tillägg är tillåtna i principen får användaren ett e-postmeddelande 14 dagar och även en dag innan tilldelningen av åtkomst paket är inställd på att förfalla och uppmanas att förlänga tilldelningen. Om användaren skickar en begäran om att utöka åtkomsten måste förlängnings datumet vara i eller innan tilldelningarna upphör att gälla enligt principen som användes för att ge användaren åtkomst till åtkomst paketet. Om principen till exempel anger att tilldelningar är inställda på att upphöra att gälla den 30 juni, är det maximala tillägg som en användare kan begära den 30 juni.

    Om en användares åtkomst är utökad kommer de inte att kunna begära åtkomst paketet efter angivet tilläggs datum (datum angivet i tids zonen för den användare som skapade principen).

1. Kräv godkännande för att bevilja **tillägg** till **Ja**för att kräva att ett tillägg beviljas.

    Samma godkännande inställningar som angavs på fliken **förfrågningar** kommer att användas.

1. Klicka på **Nästa** eller **Uppdatera**.
