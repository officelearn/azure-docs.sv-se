---
ms.assetid: ''
title: Azure Key Vault-säkerhetsvärldar | Microsoft Docs
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 7fd7357a317d5059d6169de2c1536568a254e016
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42055170"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault-säkerhetsvärldar och geografiska gränser

Azure Key Vault är en tjänst med flera klienter och använder en pool med Maskinvarusäkerhetsmoduler (HSM) i varje Azure-plats. 

Alla HSM: er i Azure-platser i samma geografiska region delar samma kryptografiska gräns (Thales Säkerhetsvärlden). Till exempel dela östra USA och västra USA samma säkerhetsvärlden eftersom de tillhör geo-platsen USA. Alla Azure-platser i Japan dela på samma sätt kan samma säkerhetsvärlden och alla Azure-platser i Australien, Indien och så vidare. 

## <a name="backup-and-restore-behavior"></a>Beteende för säkerhetskopiering och återställning

Kan återställa en säkerhetskopia som gjorts av en nyckel från key vault i en Azure-plats till ett nyckelvalv i en annan Azure-plats, så länge båda dessa villkor är uppfyllda:

- Både Azure-platser tillhör samma geografiska plats
- Båda nyckelvalven tillhör samma Azure-prenumeration

Till exempel kan en säkerhetskopia som gjorts av en viss prenumeration av en nyckel i ett nyckelvalv i västra Indien, bara återställas till en annan key vault i samma prenumeration och geoplats; Västra Indien, centrala Indien eller södra Indien.

## <a name="regions-and-products"></a>Regioner och produkter

- [Azure-regioner](https://azure.microsoft.com/regions/)
- [Microsoft-produkter per region](https://azure.microsoft.com/regions/services/)

Regioner är mappade till säkerhetsvärldar, visas som stora rubriker i tabellerna:

I produkter efter region artikeln, till exempel den **Americas** fliken innehåller ÖSTRA USA, centrala USA, västra USA all mappning till regionen USA. 

>[!NOTE]
>Ett undantag är att US DOD, ÖSTRA och USA DOD centrala har sina egna säkerhetsvärldar. 

På samma sätt på den **Europa** fliken NORDEUROPA och VÄSTEUROPA mappar båda till regionen Europa. Samma gäller även på den **Asien/Stillahavsområdet** fliken.



