---
title: Standard kontrakt | Azure
description: Standard kontrakt i Azure Marketplace och AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819499"
---
# <a name="standard-contract"></a>Standardavtal

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft en standard kontrakts mal len för att hjälpa till att under lätta en transaktion i Marketplace. I stället för att utforma anpassade villkor kan Azure Marketplace-utgivare välja att erbjuda sin program vara under standard kontraktet, som kunderna bara behöver Undersök och godkänna en gång. Du hittar standard kontraktet här: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

Villkoren för ett erbjudande definieras på fliken Marketplace när du skapar ett erbjudande i Cloud Partner Portal. Alternativet Standard kontrakt är aktiverat genom att ändra inställningen till Ja.

![Aktiverar standard kontrakts alternativet](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Om du väljer att använda standard kontraktet krävs separata villkor fortfarande för [Cloud Solution Provider](./cloud-solution-providers.md) -kanalen.

## <a name="standard-contract-amendments"></a>Standard kontrakts ändringar

Med standard kontrakts ändringar kan utgivare välja standard kontrakt för enkelhetens skull och med anpassade villkor för produkten eller verksamheten.  Kunderna behöver bara granska ändringarna i kontraktet, om de redan har granskat och godkänt Microsofts standard kontrakt.

Det finns två typer av ändringar som är tillgängliga för Azure Marketplace-utgivare:

* Universella ändringar: dessa ändringar tillämpas universellt på standard kontraktet för alla kunder. Universella ändringar visas för varje kund av produkten i inköps flödet.

![Allmänna ändringar](media/marketplace-publishers-guide/universal-amendaments.png)

* Anpassade ändringar: Azure Marketplace har också en etablering för anpassade ändringar riktade till klienter. De är särskilda ändringar i standard kontraktet som endast är riktade till vissa kunder. Utgivare kan välja den klient som de vill rikta in sig på. Kunder från den klienten köper produkten enligt standard kontrakt och de riktade ändringarna.

![Anpassade ändringar](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Kunder som är riktade mot anpassade ändringar kommer också att få den universella ändringen av standard villkoren under köpet.

>[!Note]
>Följande typer av erbjudanden stöder standard kontrakt ändringar: Azure-program (lösningsfiler och hanterade program), Virtual Machines, behållare, behållar program.

### <a name="customer-experience"></a>Kund upplevelse

Under inköps processen i Azure Portal kan kunderna se villkoren som är associerade med produkten som Microsoft standard avtal och ändringar.

![Azure Portal kund upplevelse.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Kunder kan använda `Get-AzureRmMarketplaceTerms` för att hämta villkoren för ett erbjudande och godkänna det. Standard kontraktet och tillhör ande ändringar kommer att returneras i utdata från cmdleten.

---
