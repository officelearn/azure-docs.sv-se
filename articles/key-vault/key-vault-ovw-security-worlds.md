---
title: Säkerhetsvärldar i Azure Key Vault | Microsoft-dokument
description: Azure Key Vault är en tjänst med flera innehavare. Den använder en pool av HSM:er på varje Azure-plats. Alla platser i en geografisk region delar en kryptografisk gräns.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: 3584f83c5e1a5e83d069373395227b70c084eae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457397"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Säkerhetsvärldar och geografiska gränser för Azure Key Vault

Azure Key Vault är en tjänst med flera innehavare och använder en pool av HSM -moduler (Hardware Security Modules) på varje Azure-plats. 

Alla HSM:er på Azure-platser i samma geografiska region har samma kryptografiska gräns (Thales Security World). Till exempel, Östra USA och västra USA delar samma säkerhet världen eftersom de tillhör den amerikanska geo plats. På samma sätt delar alla Azure-platser i Japan samma säkerhetsvärld och alla Azure-platser i Australien, Indien och så vidare. 

## <a name="backup-and-restore-behavior"></a>Säkerhetskopiering och återställning

En säkerhetskopia av en nyckel från ett nyckelvalv på en Azure-plats kan återställas till ett nyckelvalv på en annan Azure-plats, så länge båda dessa villkor är uppfyllda:

- Båda Azure-platserna tillhör samma geografiska plats
- Båda nyckelvalven tillhör samma Azure-prenumeration

En säkerhetskopia som tas av en viss prenumeration på en nyckel i ett nyckelvalv i västra Indien kan till exempel bara återställas till ett annat nyckelvalv i samma prenumeration och geografisk plats. Västra Indien, Centrala Indien eller Södra Indien.

## <a name="regions-and-products"></a>Regioner och produkter

- [Azure-regioner](https://azure.microsoft.com/regions/)
- [Microsoft-produkter efter region](https://azure.microsoft.com/regions/services/)

Regionerna är mappade till säkerhetsvärldar, som visas som huvudrubriker i tabellerna:

I produkterna efter region artikel, till exempel, **innehåller Americas** fliken ÖSTRA USA, CENTRALA USA, VÄSTRA USA alla kartläggning till Amerika regionen. 

>[!NOTE]
>Ett undantag är att US DOD EAST och US DOD CENTRAL har sina egna säkerhetsvärldar. 

På samma sätt **Europe** kartlägger nordeuropa och Västeuropa i Europa båda europaregionen. Detsamma gäller även på fliken **Asien och Stillahavsområdet.**



