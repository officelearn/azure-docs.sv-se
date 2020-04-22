---
title: Standardavtal | Azure Marketplace
description: Standardkontrakt för Azure Marketplace och AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681452"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardkontrakt för Microsofts kommersiella marknadsplats

För att förenkla upphandlingsprocessen för kunder och minska den juridiska komplexiteten för programvaruleverantörer erbjuder Microsoft ett standardavtal för Microsofts kommersiella marknadsplats för att underlätta transaktioner på marknaden. Hellre än crafting anpassade villkor, kommersiella marknadsplats förlag kan välja att erbjuda sin programvara enligt standardavtalet, som kunderna bara behöver veterinär och acceptera en gång. Standardavtalet hittar du här: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Villkoren för ett erbjudande definieras när erbjudandet skapas i Partner center. Du kan välja att använda standardkontraktet för Microsofts kommersiella marknadsplats i stället för att tillhandahålla dina egna anpassade villkor.

>[!Note]
>När du har publicerat ett erbjudande med standardkontraktet för Microsofts kommersiella marknadsplats kan du inte använda dina egna anpassade villkor. Det är ett "eller" scenario. Du erbjuder antingen din lösning enligt standardavtalet *eller* dina egna villkor. Om du vill ändra villkoren i standardavtalet kan du göra det genom standardavtalsändringar.

## <a name="standard-contract-amendments"></a>Standardändring av kontrakt

Standardavtalsändringar gör det möjligt för utgivare att välja standardkontraktet för enkelhetens skull och med anpassade villkor för sin produkt eller verksamhet. Kunderna behöver bara granska ändringarna i avtalet om de redan har granskat och accepterat Microsofts standardavtal.

Det finns två typer av ändringar tillgängliga för kommersiella marknadsplatsutgivare:

* Universella ändringar: Dessa ändringar tillämpas allmänt på standardavtalet för alla kunder. Universella ändringar visas för varje kund av erbjudandet i inköpsflödet. Kunderna måste acceptera villkoren i standardavtalet och ändringen innan de kan använda ditt erbjudande.

* Anpassade ändringar: Dessa ändringar är särskilda ändringar av standardkontraktet som endast är riktade till specifika kunder via Azure-klient-ID:er. Utgivare kan välja vilken klient de vill rikta in sig på. Endast kunder från klienten kommer att presenteras med de anpassade ändringsvillkoren i erbjudandets inköpsflöde.  Kunderna måste acceptera villkoren i standardavtalet och de ändringar som gäller innan de kan använda ditt erbjudande.

>[!Note]
>Dessa två typer av ändringsförslag stapla ovanpå varandra. Kunder som är inriktade på anpassade ändringar kommer också att få den universella ändringen av standardavtalet vid köp.

Du kan utnyttja standardkontraktet för Microsofts kommersiella marknadsplats för följande erbjudandetyper: Azure Applications (lösningsmallar och hanterade program), virtuella datorer, behållare, behållarprogram, IoT Edge-moduler och SaaS.

## <a name="customer-experience"></a>Kundupplevelse

Under identifieringsupplevelsen på Azure Marketplace eller AppSource kan kunderna se villkoren som är kopplade till erbjudandet som standardkontraktet för Microsofts kommersiella marknadsplats och eventuella universella ändringar.

![Azure portal kundidentifieringsupplevelse.](media/marketplace-publishers-guide/azure-discovery-process.png)

Under inköpsprocessen i Azure-portalen kan kunderna se villkoren som är kopplade till erbjudandet som standardkontraktet för Microsofts kommersiella marknadsplats och eventuella universella och/eller klientspecifika ändringar.

![Azure portal kundinköp erfarenhet.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Kunder kan använda Get-AzureRmMarketplaceTerms för att hämta villkoren för ett erbjudande och acceptera det. Standardkontraktet och tillhörande ändringar returneras i cmdletens utdata.
