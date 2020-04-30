---
title: Vad är Azure blockchain-token
description: Azure blockchain-token är en plattform som en tjänst (PaaS) för utfärdande och hantering av token.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240284"
---
# <a name="what-is-azure-blockchain-tokens"></a>Vad är Azure Blockchain-token?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure blockchain-token är en plattform som en tjänst (PaaS) för standardiserad token-utfärdande och-hantering över blockchain-redovisningar i Azure.

Med Azure blockchain-token kan du skapa standardiserade tokens för din blockchain-lösning med hjälp av en fördefinierad token-mall. Du kan också skapa en egen token-mall med hjälp av tjänsten. När du har skapat använder du Azure blockchain-token för att ansluta och utfärda token på en blockchain. När du har utfärdat kan du sedan hantera tokens i flera blockchain-nätverk.

## <a name="templates"></a>Mallar

Använd Azure blockchain-token för att välja en fördefinierad token-mall eller skapa en egen token-mall. Azure blockchain-token stöder filsammansättning för token som gör att du kan skapa din egen token-mall baserat på beteenden som stöds. Token-mallar kan användas för de flesta blockchain-lösningar eftersom de mappar till de mest använda token. Du kan börja med en mall, anpassa den och distribuera token för din lösning.

Mer information om mallar för Azure blockchain-token finns i [mallar för Azure blockchain-token](templates.md).

## <a name="management"></a>Hantering

Azure blockchain-token tillhandahåller Azure Portal hantering och API: er för att ansluta till ett befintligt blockchain-nätverk. För närvarande kan du ansluta till [Azure blockchain-tjänsten](../service/overview.md) eller en annan Ethereum Family-blockchain.

När du har anslutit till ett eller flera blockchain-nätverk kan du använda API: er för Azure blockchain-token för att utfärda och hantera token för användning i din blockchain-lösning. Med hjälp av API: er kan du integrera hantering av token i affärs program och logik. Du kan till exempel använda REST API för att hantera tokens i stället för att hantera tokens direkt på blockchain.

## <a name="blockchains-and-accounts"></a>Blockchains och konton

Azure blockchain-token tillhandahåller Azure Portal hantering och API: er för att skapa nya grupper och nya Blockchain-konton på anslutna blockchain-nätverk. Du kan skapa nya konton direkt på dina anslutna nätverk och Azure blockchain-token hanterar ditt kontos privata nycklar åt dig. Med hjälp av grupper kan du gruppera olika blockchain-konton från flera nätverk och hantera åtkomst kontroll via grupperna.

Mer information om konto hantering för Azure blockchain-token finns i [konto hantering för Azure blockchain-token](account-management.md).

## <a name="token-taxonomy-framework"></a>Ramverk för token taxonomi

Azure blockchain-token bygger på en standard-baserad grund som kallas för token för token. TTF är en uppsättning slut produkter som skapats från arbets gruppen [token](https://entethalliance.org/participate/token-taxonomy-initiative/) för TTI-token. TTI-arbetsgruppen definierar en affärstaxonomi för token och deras beteenden som kan tillämpas på alla huvud böcker, inklusive Ethereum, kvorum, sladdar och mikroredovisningens infrastruktur resurs. Arbets gruppens mål är att skapa ett ramverk som standardiserar användningen av tokens från ett affärs perspektiv till att förenkla och demokratisera identifieringen av-baserad utveckling. Genom att låta branschen definiera dessa tokens och deras beteende på affärs nivå, är den detaljerade implementeringen av tokens sammanslagen från affärs logiken som manipulerar tokens.

## <a name="support-and-feedback"></a>Support och feedback

För Azure blockchain News går du till [Azure blockchain-bloggen](https://azure.microsoft.com/blog/topics/blockchain/) för att hålla dig uppdaterad om blockchain service-erbjudanden och information från Azures teknik team för blockchain.

[Kontakta Azure blockchain tokens-teamet](https://aka.ms/PreviewForm)för att få åtkomst till för hands versionen av Azure blockchain-token.

För att ge feedback på produkter eller för att begära nya funktioner, röst post för en idé via [Azure feedback-forumet för blockchain](https://aka.ms/blockchainuservoice).

## <a name="next-steps"></a>Nästa steg

Läs mer om tillgängliga [mallar för Azure blockchain-token](templates.md).
