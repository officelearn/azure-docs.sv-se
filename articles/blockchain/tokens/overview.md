---
title: Vad är Azure Blockchain-tokens
description: Azure Blockchain Tokens är en plattform som en tjänst (PaaS) för tokenutfärdande och hantering.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240284"
---
# <a name="what-is-azure-blockchain-tokens"></a>Vad är Azure Blockchain-token?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure Blockchain Tokens är en plattform som en tjänst (PaaS) för standardiserad tokenutgivning och hantering över blockchain-liggare i Azure.

Med Hjälp av Azure Blockchain-tokens kan du skapa standardiserade token för din blockchain-lösning med hjälp av en fördefinierad tokenmall. Du kan också skriva din egen tokenmall med hjälp av tjänsten. När du har skapat, använd Azure Blockchain Tokens för att ansluta och utfärda token på en blockchain. När du har utfärdats kan du sedan hantera tokens över flera blockchain-nätverk.

## <a name="templates"></a>Mallar

Använd Azure Blockchain-tokens för att välja en fördefinierad tokenmall eller skapa din egen tokenmall. Azure Blockchain-tokens stöder komposterbarhet för tokenmallar som gör att du kan skapa din egen tokenmall baserat på beteenden som stöds. Tokenmallar kan användas för de flesta blockchain-lösningar eftersom de mappas till de vanligaste tokens. Du kan börja med en mall, anpassa den och distribuera token för din lösning.

Mer information om Azure Blockchain-tokens-mallar finns i [Azure Blockchain Tokens-mallar](templates.md).

## <a name="management"></a>Hantering

Azure Blockchain-tokens tillhandahåller Azure portal-hantering och API:er för att ansluta till ett befintligt blockchain-nätverk. För närvarande kan du ansluta till [Azure Blockchain Service](../service/overview.md) eller en annan Ethereum-familjekedjekedja.

När du är ansluten till ett eller flera blockchain-nätverk kan du använda Azure Blockchain Tokens API:er för att utfärda och hantera token för användning i din blockchain-lösning. Med API:er kan du integrera tokenhantering i dina affärsprogram och logik. Du kan till exempel använda REST API för att hantera token i stället för att hantera token direkt på blockchain.

## <a name="blockchains-and-accounts"></a>Blockkedjor och konton

Azure Blockchain-tokens tillhandahåller Azure portal-hantering och API:er för att skapa nya grupper och nya blockchain-konton i anslutna blockchain-nätverk. Du kan skapa nya konton direkt i dina anslutna nätverk och Azure Blockchain-tokens hanterar dina privata kontonycklar för din räkning. Med hjälp av grupper kan du gruppera olika blockchain-konton från flera nätverk och hantera åtkomstkontroll via grupperna.

Mer information om Azure Blockchain Tokens-kontohantering finns i [Azure Blockchain Tokens-kontohantering](account-management.md).

## <a name="token-taxonomy-framework"></a>Symboliskt ramverk för taxonomi

Azure Blockchain-tokens bygger på en standardbaserad stiftelse som heter Token Taxonomy Framework (TTF). TTF är en uppsättning slutprodukter som skapats från tokengruppen [Token Taxonomy Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI). TTI-arbetsgruppen definierar en affärstaxonomi för tokens och deras beteenden som kan tillämpas i alla större liggare, inklusive Ethereum, Quorum, Corda och Hyperledger Fabric. Arbetsgruppens mål är att skapa ett ramverk som standardiserar användningen av tokens ur ett affärsperspektiv för att driva förenkling och demokratisera tokenbaserad utveckling. Genom att låta branschen definiera dessa token och deras beteende på affärsnivå, är den detaljerade implementeringen av tokens abstraheras bort från affärslogiken som manipulerar token.

## <a name="support-and-feedback"></a>Support och feedback

För Azure Blockchain-nyheter kan du besöka [Azure Blockchain-bloggen](https://azure.microsoft.com/blog/topics/blockchain/) för att hålla dig uppdaterad om blockchain-tjänsterbjudanden och information från Azure Blockchain-teknikteamet.

Kontakta Azure [Blockchain Tokens-teamet](https://aka.ms/PreviewForm)om du vill få åtkomst till förhandsversionen av Azure Blockchain Tokens .

Om du vill ge produktfeedback eller begära nya funktioner kan du rösta på en idé via [Azures feedbackforum för blockchain](https://aka.ms/blockchainuservoice).

## <a name="next-steps"></a>Nästa steg

Läs mer om tillgängliga [Azure Blockchain-tokenmallar](templates.md).
