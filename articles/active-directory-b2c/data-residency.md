---
title: Regionens tillgänglighet och datahemvist
titleSuffix: Azure AD B2C
description: Regionens tillgänglighet, datahemvist och information om Azure Active Directory B2C-förhandsversionsklienter.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3df0f581d0d2a1e5ca02202b4eeaede5a1dd5362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188859"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Regiontillgänglighet & datahemvist

Regionens tillgänglighet och databehöver är två mycket olika begrepp som gäller på olika sätt för Azure AD B2C från resten av Azure. I den här artikeln beskrivs skillnaderna mellan dessa två begrepp och hur de tillämpas på Azure jämfört med Azure AD B2C.

Azure AD B2C är **allmänt tillgängligt över hela världen** med möjlighet till **datahemvist** i **USA, Europa eller Asien och Stillahavsområdet**.

[Regiontillgänglighet](#region-availability) avser var en tjänst är tillgänglig för användning.

[Med datahemvist](#data-residency) avses var användardata lagras.

## <a name="region-availability"></a>Regional tillgänglighet

Azure AD B2C är tillgängligt över hela världen via det offentliga Azure-molnet.

Detta skiljer sig från modellen följt av de flesta andra Azure-tjänster, som vanligtvis kopplar *tillgänglighet* med *datahemvist*. Du kan se exempel på detta på både sidan [Tillgängliga produkter för regioner](https://azure.microsoft.com/regions/services/) efter region och i Active Directory [B2C-priskalkylatorn](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Dataplacering

Azure AD B2C lagrar användardata i antingen USA, Europa eller Asien och Stillahavsområdet.

Datahemvist bestäms av det land/den region du väljer när du [skapar en Azure AD B2C-klient:](tutorial-create-tenant.md)

![Skärmbild av en förhandsgranskningsklient](./media/data-residency/data-residency-b2c-tenant.png)

Data finns i **USA** för följande länder/regioner:

> USA, Kanada, Costa Rica, Dominikanska republiken, El Salvador, Guatemala, Mexiko, Panama, Puerto Rico och Trinidad & Tobago

Uppgifterna finns i **Europa** för följande länder/regioner:

> Algeriet, Österrike, Azerbajdzjan, Bahrain, Vitryssland, Belgien, Bulgarien, Kroatien, Cypern, Tjeckien, Danmark, Egypten, Estland, Finland, Frankrike, Tyskland, Grekland, Ungern, Island, Irland, Israel, Italien, Jordanien, Kazakstan, Kenya, Kuwait, Lettland, Libanon, Liechtenstein, Litauen, Luxemburg, Nordmakedonien, Malta, Montenegro, Marocko, Nederländerna, Nigeria, Norge, Oman, Pakistan, Polen, Portugal, Qatar, Rumänien, Ryssland, Saudiarabien, Serbien, Slovakien, Slovenien, Sydafrika, Spanien, Sverige, Schweiz, Tunisien, Turkiet, Ukraina, Förenade Arabemiraten och Storbritannien.

Data finns i **Asien och Stillahavsområdet** för följande länder/regioner:

> Afghanistan, Hongkong SAR, Indien, Indonesien, Japan, Korea, Malaysia, Filippinerna, Singapore, Sri Lanka, Taiwan och Thailand.

Följande länder/regioner håller på att läggas till i listan. För tillfället kan du fortfarande använda Azure AD B2C genom att välja något av länderna/regionerna ovan.

> Argentina, Australien, Brasilien, Chile, Colombia, Ecuador, Irak, Nya Zeeland, Paraguay, Peru, Uruguay och Venezuela.

## <a name="preview-tenant"></a>Förhandsgranska klient

Om du hade skapat en B2C-klient under Azure AD B2C:s förhandsgranskningsperiod är det troligt att din **klienttyp** säger **Förhandsklient.**

Om så är fallet måste du använda din klient ENDAST för utvecklings- och testningsändamål. Använd INTE en förhandsgranskningsklient för produktionsprogram.

**Det finns ingen migreringssökväg** från en förhandsgransknings-B2C-klient till en B2C-klient för produktionsskala. Du måste skapa en ny B2C-klient för dina produktionsprogram.

Det finns kända problem när du tar bort en förhandsgranskningS-B2C-klientorganisation och skapar en B2C-klient i produktionsskala med samma domännamn. *Du måste skapa en B2C-klient för produktionsskala med ett annat domännamn*.

![Skärmbild av en förhandsgranskningsklient](./media/data-residency/preview-b2c-tenant.png)