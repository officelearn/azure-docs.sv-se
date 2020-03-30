---
title: Förutsättningar för att konfigurera peering med Microsoft
titleSuffix: Azure
description: Förutsättningar för att konfigurera peering med Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775412"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Förutsättningar för att konfigurera peering med Microsoft

Se till att förutsättningarna nedan uppfylls innan du begär en ny peering eller konvertera en äldre peering till Azure-resurs.

## <a name="azure-related-prerequisites"></a>Azure-relaterade förutsättningar
* **Microsoft Azure-konto:** Om du inte har ett Microsoft Azure-konto skapar du ett [Microsoft Azure-konto](https://azure.microsoft.com/free). En giltig och aktiv Microsoft Azure-prenumeration krävs för att konfigurera peering, eftersom peerings modelleras som resurser inom Azure-prenumerationer. Det är viktigt att notera att:
    * De Azure-resurstyper som används för att konfigurera peering är alltid kostnadsfria Azure-produkter, dvs du debiteras inte för att skapa ett Azure-konto eller skapa en prenumeration eller komma åt Azure-resurser **PeerAsn** och **Peering** för att konfigurera peering. Detta ska inte förväxlas med peering-avtal för direkt peering mellan dig och Microsoft, vars villkor uttryckligen diskuteras med vårt peering-team. Kontakta [Microsoft peering](mailto:peering@microsoft.com) om några frågor i detta avseende.
    * Du kan använda samma Azure-prenumeration för att komma åt andra Azure-produkter eller molntjänster som kan vara gratis eller betalas. När du öppnar en betalprodukt debiteras du.
    * Om du skapar ett nytt Azure-konto och/eller prenumeration kan du vara berättigad till kostnadsfri Azure-kredit under en utvärderingsperiod som du kan använda för att prova Azure Cloud-tjänster. Om du är intresserad besöker du [Microsoft Azure-kontot](https://azure.microsoft.com/free) för mer information.

* **Associera peer ASN:** Innan du begär peering associerar du först ditt ASN och kontaktinformation till din prenumeration. Följ instruktionerna i [Associera peer ASN till Azure-prenumeration](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Andra förutsättningar
* **PeeringDB-profil:** Peer-datorer förväntas ha en fullständig och uppdaterad profil på [PeeringDB](https://www.peeringdb.com). Vi använder denna information i vårt registreringssystem för att validera peer-uppgifter såsom NOC-information, teknisk kontaktinformation och deras närvaro på peering-anläggningar etc.

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md).
* [Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)
* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)