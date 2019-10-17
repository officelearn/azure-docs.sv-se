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
ms.openlocfilehash: 88e11adadcc06875964146ad2046828267258038
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389231"
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

    Om tillägg är tillåtna i principen får användaren ett e-postmeddelande 14 dagar och även en dag innan tilldelningen av åtkomst paket är inställd på att förfalla och uppmanas att förlänga tilldelningen.

    Om en användares åtkomst är utökad kommer de inte att kunna begära åtkomst paketet efter angivet tilläggs datum (datum angivet i tids zonen för den användare som skapade principen).

1. Kräv godkännande för att bevilja **tillägg** till **Ja**för att kräva att ett tillägg beviljas.

    Samma godkännande inställningar som angavs på fliken **förfrågningar** kommer att användas.

1. Klicka på **Nästa** eller **Uppdatera**.
