---
title: Lösenordskomplexitet - Azure Active Directory B2C | Microsoft Docs
description: Så här konfigurerar du komplexitetskrav för lösenord som anges av användare i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5c47a22883de4a3b28a42b390ef78368277e22be
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703747"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurera komplexitetskrav för lösenord i Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C stöder ändra komplexitetskrav för lösenord som tillhandahålls av en slutanvändare när du skapar ett konto. Azure AD B2C använder som standard `Strong` lösenord. Azure AD-B2C stöder också alternativ för att styra komplexitet och deras lösenord som kunder kan använda.

## <a name="password-rule-enforcement"></a>Regelverkställning för lösenord

Under registreringen eller återställning av lösenord, en användare måste ange ett lösenord som uppfyller reglerna komplexitet. Lösenord tillämpas per användarflödet. Det är möjligt att ha en policy som kräver en 4-siffrig PIN-kod under registrering medan en annan användarflödet kräver en åtta tecken lång sträng under registreringen. Du kan till exempel använda ett användarflöde med olika lösenordskomplexitet för vuxna än för barn.

Lösenordskomplexitet tillämpas aldrig under inloggning. Användare uppmanas aldrig under inloggning för att ändra sina lösenord eftersom den inte uppfyller kravet på aktuell komplexitet.

Lösenordskomplexitet kan konfigureras i följande typer av användarflöden:

- Registrering eller inloggning användarflödet
- Användarflödet med återställning av lösenord

Om du använder anpassade principer, kan du ([konfigurera lösenordskomplexitet i en anpassad princip](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="configure-password-complexity"></a>Konfigurera lösenordskomplexitet

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **användarflöden**.
2. Välj ett användarflöde klicka sedan på **egenskaper**.
3. Under **lösenordskomplexitet**, ändra lösenordskomplexiteten för det här användarflödet till **enkel**, **starka**, eller **anpassad**.

### <a name="comparison-chart"></a>Jämförelsetabell

| Komplexitet | Beskrivning |
| --- | --- |
| Enkel | Ett lösenord som är minst 8 – 64 tecken. |
| Stark | Ett lösenord som är minst 8 – 64 tecken. Det krävs 3 av 4 av gemener, versaler, siffror eller symboler. |
| Anpassat | Det här alternativet ger störst kontroll över lösenord.  Det gör att konfigurera en anpassad längd.  Det gör också accepterar endast många lösenord (PIN). |

## <a name="custom-options"></a>Anpassade alternativ

### <a name="character-set"></a>Teckenuppsättning

Gör att du accepterar endast siffror (PIN) eller fullständiga teckenuppsättning.

- **Nummer endast** tillåter siffror endast (0-9) när du skriver in ett lösenord.
- **Alla** tillåter alla bokstav, siffra eller symbol.

### <a name="length"></a>Längd

Låter dig styra längdkraven av lösenordet.

- **Minsta längd** måste vara minst 4.
- **Maximal längd** måste vara större eller lika med minimilängden och högst kan vara 64 tecken.

### <a name="character-classes"></a>Teckenklasser

Låter dig styra vilka olika teckenuppsättningar som används i lösenordet.

- **2 av 4: Gemen, en versal bokstav, siffra (0-9), en Symbol** säkerställer lösenordet innehåller minst två teckentyper. Till exempel ett tal och en gemen.
- **3 av 4: Gemen, en versal bokstav, siffra (0-9), en Symbol** säkerställer lösenordet innehåller minst två teckentyper. Till exempel ett tal, en gemen bokstav och en versal.
- **4 av 4: Gemen, en versal bokstav, siffra (0-9), en Symbol** säkerställer lösenordet innehåller alla för teckentyper.

    > [!NOTE]
    > Kräver **4 av 4** kan resultera i slutanvändarens frustrationen. Vissa studier har visat att det här kravet inte förbättrar lösenord entropi. Se [NIST: S riktlinjer för lösenord](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
