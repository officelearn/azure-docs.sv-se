---
title: ta med fil
description: ta med fil
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694473"
---
## <a name="lifecycle"></a>Livscykel

På fliken **Livscykel** anger du när en användares tilldelning till åtkomstpaketet upphör att gälla. Du kan också ange om användare kan utöka sina tilldelningar.

1. I avsnittet **Förfallodatum** **anges åtkomstpakettilldelningar som upphör** att gälla vid **datum**, **Antal dagar**eller **Aldrig**.

    För **På datum**väljer du ett utgångsdatum i framtiden.

    För **antal dagar**anger du ett tal mellan 0 och 3660 dagar.

    Baserat på ditt val upphör en användares tilldelning till åtkomstpaketet att gälla ett visst datum, ett visst antal dagar efter att de har godkänts eller aldrig.

1. Klicka på **Visa avancerade förfalloinställningar** om du vill visa ytterligare inställningar.

    ![Åtkomstpaket - Inställningar för förfallodatum för livscykel](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Om du vill att användaren ska kunna utöka sina tilldelningar anger du **Tillåt användare att utöka åtkomsten** till **Ja**.

    Om tillägg tillåts i principen får användaren ett e-postmeddelande 14 dagar och även 1 dag innan deras åtkomstpakettilldelning har förfallit och uppmanar dem att förlänga tilldelningen. Om användaren skickar en begäran om att utöka åtkomsten måste tilläggsdatumet vara på eller innan tilldelningar upphör att gälla enligt definitionen i principen som användes för att ge användaren åtkomst till åtkomstpaketet. Om principen till exempel anger att tilldelningar ska upphöra att gälla den 30 juni, är det maximala tillägg som en användare kan begära den 30 juni.

    Om en användares åtkomst utökas kan han eller hon inte begära åtkomstpaketet efter det angivna förlängningsdatumet (datum som angetts i tidszonen för den användare som skapade principen).

1. Om du vill kräva godkännande för att bevilja ett tillägg anger du **Kräv godkännande för att bevilja tillägget** till **Ja**.

    Samma godkännandeinställningar som angavs på fliken **Begäran kommer** att användas.

1. Klicka på **Nästa** eller **Uppdatera**.
