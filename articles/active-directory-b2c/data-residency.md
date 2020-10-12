---
title: Regionstillgänglighet och datahemvist
titleSuffix: Azure AD B2C
description: Regions tillgänglighet, data placering och information om Azure Active Directory B2C för hands versioner av klienter.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: f8c6f7daecd38babaa4f2961d04a6cd4c3b4dbed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840565"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: regions tillgänglighet & data placering

Regions tillgänglighet och data placering är två mycket olika koncept som fungerar annorlunda för Azure AD B2C från resten av Azure. Den här artikeln förklarar skillnaderna mellan dessa två begrepp och jämför hur de gäller för Azure jämfört med Azure AD B2C.

Azure AD B2C är **allmänt tillgänglig i hela världen** med alternativet för **data placering** i **USA, Europa eller Asien och Stillahavsområdet**.

[Region tillgänglighet](#region-availability) syftar på var en tjänst är tillgänglig för användning.

[Data placering](#data-residency) refererar till var användar data lagras.

## <a name="region-availability"></a>Regional tillgänglighet

Azure AD B2C är tillgängligt i hela världen via det offentliga Azure-molnet.

Detta skiljer sig från modellen följt av de flesta andra Azure-tjänster, som vanligt vis är *tillgängliga* med *data placering*. Du kan se exempel på detta i båda Azures [produkter som är tillgängliga på sidan region](https://azure.microsoft.com/regions/services/) och [Active Directory B2C pris kalkylatorn](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Dataplacering

Azure AD B2C lagrar användar data i antingen USA, Europa eller Asien och stillahavsområdet region.

Data placering bestäms av landet/regionen som du väljer när du [skapar en Azure AD B2C klient](tutorial-create-tenant.md):

![Skärm bild av formuläret Skapa klient organisation, Välj land eller region.](./media/data-residency/data-residency-b2c-tenant.png)

Data finns i **USA** för följande länder/regioner:

> USA (US), Kanada (CA), Costa Rica (CR), Dominikanska republiken (a), El Salvador (sa), Guatemala (GT), Mexiko (MX), Panama (PA), Puerto Rico (PR) och Trinidade & Tobago (TT)

Data finns i **Europa** för följande länder/regioner:

> Algeriet (DZ), Österrike (AT), Azerbajdzjan (AZ), Bahrain (BH), Vitryssland (av), Belgien (vara), Bulgarien (BG), Kroatien (HR), Cypern (CY), Tjeckien (CZ), Danmark (DK), Egypten (EG), Estland (EE), Frankrike (FR). Tyskland (DE), Grekland (GR), Ungern (Slovakien), Island (är), Irland (IE), Israel (IL), Italien (IT), Jordanien (JO), Kazakstan (KZ), Kenya (KE), Kuwait (KW), Lettland (LV), Libanon (LI), Litauen (LT) , Luxemburg (LU), Nord Makedonien (ML), Malta (MT), Montenegro (ME), Marocko (MA), Nederländerna (NL), Nigeria (naturgas), Norge (NO), Oman (OM), Pakistan (PK), Polen (PL), Portugal (PT), Qatar (frågor och svar), Rumänien (RO). Ryssland (RU), Saudiarabien (SA), Serbien (RS), Slovakien (SK), Slovenien (ST), södra Afrika (ZA), Spanien (SE), Schweiz (SE), Schweiz (TN), Turkiet (TR), Ukraina (UA), Förenade Arabemiraten (AE) och Storbritannien (GB)

Data finns i **Asien och Stillahavsområdet** för följande länder/regioner:

> Afghanistan (AF), Hongkong SAR (HK), Indien (i), Indonesien (ID), Japan (JP), Korea (KR), Malaysia (MY), Filippinerna (PH), Singapore (TG), Sri Lanka (LK), Taiwan (TW) och Thailand (TH).

Följande länder/regioner håller på att läggas till i listan. Nu kan du fortfarande använda Azure AD B2C genom att välja någon av de länder/regioner som finns ovan.

> Argentina, Australien, Brasilien, Chile, Colombia, Ecuador, Irak, Nya Zeeland, Paraguay, Peru, Uruguay och Venezuela.

## <a name="preview-tenant"></a>Förhandsgranska klient organisation

Om du har skapat en B2C-klient under för hands versionen av Azure AD B2C's är det troligt att **klient typen** säger för **hands klienten**.

I så fall måste du bara använda din klient för utvecklings-och testnings syfte. Använd inte en för hands version av produktions program.

**Det finns ingen sökväg för migrering** från en för hands version B2C-klient till en B2C-klient för produktion. Du måste skapa en ny B2C-klient för dina produktions program.

Det finns kända problem när du tar bort en för hands version av B2C-klienten och skapar en B2C-klient för produktion med samma domän namn. *Du måste skapa en B2C-klient för produktions skala med ett annat domän namn*.

![Skärm bild av en klient typ, som för hands versions klient.](./media/data-residency/preview-b2c-tenant.png)