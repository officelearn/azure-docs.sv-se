---
title: Konfigurera krav på lösenordskomplexitet
titleSuffix: Azure AD B2C
description: Konfigurera komplexitetskrav för lösenord som levereras av konsumenter i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5ef550af0c7e19531ea19093ea937880f7dcf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185649"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurera komplexitetskrav för lösenord i Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) stöder ändring av komplexitetskraven för lösenord som tillhandahålls av en slutanvändare när ett konto skapas. Som standard använder `Strong` Azure AD B2C lösenord. Azure AD B2C stöder också konfigurationsalternativ för att styra komplexiteten i lösenord som kunder kan använda.

## <a name="password-rule-enforcement"></a>Tvingande lösenordsregel

Under registrering eller återställning av lösenord måste en slutanvändare ange ett lösenord som uppfyller komplexitetsreglerna. Regler för lösenordskomplexitet tillämpas per användarflöde. Det är möjligt att ha ett användarflöde kräver en fyrsiffrig pin under registreringen medan ett annat användarflöde kräver en åtta teckensträng under registreringen. Du kan till exempel använda ett användarflöde med annan lösenordskomplexitet för vuxna än för barn.

Lösenordskomplexitet tillämpas aldrig under inloggningen. Användare uppmanas aldrig under inloggningen att ändra sitt lösenord eftersom det inte uppfyller det aktuella komplexitetskravet.

Lösenordskomplexitet kan konfigureras i följande typer av användarflöden:

- Användarflöde för registrering eller inloggning
- Användarflöde för återställning av lösenord

Om du använder anpassade principer kan du[(konfigurera lösenordskomplexitet i en anpassad princip).](custom-policy-password-complexity.md)

## <a name="configure-password-complexity"></a>Konfigurera lösenordskomplexitet

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
3. Sök efter och välj **Azure AD B2C**i Azure-portalen .
4. Välj **Användarflöden (principer)**.
2. Markera ett användarflöde och klicka på **Egenskaper**.
3. Under **Lösenordskomplexitet**ändrar du lösenordskomplexiteten för det här användarflödet till **Enkel,** **Stark**eller **Anpassad**.

### <a name="comparison-chart"></a>Jämförelsediagram

| Komplexitet | Beskrivning |
| --- | --- |
| Enkel | Ett lösenord som är minst 8 till 64 tecken. |
| Stark | Ett lösenord som är minst 8 till 64 tecken. Det kräver 3 av 4 gemener, versaler, siffror eller symboler. |
| Anpassat | Det här alternativet ger mest kontroll över lösenordskomplexitetsregler.  Det gör det möjligt att konfigurera en anpassad längd.  Det gör det också möjligt att acceptera lösenord endast för nummer (stift). |

## <a name="custom-options"></a>Anpassade alternativ

### <a name="character-set"></a>Teckenuppsättning

Gör att du bara kan acceptera siffror (stift) eller den fullständiga teckenuppsättningen.

- **Siffror** tillåter bara siffror (0-9) när du anger ett lösenord.
- **Alla** tillåter alla bokstav, nummer eller symboler.

### <a name="length"></a>Längd

Gör att du kan styra längden på lösenordet.

- **Minsta längd** måste vara minst 4.
- **Maximal längd** måste vara större eller lika med minsta längd och som mest kan vara 64 tecken.

### <a name="character-classes"></a>Teckenklasser

Gör att du kan styra de olika teckentyper som används i lösenordet.

- **2 av 4: Gemener, Versaler, Tal (0-9), Symbol** säkerställer att lösenordet innehåller minst två teckentyper. Till exempel ett tal och ett gemener.
- **3 av 4: Gemener, Versaler, Tal (0-9), Symbol** säkerställer att lösenordet innehåller minst tre teckentyper. Till exempel ett tal, ett gemener och ett versaler.
- **4 av 4: Gemener, Versaler, Tal (0-9), Symbol** säkerställer att lösenordet innehåller allt för teckentyper.

    > [!NOTE]
    > Kräver **4 av 4** kan resultera i slutanvändarens frustration. Vissa studier har visat att detta krav inte förbättrar lösenord entropy. Se [riktlinjer för NIST-lösenord](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
