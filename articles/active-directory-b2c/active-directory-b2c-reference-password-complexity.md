---
title: Konfigurera krav för lösen ords komplexitet
titleSuffix: Azure AD B2C
description: Hur du konfigurerar komplexitets kraven för lösen ord som tillhandahålls av användare i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d37d1ae3871e8b14a44540883b1d03c29b58d27e
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950569"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurera komplexitets krav för lösen ord i Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) stöder ändring av komplexitets kraven för lösen ord som tillhandahålls av en slutanvändare när ett konto skapas. Som standard använder Azure AD B2C `Strong` lösen ord. Azure AD B2C också stöd för konfigurations alternativ för att kontrol lera komplexiteten för lösen ord som kunder kan använda.

## <a name="password-rule-enforcement"></a>Tvingad lösen ords regel

Vid registrering eller återställning av lösen ord måste användaren ange ett lösen ord som uppfyller komplexitets reglerna. Reglerna för komplexitet av lösen ord upprätthålls per användar flöde. Det är möjligt att ett användar flöde kräver en fyrsiffrig PIN-kod under registreringen medan ett annat användar flöde kräver en sträng med åtta tecken under registreringen. Du kan till exempel använda ett användar flöde med annan lösen ords komplexitet för vuxna än för barn.

Lösen ords komplexitet tillämpas aldrig under inloggningen. Användare tillfrågas aldrig under inloggningen för att ändra sina lösen ord eftersom det inte uppfyller det aktuella komplexitets kravet.

Lösen ords komplexitet kan konfigureras i följande typer av användar flöden:

- Användar flöde för registrering eller inloggning
- Användar flöde för återställning av lösen ord

Om du använder anpassade principer kan du ([Konfigurera lösen ords komplexitet i en anpassad princip](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="configure-password-complexity"></a>Konfigurera lösen ords komplexitet

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **användar flöden**.
2. Välj ett användar flöde och klicka på **Egenskaper**.
3. Under **lösen ords komplexitet**ändrar du lösen ords komplexiteten för det här användar flödet till **enkel**, **stark**eller **anpassad**.

### <a name="comparison-chart"></a>Jämförelse diagram

| Komplexitet | Beskrivning |
| --- | --- |
| Enkelt | Ett lösen ord som består av minst 8 till 64 tecken. |
| Stark | Ett lösen ord som består av minst 8 till 64 tecken. Det kräver 3 av 4 av gemener, versaler, siffror eller symboler. |
| Anpassat | Det här alternativet ger störst kontroll över regler för lösen ords komplexitet.  Det gör det möjligt att konfigurera en anpassad längd.  Det tillåter också att du godkänner endast lösen ord (PIN-kod). |

## <a name="custom-options"></a>Anpassade alternativ

### <a name="character-set"></a>Teckenuppsättning

Du kan bara acceptera siffror (PIN-nummer) eller den fullständiga teckenuppsättningen.

- **Siffror endast** tillåter siffror (0-9) när du anger ett lösen ord.
- **Alla** tillåter bokstäver, siffror och symboler.

### <a name="length"></a>Längd

Gör att du kan kontrol lera lösen ordets längd krav.

- Den **minsta längden** måste vara minst 4.
- Den **maximala längden** måste vara större än eller lika med minimilängd och får innehålla högst 64 tecken.

### <a name="character-classes"></a>Character-klasser

Gör att du kan kontrol lera de olika teckenuppsättningarna som används i lösen ordet.

- **2 av 4: gemener, versaler, tal (0-9), symbol** ser till att lösen ordet innehåller minst två tecken typer. Till exempel ett tal och ett gemener.
- **3 av 4: gemener, versaler, tal (0-9), symbol** ser till att lösen ordet innehåller minst två tecken typer. Till exempel ett tal, ett gement och ett versalt.
- **4 av 4: gemener, versaler, siffror (0-9), symbol** ser till att lösen ordet innehåller alla för tecken typer.

    > [!NOTE]
    > Genom att kräva **4 av 4** kan du få till följd av en slutanvändare. Vissa studier har visat att detta krav inte förbättrar entropi. Se [rikt linjer för NIST-lösenord](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
