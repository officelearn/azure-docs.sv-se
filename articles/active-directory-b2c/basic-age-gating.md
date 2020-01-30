---
title: Aktivera ålders hantera i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om att identifiera minderåriga med ditt program.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1fc63b222fd2f08bb4b5596d58f825c8f6b1910e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836244"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Aktivera ålders hantera i Azure Active Directory B2C

>[!IMPORTANT]
>Den här funktionen är en allmänt tillgänglig förhandsversion. Använd inte funktionen för produktions program.
>

Ålders hantera i Azure Active Directory B2C (Azure AD B2C) gör att du kan identifiera minderåriga som vill använda ditt program. Du kan välja att blockera den underordnade från att logga in på programmet. Användarna kan också gå tillbaka till programmet och identifiera sin ålders grupp och deras överordnade medgivande status. Azure AD B2C kan blockera minderåriger utan överordnat medgivande. Azure AD B2C kan också konfigureras så att programmet kan bestämma vad som ska göras med minderåriga.

När du har aktiverat ålders hantera i ditt [användar flöde](user-flow-overview.md)tillfrågas användarna när de föddes och vilket land/vilken region de bor i. Om en användare loggar in som inte tidigare har angett informationen måste han eller hon ange den nästa gången de loggar in. Reglerna tillämpas varje gång en användare loggar in.

Azure AD B2C använder den information som användaren anger för att identifiera om de är mindre. Fältet **ageGroup** uppdateras sedan i sitt konto. Värdet kan vara `null`, `Undefined`, `Minor`, `Adult`och `NotAdult`.  Fälten **ageGroup** och **consentProvidedForMinor** används sedan för att beräkna värdet för **legalAgeGroupClassification**.

Ålders hantera omfattar två ålders värden: den ålder som någon inte längre anses vara mindre och den tid då en mindre måste ha ett överordnat medgivande. I följande tabell visas de ålders regler som används för att definiera en mindre och en mindre som kräver medgivande.

| Land/region | Land/region namn | Liten medgivande ålder | Mindre ålder |
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
| Personal | Kroatien | 16 | 18 |
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

## <a name="age-gating-options"></a>Alternativ för ålders hantera

### <a name="allowing-minors-without-parental-consent"></a>Tillåta minderåriga utan föräldra medgivande

För användar flöden som tillåter antingen registrering, inloggning eller både och kan du välja att tillåta minderåriga utan medgivande till ditt program. Minderåriga får inte logga in eller registrera sig som vanligt och Azure AD B2C utfärdar en ID-token med **legalAgeGroupClassification** -anspråket. Detta påstående definierar den upplevelse som användarna har, till exempel samla in föräldra medgivande och uppdatera fältet **consentProvidedForMinor** .

### <a name="blocking-minors-without-parental-consent"></a>Blockera minderåriger utan överordnat medgivande

För användar flöden som tillåter antingen registrering, inloggning eller båda kan du välja att blockera minderåriga utan medgivande från programmet. Följande alternativ är tillgängliga för att hantera blockerade användare i Azure AD B2C:

- Skicka en JSON tillbaka till programmet – med det här alternativet skickas ett svar tillbaka till programmet som en mindre blockerades.
- Visa en felsida – användaren visas en sida som informerar dem om att de inte har åtkomst till programmet.

## <a name="set-up-your-tenant-for-age-gating"></a>Konfigurera din klient för ålders hantera

Om du vill använda ålders hantera i ett användar flöde måste du konfigurera klienten så att den har ytterligare egenskaper.

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn. Välj den katalog som innehåller din klient.
2. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal, Sök efter och välj **Azure AD B2C**.
3. Välj **Egenskaper** för din klient på menyn till vänster.
2. Under avsnittet **ålders hantera** klickar du på **Konfigurera**.
3. Vänta tills åtgärden har slutförts och klienten kommer att konfigureras för ålders hantera.

## <a name="enable-age-gating-in-your-user-flow"></a>Aktivera ålders hantera i ditt användar flöde

När din klient organisation har kon figurer ATS för att använda ålders hantera kan du använda den här funktionen i [användar flöden](user-flow-versions.md) där den är aktive rad. Du aktiverar ålders hantera med följande steg:

1. Skapa ett användar flöde som har ålders hantera aktiverat.
2. När du har skapat användar flödet väljer du **Egenskaper** på menyn.
3. I avsnittet **ålders hantera** väljer du **aktive rad**.
4. Sedan bestämmer du hur du vill hantera användare som identifierar som minor. För **registrering eller inloggning**väljer du `Allow minors to access your application` eller `Block minors from accessing your application`. Om du väljer att blockera minor väljer du `Send a JSON back to the application` eller `Show an error message`.




