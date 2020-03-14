---
title: Tal tjänst kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Tal tjänst kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372363"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Tal tjänst kryptering av data i vila

Tal tjänsten krypterar automatiskt dina data när de sparas i molnet. Tal tjänst kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services kryptering

Data krypteras och dekrypteras med hjälp av [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-kompatibla 256-bitars AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kryptering. Kryptering och dekryptering är transparent, vilket innebär att kryptering och åtkomst hanteras åt dig. Dina data är säkra som standard och du behöver inte ändra din kod eller dina program för att utnyttja kryptering.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

När du använder Custom Speech och anpassad röst kan tal tjänsten lagra följande data i molnet:  

* Tal spårnings data – endast om du aktiverar spårning för din anpassade slut punkt
* Överförda utbildningar och test data

Som standard lagras dina data i Microsofts lagring och din prenumeration använder Microsoft-hanterade krypterings nycklar. Du kan också välja att förbereda ditt eget lagrings konto. Åtkomst till butiken hanteras av den hanterade identiteten och tal tjänsten kan inte direkt komma åt dina egna data, till exempel tal spårnings data, anpassnings tränings data och anpassade modeller.

Mer information om hanterad identitet finns i [Vad är hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Ta med din egen lagring (BYOS) för anpassning och loggning

Om du vill begära åtkomst för att ta med din egen lagring kan du fylla i och skicka in tjänsten [Speech service – ta med din egen lagring (BYOS)](https://aka.ms/cogsvc-cmk). När du har godkänt måste du skapa ditt eget lagrings konto för att lagra de data som krävs för anpassning och loggning. När du lägger till ett lagrings konto aktiverar tal tjänst resursen en systemtilldelad hanterad identitet. När systemtilldelad hanterad identitet har Aktiver ATS kommer den här resursen att registreras med Azure Active Directory (AAD). När den hanterade identiteten har registrerats får den åtkomst till lagrings kontot. Du kan lära dig mer om hanterade identiteter här. Mer information om hanterad identitet finns i [Vad är hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter kommer åtkomst till lagrings kontot att tas bort. Detta leder till att de delar av tal tjänsten som kräver åtkomst till lagrings kontot slutar fungera.  

## <a name="regional-availability"></a>Regional tillgänglighet

BYOS är för närvarande tillgängligt i följande regioner:

* USA, södra centrala
* USA, västra 2
* USA, östra

## <a name="next-steps"></a>Nästa steg

* [Speech service – Hämta ditt eget lagrings formulär (BYOS)](https://aka.ms/cogsvc-cmk)
* [Vad är hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
