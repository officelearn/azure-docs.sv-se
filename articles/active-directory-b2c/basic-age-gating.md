---
title: Aktivera åldershantering i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om hur du identifierar minderåriga använder programmet.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a9220349249315d807a9dba675f6b074ddd385fa
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291104"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Aktivera åldershantering i Azure Active Directory B2C

>[!IMPORTANT]
>Den här funktionen är i offentlig förhandsversion. Använd inte funktionen för program i produktion. 
>

Åldershantering i Azure Active Directory (Azure AD) B2C kan du identifiera minderåriga som vill använda ditt program. Du kan välja att blockera mindre från att logga in i programmet. Användare kan också gå tillbaka till programmet och identifiera deras åldersgrupp och deras medgivandenivå status. Azure AD B2C kan blockera minderåriga utan föräldrars tillstånd. Azure AD B2C kan också konfigureras att tillåta program att avgöra vad du gör med minderåriga.

När du har aktiverat åldershantering i din [användarflödet](active-directory-b2c-reference-policies.md), användarna uppmanas när de föddes och vilka land de finnas i. Om en användare loggar in som tidigare inte har angett information, måste de ange den nästa gång de loggar in. Reglerna tillämpas varje gång en användare loggar in.

Azure AD B2C använder informationen som användaren anger för att identifiera om de är minderårig. Den **ageGroup** uppdateras sedan fältet i sina konton. Värdet kan vara `null`, `Undefined`, `Minor`, `Adult`, och `NotAdult`.  Den **ageGroup** och **consentProvidedForMinor** fält används sedan för att beräkna värdet för **legalAgeGroupClassification**.

Åldershantering omfattar två ålder värden: ålder som någon inte längre anses vara minderårig och ålder då minderårig måste ha föräldrars tillstånd. I följande tabell visas de ålder regler som används för att definiera en mindre och en mindre kräver godkännande.

| Land/region | Landsnamn | Mindre medgivande ålder | Mindre ålder |
| ------- | ------------ | ----------------- | --------- |
| Standard | Ingen | Ingen | 18 |
| AE | Förenade Arabemiraten | Ingen | 21 |
| AT | Österrike | 14 | 18 |
| BE | Belgien | 14 | 18 |
| BG | Bulgarien | 16 | 18 |
| BH | Bahrain | Ingen | 21 |
| CM | Kamerun | Ingen | 21 |
| CY | Cypern | 16 | 18 |
| CZ | Tjeckien | 16 | 18 |
| DE | Tyskland | 16 | 18 |
| DK | Danmark | 16 | 18 |
| EE | Estland | 16 | 18 |
| EG | Egypten | Ingen | 21 |
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
| Ej tillämpligt | Namibia | Ingen | 21 |
| NL | Nederländerna | 16 | 18 |
| PL | Polen | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Rumänien | 16 | 18 |
| SE | Sverige | 13 | 18 |
| SG | Singapore | Ingen | 21 |
| SI | Slovenien | 16 | 18 |
| SK | Slovakien | 16 | 18 |
| TD | Tchad | Ingen | 21 |
| TH | Thailand | Ingen | 20 |
| TW | Taiwan SAR | Ingen | 20 | 
| USA | USA | 13 | 18 |

## <a name="age-gating-options"></a>Hantera alternativ ålder
 
### <a name="allowing-minors-without-parental-consent"></a>Så att minderåriga utan föräldrars tillstånd

För användarflöden som tillåter registrering, inloggning, eller båda, du kan välja att tillåta minderåriga utan medgivande i ditt program. Minderåriga utan föräldrars tillstånd ska kunna logga in eller registrera dig som normalt och Azure AD B2C utfärdar en ID-token med den **legalAgeGroupClassification** anspråk. Det här anspråket definierar som användarna har till exempel samla in föräldrars tillstånd och uppdaterar den **consentProvidedForMinor** fält.

### <a name="blocking-minors-without-parental-consent"></a>Blockera minderåriga utan föräldrars tillstånd

Du kan välja att blockera minderåriga utan medgivande från programmet för användarflöden som gör att antingen registrera, logga in eller båda. Följande alternativ är tillgängliga för hantering av blockerade användare i Azure AD B2C:

- Skicka en JSON tillbaka till programmet – det här alternativet skickar tillbaka ett svar till programmet att minderårig har blockerats.
- Visa en felsida - användaren visas en sida som informerar dem om att de inte kan komma åt programmet.

## <a name="set-up-your-tenant-for-age-gating"></a>Konfigurera din klient för åldershantering

Om du vill använda åldershantering i ett användarflöde, måste du konfigurera din klient om du vill ha ytterligare egenskaper.

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** på den översta menyn. Välj den katalog som innehåller din klient. 
2. Välj **alla tjänster** i det övre vänstra hörnet i Azure Portal Sök efter och välj **Azure AD B2C**.
3. Välj **egenskaper** för din klient i menyn till vänster.
2. Under den **åldershantering** klickar du på **konfigurera**.
3. Vänta tills åtgärden har slutförts och din klient kommer att ställas in för åldershantering.

## <a name="enable-age-gating-in-your-user-flow"></a>Aktivera åldershantering i ditt användarflöde

När din klient har ställts in för användning av åldershantering, du kan sedan använda den här funktionen i [användarflöden](user-flow-versions.md) där det är aktiverat. Du aktiverar åldershantering med följande steg:

1. Skapa ett användarflöde som har åldershantering aktiverat.
2. När du har skapat användarflödet Välj **egenskaper** på menyn.
3. I den **åldershantering** väljer **aktiverad**.
4. Du kan sedan bestämmer hur du vill hantera användare som identifierar som minderåriga. För **registrering eller inloggning**, du väljer `Allow minors to access your application` eller `Block minors from accessing your application`. Om blockerar minderåriga är valt väljer du `Send a JSON bcak to the application` eller `Show an error message`. 




