---
title: Lösenordskomplexitet i Azure Active Directory B2C | Microsoft Docs
description: Så här konfigurerar du komplexitetskrav för lösenord som anges av användare i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b16ac10e10655bbc7e41d9336378228097ca19ff
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014728"
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurera komplexitetskrav för lösenord

> [!NOTE]
> **Den här funktionen är i offentlig förhandsversion.**

Azure Active Directory B2C (Azure AD B2C) stöder ändring av komplexitetskrav för lösenord som tillhandahålls av en slutanvändare när du skapar ett konto.  Azure AD B2C använder som standard `Strong` lösenord.  Azure AD-B2C stöder också alternativ för att styra komplexitet och deras lösenord som kunder kan använda.

## <a name="when-password-rules-are-enforced"></a>När lösenordsregler tillämpas

Under registreringen eller återställning av lösenord, en användare måste ange ett lösenord som uppfyller reglerna komplexitet.  Lösenord tillämpas per princip.  Det är möjligt att ha en princip som kräver en 4-siffrig PIN-kod under registrering medan en annan princip kräver en åtta tecken lång sträng under registreringen.  Du kan till exempel använda en princip med olika lösenordskomplexitet för vuxna än för barn.

Lösenordskomplexitet tillämpas aldrig under inloggning.  Användare uppmanas aldrig under inloggning för att ändra sina lösenord eftersom den inte uppfyller kravet på aktuell komplexitet.

Här är typerna av principer där du kan konfigurera lösenordskomplexitet:

* Princip för registrering eller inloggning
* Principen för lösenordsåterställning
* Anpassad princip ([konfigurera lösenordskomplexitet i anpassad princip för](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Så här konfigurerar du lösenordskomplexitet

1. Öppna **principerna för registrering eller inloggning**.
2. Välj en princip och klicka på **redigera**.
3. Öppna **lösenordskomplexitet**.
4. Ändra lösenordskomplexiteten för den här principen ska **enkel**, **starka**, eller **anpassad**.

### <a name="comparison-chart"></a>Jämförelsetabell

| Komplexitet | Beskrivning |
| --- | --- |
| Enkel | Ett lösenord som är minst 8 – 64 tecken. |
| Stark | Ett lösenord som är minst 8 – 64 tecken. Det krävs 3 av 4 av gemener, versaler, siffror eller symboler. |
| Anpassat | Det här alternativet ger störst kontroll över lösenord.  Det gör att konfigurera en anpassad längd.  Det gör också accepterar endast många lösenord (PIN). |

## <a name="options-available-under-custom"></a>Alternativ som är tillgängliga under Anpassad

### <a name="character-set"></a>Teckenuppsättning

Gör att du accepterar endast siffror (PIN) eller fullständiga teckenuppsättning.

* **Nummer endast** tillåter siffror endast (0-9) när du skriver in ett lösenord.
* **Alla** tillåter alla bokstav, siffra eller symbol.

### <a name="length"></a>Längd

Låter dig styra längdkraven av lösenordet.

* **Minsta längd** måste vara minst 4.
* **Maximal längd** måste vara större eller lika med minimilängden och högst kan vara 64 tecken.

### <a name="character-classes"></a>Teckenklasser

Låter dig styra vilka olika teckenuppsättningar som används i lösenordet.

* **2 av 4: gemen, en versal bokstav, siffra (0-9), en Symbol** säkerställer lösenordet innehåller minst två teckentyper. Till exempel ett tal och en gemen.
* **3 av 4: gemen, en versal bokstav, siffra (0-9), en Symbol** säkerställer lösenordet innehåller minst två teckentyper. Till exempel ett tal, en gemen bokstav och en versal.
* **4 av 4: gemen, en versal bokstav, siffra (0-9), en Symbol** säkerställer lösenordet innehåller alla för teckentyper.

    > [!NOTE]
    > Kräver **4 av 4** kan resultera i slutanvändarens frustrationen. Vissa studier har visat att det här kravet inte förbättrar lösenord entropi. Se [NIST: S riktlinjer för lösenord](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
