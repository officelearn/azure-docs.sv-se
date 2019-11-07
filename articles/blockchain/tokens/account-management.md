---
title: Konto hantering för Azure blockchain-token
description: Med konto hantering för Azure blockchain-token kan du skapa grupper och länka blockchain-konton för att kontrol lera åtkomsten till blockchain-åtgärder.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: 91dcadd3c7704b7b8c6cab45005bc83e1bba18bb
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579762"
---
# <a name="azure-blockchain-tokens-account-management"></a>Konto hantering för Azure blockchain-token

[!INCLUDE [Preview note](./includes/preview.md)]

För en blockchain-lösning kan användarna behöva olika åtkomst nivåer till de token som skapas med Azure blockchain tokens-tjänsten. I de flesta blockchain-scenarier måste du planera och distribuera olika blockchain-konton som finns i redovisningen. Du måste också hantera åtkomst mellan deltagare. Med konto hantering för Azure blockchain-token kan du skapa grupper och länka blockchain-konton för att kontrol lera åtkomsten till blockchain-åtgärder.

## <a name="blockchain-networks"></a>Blockchain nätverk

Med Azure blockchain-token kan du distribuera och hantera token i en uppsättning blockchain nätverk. Du kan ansluta en enda blockchain-redovisning eller flera blockchain-redovisningar till tjänsten.

## <a name="accounts"></a>Konton

För blockchain-nätverk som är anslutna till Azure blockchain-tokens skapar och hanterar tjänsten kontots privata offentliga nyckel par och utför transaktions signering och överföring. Azure blockchain-token tillhandahåller också identitets mappning som matchar konton med den offentliga nyckel identiteten i redovisningen.

## <a name="groups"></a>Grupper

Med grupper kan du hantera ett stort antal blockchain-konton i anslutna nätverk. Du kan spåra och granska vilka program och användare i katalogen som har möjlighet att använda konton via API: er för Azure blockchain-token. Du kan till exempel gruppera en uppsättning konton som representerar olika affärs linjer eller olika roller och åtkomst till blockchain-token.

Du kan också associera en grupp till en Azure Active Directory användare eller tjänstens huvud namn och detta huvud namn har behörighet till gruppen och dess associerade konton.  

## <a name="next-steps"></a>Nästa steg

Läs mer om tillgängliga [mallar för Azure blockchain-token](templates.md).
