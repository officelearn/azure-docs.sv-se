---
ms.assetid: 
title: "Azure Key Vault-säkerhetsvärldar | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 921bbd109c9ea98d8b5c286a7512bed026412d26
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault-säkerhetsvärldar och geografisk gränser

Azure Key Vault är en tjänst med flera klienter och använder en pool med Maskinvarusäkerhetsmoduler (HSM) i varje Azure-plats. 

Alla HSM: er på Azure platser i samma geografiska region dela samma kryptografiska gräns (Thales Säkerhetsvärld). Till exempel dela östra USA och västra USA samma säkerhetsvärlden eftersom de tillhör geoplats USA. På liknande sätt kan dela alla Azure platser i Japan samma säkerhetsvärlden och alla Azure platser i Australien Indien och så vidare. 

## <a name="backup-and-restore-behavior"></a>Beteende för säkerhetskopiering och återställning

En säkerhetskopia som gjorts av en nyckel från nyckelvalvet i en Azure-plats kan återställas till nyckelvalvet i en annan Azure-plats, förutsatt att båda dessa villkor är uppfyllda:

- Båda av platserna som Azure tillhör samma geografiska plats
- Både nyckelvalv tillhör samma Azure-prenumerationen

Till exempel kan en säkerhetskopia som gjorts av en viss prenumeration på en nyckel i nyckelvalvet i västra Indien bara återställas till en annan nyckelvalvet i samma prenumeration och geoplats; Västra Indien, centrala Indien eller södra Indien.

## <a name="regions-and-products"></a>Regioner och produkter

- [Azure-regioner](https://azure.microsoft.com/regions/)
- [Microsoft-produkter efter region](https://azure.microsoft.com/regions/services/)

Regioner är mappade till säkerhetsvärldar visas som större rubriker i tabeller:

I produkter efter region artikel, till exempel den **Americas** fliken innehåller ÖSTRA USA, centrala USA, västra USA all mappning för regionen USA. 

>[!NOTE]
>Ett undantag är att oss DOD ÖST och oss DOD centrala har sina egna säkerhetsvärldar. 

På liknande sätt på den **Europa** fliken NORRA Europa och VÄSTEUROPA mappar båda till Europa region. Samma gäller även på den **Stillahavsområdet** fliken.



