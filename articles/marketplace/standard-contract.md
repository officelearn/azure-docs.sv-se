---
title: Standardavtal | Azure
description: Standardavtal i Azure Marketplace och AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620402"
---
# <a name="standard-contract"></a>Standardavtal

För att förenkla inköpsprocessen för kunder och minska juridiska komplexiteten för programvaruleverantörer, erbjuder Microsoft en standardavtal mall för att hjälpa att underlätta en transaktion i marketplace. I stället för att utforma anpassade villkor, kan Azure Marketplace-utgivare välja att erbjuda sin programvara under standardavtal som kunder behöver bara se och acceptera en gång. Standard kontraktet finns här: [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178). 

Villkor för ett erbjudande har definierats på fliken Marketplace när du skapar ett erbjudande i partnerportalen i molnet. Standardavtal-alternativet är aktiverat genom att ändra inställningen till Ja.

![Aktiverar alternativet standardavtal](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Om du väljer att använda Standard kontraktet separat villkor krävs fortfarande för den [Molnlösningsleverantör](./cloud-solution-providers.md) kanal.

## <a name="standard-contract-amendments"></a>Standardavtal ändringar

Standard kontrakt ändringar kan utgivare att välja standardavtal för enkelhetens skull, och med anpassade villkor för sin produkt eller ditt företag.  Kunderna bara behöver granska ändringar i kontraktet, om de redan har granskat och godkänt Microsoft Standard kontraktet.

Det finns två typer av ändringar för Azure Marketplace-utgivare:

* Universal ändringar: Dessa ändringar tillämpas universellt standardavtal för alla kunder. Universal ändringar visas varje kund av produkten i köp flödet.

![Universal ändringar](media/marketplace-publishers-guide/universal-amendaments.png)

* Anpassade ändringar: Azure Marketplace har också en etablera för anpassade ändringar som är riktade till klienter. De är särskilt ändringar standard kontraktet som är avsedda för vissa kunder bara. Utgivare kan välja den klient som de vill använda. Kunder från den klienten skulle köpa produkten under standardavtal och de aktuella ändringarna.

![Anpassade ändringar](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Kunder som mål för anpassade ändringar får också standardvillkor vid inköp universal ändringen.

>[!Note]
>Följande typer av erbjudanden stöder Standard kontrakt ändringar: Azure-program (Lösningsmallar och hanterade program), virtuella datorer, behållare, behållarprogram.

### <a name="customer-experience"></a>Kundupplevelsen

Kunder kommer att kunna se de villkor som gäller för produkten som Microsoft standardavtal och ändringar under inköpsprocessen i Azure-portalen.

![Azure portal kundupplevelsen.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Kunder kan använda `Get-AzureRmMarketplaceTerms` hämtar du villkoren i ett erbjudande och acceptera den. Standardavtal och associerade ändringar kommer att returneras i utdata från cmdleten.

---
