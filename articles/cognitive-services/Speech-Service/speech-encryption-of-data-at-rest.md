---
title: Kryptering av taltjänst för data i vila
titleSuffix: Azure Cognitive Services
description: Kryptering av taltjänst av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372363"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Kryptering av taltjänst för data i vila

Taltjänsten krypterar automatiskt dina data när de sparas till molnet. Kryptering av taltjänster skyddar dina data och hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden.

## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services-kryptering

Data krypteras och dekrypteras med [FIPS 140-2-kompatibel](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bitars AES-kryptering.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) Kryptering och dekryptering är transparenta, vilket innebär att kryptering och åtkomst hanteras för dig. Dina data är säkra som standard och du behöver inte ändra din kod eller dina program för att dra nytta av kryptering.

## <a name="about-encryption-key-management"></a>Om hantering av krypteringsnyckel

När du använder Anpassad tal och anpassad röst kan taltjänsten lagra följande data i molnet:  

* Talspårningsdata - bara om du aktiverar spårningen för din anpassade slutpunkt
* Uppladdade tränings- och testdata

Som standard lagras dina data i Microsofts lagring och din prenumeration använder Microsoft-hanterade krypteringsnycklar. Du har också en möjlighet att förbereda ditt eget lagringskonto. Åtkomsten till arkivet hanteras av tjänsten Hanterad identitet och taltjänsten kan inte direkt komma åt dina egna data, till exempel talspårningsdata, anpassningsutbildningsdata och anpassade modeller.

Mer information om hanterad identitet finns i [Vad är hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Ta med eget lagringsutrymme (BYOS) för anpassning och loggning

För att begära åtkomst för att få ditt eget lagringsutrymme, fyll i och skicka [in taltjänsten - ta med ditt eget formulär för lagring (BYOS).](https://aka.ms/cogsvc-cmk) När du har godkänts måste du skapa ett eget lagringskonto för att lagra de data som krävs för anpassning och loggning. När du lägger till ett lagringskonto aktiverar taltjänstresursen en systemtilldelerad hanterad identitet. När den systemtilldelade hanterade identiteten har aktiverats registreras den här resursen med Azure Active Directory (AAD). När den hanterade identiteten har registrerats får den åtkomst till lagringskontot. Du kan läsa mer om hanterade identiteter här. Mer information om hanterad identitet finns i [Vad är hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter tas åtkomsten till lagringskontot bort. Detta gör att de delar av taltjänsten som kräver åtkomst till lagringskontot slutar fungera.  

## <a name="regional-availability"></a>Regional tillgänglighet

BYOS är för närvarande tillgängligt i dessa regioner:

* USA, södra centrala
* USA, västra 2
* USA, östra

## <a name="next-steps"></a>Nästa steg

* [Taltjänst - ta med eget formulär för lagring (BYOS)](https://aka.ms/cogsvc-cmk)
* [Vad är hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
