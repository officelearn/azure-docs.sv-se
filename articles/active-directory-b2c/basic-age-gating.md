---
title: Aktivera ålders- och uppskrivning i Azure Active Directory B2C | Microsoft-dokument
description: Läs mer om hur du identifierar minderåriga som använder ditt program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56cbeb8e8fe21f4b39c2f5c6af43e83ae330e5d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189981"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Aktivera Ålder Gating i Azure Active Directory B2C

>[!IMPORTANT]
>Den här funktionen är en allmänt tillgänglig förhandsversion. Använd inte funktionen för produktionsprogram.
>

Med Azure Active Directory B2C (Azure AD B2C) kan du identifiera minderåriga som vill använda ditt program. Du kan välja att blockera den minderårige från att logga in på programmet. Användare kan också gå tillbaka till programmet och identifiera sin åldersgrupp och deras föräldramedgivandestatus. Azure AD B2C kan blockera minderåriga utan föräldrarnas medgivande. Azure AD B2C kan också konfigureras så att programmet kan bestämma vad som ska göras med minderåriga.

När du har aktiverat åldersanalys i [användarflödet](user-flow-overview.md)tillfrågas användarna när de är födda och vilket land/vilken region de bor i. Om en användare loggar in som inte tidigare har angett informationen måste han eller hon ange den nästa gång de loggar in. Reglerna tillämpas varje gång en användare loggar in.

Azure AD B2C använder den information som användaren anger för att identifiera om de är en minderårig. Fältet **ageGroup** uppdateras sedan i deras konto. Värdet kan `null`vara `Undefined` `Minor`, `Adult`, `NotAdult`, och .  **Fälten ageGroup** och **consentProvidedForMinor** används sedan för att beräkna värdet för **legalAgeGroupClassification**.

Åldersbating innebär två åldersvärden: den ålder som någon inte längre anses vara minderårig, och den ålder vid vilken en minderårig måste ha föräldrarnas samtycke. I följande tabell visas de åldersregler som används för att definiera en minderårig och en minderårig som kräver samtycke.

| Land/region | Namn på land/region | Mindre samtycke ålder | Mindre ålder |
| -------------- | ------------------- | ----------------- | --------- |
| Default | Inget | Inget | 18 |
| AE | Förenade Arabemiraten | Inget | 21 |
| AT | Österrike | 14 | 18 |
| BE | Belgien | 14 | 18 |
| BG | Bulgarien | 16 | 18 |
| BH | Bahrain | Inget | 21 |
| CM | Kamerun | Inget | 21 |
| CY | Cypern | 16 | 18 |
| CZ | Tjeckien | 16 | 18 |
| DE | Tyskland | 16 | 18 |
| DK | Danmark | 16 | 18 |
| EE | Estland | 16 | 18 |
| EG | Egypten | Inget | 21 |
| ES | Spanien | 13 | 18 |
| FR | Frankrike | 16 | 18 |
| GB | Storbritannien | 13 | 18 |
| GR | Grekland | 16 | 18 |
| HR | Kroatien | 16 | 18 |
| HU | Ungern | 16 | 18 |
| IE | Irland | 13 | 18 |
| IT | Italien | 16 | 18 |
| KR | Sydkorea | 14 | 18 |
| LT | Litauen | 16 | 18 |
| LU | Luxemburg | 16 | 18 |
| LV | Lettland | 16 | 18 |
| MT | Malta | 16 | 18 |
| Ej tillämpligt | Namibia | Inget | 21 |
| NL | Nederländerna | 16 | 18 |
| PL | Polen | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Rumänien | 16 | 18 |
| SE | Sverige | 13 | 18 |
| SG | Singapore | Inget | 21 |
| SI | Slovenien | 16 | 18 |
| SK | Slovakien | 16 | 18 |
| TD | Tchad | Inget | 21 |
| TH | Thailand | Inget | 20 |
| TW | Taiwan | Inget | 20 |
| USA | USA | 13 | 18 |

## <a name="age-gating-options"></a>Alternativ för åldersgating

### <a name="allowing-minors-without-parental-consent"></a>Tillåta minderåriga utan föräldrarnas samtycke

För användarflöden som tillåter antingen registrering, inloggning eller båda kan du välja att tillåta minderåriga utan medgivande i ditt program. Minderåriga utan föräldrarnas medgivande får logga in eller registrera sig som vanligt och Azure AD B2C utfärdar en ID-token med **legalAgeGroupClassification-anspråket.** Det här påståendet definierar den upplevelse som användare har, till exempel att samla in föräldrarnas samtycke och uppdatera fältet **ConsentProvidedForMinor.**

### <a name="blocking-minors-without-parental-consent"></a>Blockera minderåriga utan föräldrarnas samtycke

För användarflöden som tillåter antingen registrering, inloggning eller båda kan du välja att blockera minderåriga utan medgivande från programmet. Följande alternativ är tillgängliga för hantering av blockerade användare i Azure AD B2C:

- Skicka tillbaka en JSON till programmet - det här alternativet skickar ett svar tillbaka till programmet att en minderårig blockerades.
- Visa en felsida – användaren visas en sida som informerar dem om att de inte kan komma åt programmet.

## <a name="set-up-your-tenant-for-age-gating"></a>Konfigurera din klient för ålders-

Om du vill använda åldersanalys i ett användarflöde måste du konfigurera klienten så att den har ytterligare egenskaper.

1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn. Välj den katalog som innehåller din klient.
2. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen, sök efter och välj Azure AD **B2C**.
3. Välj **Egenskaper** för din klient i menyn till vänster.
2. Klicka på **Konfigurera**under avsnittet **Åldersgläderande** .
3. Vänta tills åtgärden har slutförts och din klient kommer att ställas in för ålder gating.

## <a name="enable-age-gating-in-your-user-flow"></a>Aktivera ålders- och upprinnning i ditt användarflöde

När din klient har konfigurerats för att använda åldersanalys kan du sedan använda den här funktionen i [användarflöden](user-flow-versions.md) där den är aktiverad. Du aktiverar åldersaktivering med följande steg:

1. Skapa ett användarflöde som har åldersgating aktiverat.
2. När du har skapat användarflödet väljer du **Egenskaper** på menyn.
3. I avsnittet **Åldersgating** väljer du **Aktiverad**.
4. Du bestämmer sedan hur du vill hantera användare som identifierar sig som minderåriga. För **registrering eller inloggning**väljer `Allow minors to access your application` du `Block minors from accessing your application`eller . Om blockering av minderåriga är `Send a JSON back to the application` markerat markerar du eller `Show an error message`.




