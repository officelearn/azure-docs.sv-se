---
title: "Lösenordskomplexitet - Azure AD B2C | Microsoft Docs"
description: "Konfigurera komplexitetskrav för lösenord som angavs av konsumenten i Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 3906c9fa1def206a8f0a7e155949097242728c2f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurera komplexitetskrav för lösenord

> [!NOTE]
> **Den här funktionen är i förhandsgranskningen.**  Kontakta [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com) ha din testklienten aktiverad med den här funktionen.  Testa detta inte på klienter för produktion.

Azure Active Directory B2C (Azure AD B2C) stöder ändring av komplexitetskrav för lösenord som tillhandahålls av en slutanvändare när du skapar ett konto.  Som standard använder Azure AD B2C `Strong` lösenord.  Azure AD B2C stöder även konfigurationsalternativ för att styra komplexitet för lösenord som kunder kan använda.

## <a name="when-password-rules-are-enforced"></a>När lösenordsregler tillämpas

Under registreringen eller återställning av lösenord, en användare måste ange ett lösenord som uppfyller reglerna komplexitet.  Reglerna om komplexa lösenord tillämpas per princip.  Det är möjligt att en princip som kräver en fyrsiffrig PIN-kod under registreringen medan en annan princip kräver en åtta tecken lång sträng under registreringen.  Du kan till exempel använda en princip med olika lösenordskomplexitet för vuxna än för barn.

Lösenordskomplexitet tillämpas aldrig under inloggning.  Användarna uppmanas aldrig under inloggning för att ändra sina lösenord eftersom den inte uppfyller kravet på aktuell komplexitet.

Här följer typerna av principer där lösenordskomplexitet kan konfigureras:

* Princip för registrering eller inloggning
* Principen för lösenordsåterställning
* Anpassad princip ([konfigurera lösenordskomplexitet i anpassad princip för](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Så här konfigurerar du lösenordskomplexitet

1. Följ dessa steg för att [navigera till Azure AD B2C inställningar](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Öppna **principer för registrering eller inloggning**.
1. Välj en princip och klicka på **redigera**.
1. Öppna **lösenordskomplexitet**.
1. Ändra lösenordskomplexitet för den här principen ska **enkel**, **starka**, eller **anpassad**.

### <a name="comparison-chart"></a>Jämförelsediagram

| Komplexitet | Beskrivning |
| --- | --- |
| Enkel | Ett lösenord som är minst 8 till 64 tecken. |
| Stark | Ett lösenord som är minst 8 till 64 tecken. Det krävs 3 av 4 i gemener, versaler, siffror eller symboler. |
| Anpassat | Det här alternativet ger de flesta kontroll över reglerna om komplexa lösenord.  Det gör att konfigurera en anpassad längd.  Det gör också att acceptera lösenord endast nummer (PIN). |

## <a name="options-available-under-custom"></a>Tillgängliga alternativ under Anpassad

### <a name="character-set"></a>Teckenuppsättning

Gör att du accepterar endast siffror (PIN) eller hela teckenuppsättningen.

* **Endast siffror** tillåter siffror endast (0-9) när du skriver in ett lösenord.
* **Alla** kan en bokstav, siffra eller symbol.

### <a name="length"></a>Längd

Kan du styra längdkraven av lösenordet.

* **Minsta längd** måste vara minst 4.
* **Maximal längd** måste vara större eller lika med minsta längd och högst kan vara 64 tecken.

### <a name="character-classes"></a>Teckenklasserna

Kan du kontrollera vilka olika teckenuppsättningar som används i lösenordet.

* **2 av 4: gemen, en versal bokstav, siffra (0-9), Symbol** garanterar lösenordet innehåller minst två teckentyper. En siffra och en gemen bokstav.
* **3 av 4: gemen, en versal bokstav, siffra (0-9), Symbol** garanterar lösenordet innehåller minst två teckentyper. Till exempel en siffra, en gemen bokstav och en versal.
* **4 av 4: gemen, en versal bokstav, siffra (0-9), Symbol** garanterar lösenordet innehåller alla för teckentyper.

    > [!NOTE]
    > Kräver **4 av 4** kan resultera i slutanvändarens genomförande. Vissa studier har visat att det här kravet inte förbättra lösenord entropi. Se [NIST riktlinjer för lösenord](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
