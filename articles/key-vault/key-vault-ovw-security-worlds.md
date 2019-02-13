---
ms.assetid: ''
title: Azure Key Vault-säkerhetsvärldar | Microsoft Docs
ms.service: key-vault
ms.topic: conceptual
author: bryanla
ms.author: bryanla
manager: barbkess
ms.date: 07/03/2017
ms.openlocfilehash: 3dea506958bbe41f1c387959bb1188696e57043a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107540"
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



