---
title: Region regionstillgänglighet och dataplacering i Azure Active Directory B2C | Microsoft Docs
description: Ett avsnitt om vilka typer av Azure Active Directory B2C-klienter.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 928c6316ea964472faadc82213c4c1ff81c3e038
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317112"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Regionstillgänglighet och dataplacering
Tillgänglighet för regioner och dataplacering är två helt olika begrepp som gäller på olika sätt för Azure AD B2C från resten av Azure. Den här artikeln förklarar skillnaderna mellan dessa två begrepp och jämför hur de används Azure jämfört med Azure AD B2C.

## <a name="summary"></a>Sammanfattning
Azure AD B2C är **allmänt tillgänglig i hela världen** med alternativet för **dataplacering i USA eller Europa**.

## <a name="concepts"></a>Begrepp
* **Regiontillgänglighet** avser där en tjänst är tillgänglig för användning.
* **Dataplacering** refererar till där användarinformationen är lagrad.

## <a name="region-availability"></a>Regional tillgänglighet
Azure AD B2C är tillgängligt i hela världen via det offentliga Azure-molnet. 

Detta skiljer sig från modellen följer de flesta andra Azure-tjänster som koppla tillgänglighet med dataplacering. Du kan se exempel på detta i både Azure [produkter tillgängliga efter Region](https://azure.microsoft.com/regions/services/) sidan och [Active Directory B2C priskalkylator](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Dataplacering
Azure AD B2C lagrar användardata i USA eller Europa.

Dataplacering fastställs baserat på vilket land/region är markerad när [skapar en Azure AD B2C-klient](active-directory-b2c-get-started.md).

![Skärmbild av en Förhandsgranska klient](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Det finns data i USA för följande länder/regioner:

> USA, Kanada, Costa Rica, Dominikanska republiken, El Salvador, Guatemala, Mexiko, Panama, Puerto Rico och Trinidad och Tobago

Det finns data i Europa för följande länder/regioner:

> Algeriet, Österrike, Azerbajdzjan, Bahrain, Vitryssland, Belgien, Bulgarien, Kroatien, Cypern, Tjeckien, Danmark, Egypten, Estland, Finland, Frankrike, Tyskland, Grekland, Ungern, Island, Irland, Israel, Italien, Jordanien, Kazakstan, Kenya, Kuwait, Lettland, Libanon Liechtenstein, Litauen, Luxemburg, norra Makedonien, Malta, Montenegro, Marocko, Nederländerna, Nigeria, Norge, Oman, Pakistan, Polen, Portugal, Qatar, Rumänien, Ryssland, Saudiarabien, Serbien, Slovakien, Slovenien, Sydafrika, Spanien, Sverige, Schweiz, Tunisien, Turkiet, Ukraina, Förenade Arabemiraten och Storbritannien.

Återstående länderna/regionerna håller som läggs till i listan.  Nu kan du fortfarande använda Azure AD B2C genom att välja någon av de länder/regionerna som ovan.

> Afghanistan, Argentina, Australien, Brasilien, Chile, Colombia, Ecuador, Hongkong SAR, Indien, Indonesien, Irak, Japan, Sydkorea, Malaysia, Nya Zeeland, Paraguay, Peru, Filippinerna, Singapore, Sri Lanka, Taiwan, Thailand, Uruguay och Venezuela.

## <a name="preview-tenant"></a>Förhandsgranska klient
Om du har skapat en B2C-klient för Azure AD B2C-förhandsversionen, är det troligt som din **klient typ** säger **Förhandsgranska klient**. Om så är fallet, måste du använda din klient endast för utveckling och testning och inte för produktionsappar.

> [!IMPORTANT]
> Det finns inga migreringsvägen från en förhandsversion B2C-klient till en produktionsskala-B2C-klient. Observera att det finns kända problem när du tar bort en Förhandsgranska B2C-klient och återskapa en produktionsskala-B2C-klient med samma domännamn. Du måste skapa en B2C-produktionsskala-klient med ett annat domännamn.


![Skärmbild av en Förhandsgranska klient](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
