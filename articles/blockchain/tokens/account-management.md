---
title: Azure Blockchain Tokens kontohantering
description: Med hjälp av Azure Blockchain Tokens-kontohantering kan du skapa grupper och länka blockchain-konton för att styra åtkomsten till blockchain-åtgärder.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326104"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure Blockchain Tokens kontohantering

[!INCLUDE [Preview note](./includes/preview.md)]

För en blockchain-lösning kan användare kräva olika åtkomstnivåer till tokens som skapas med Azure Blockchain Tokens-tjänsten. I de flesta blockchain-scenarier måste du planera och distribuera olika blockchain-konton som finns i redovisningen. Du måste också hantera åtkomst mellan deltagare.Med hjälp av Azure Blockchain Tokens-kontohantering kan du skapa grupper och länka blockchain-konton för att styra åtkomsten till blockchain-åtgärder.

## <a name="blockchain-networks"></a>Blockchain-nätverk

Azure Blockchain-tokens möjliggör distribution och hantering av tokens över en uppsättning blockchain-nätverk. Du kan ansluta en enda blockchain-huvudbok eller flera blockchain-liggare till tjänsten.

## <a name="accounts"></a>Konton

För blockchain-nätverk som är anslutna till Azure Blockchain-token skapar och hanterar tjänsten kontots privat-offentliga nyckelpar och utför transaktionssignering och inlämning. Azure Blockchain-tokens tillhandahåller också identitetsmappning för att matcha konton med den offentliga nyckelidentiteten i redovisningen.

## <a name="groups"></a>Grupper

Med grupper kan du hantera ett stort antal blockchain-konton i anslutna nätverk. Du kan spåra och granska vilka program och användare i katalogen som har möjlighet att använda konton via Azure Blockchain Tokens API:er. Du kan till exempel gruppera en uppsättning konton som representerar olika affärsområden eller olika roller och åtkomst till blockchain-token.

Du kan också associera en grupp till ett Azure Active Directory-användar- eller tjänsthuvudnamn och det här huvudnamnet har behörighet till gruppen och dess associerade konton.  

## <a name="next-steps"></a>Nästa steg

Läs mer om tillgängliga [Azure Blockchain-tokenmallar](templates.md).
