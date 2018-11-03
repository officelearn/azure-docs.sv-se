---
title: Erbjudandeinställningar för en avbildning i Azure-behållare | Microsoft Docs
description: Konfigurera inställningar för erbjudande för en Azure-behållare.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 9a669ebad674f938d11c8e372e6f586ca3c67f03
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979848"
---
# <a name="container-offer-settings-tab"></a>Behållare erbjuder inställningsflik

Den **behållare > nytt erbjudande** öppnas med fokus på den **erbjuder inställningar** fliken. 

![Erbjud identitet](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Inställningar för erbjudande-ID

Under **erbjuder identitet**, måste du ange information för fälten som beskrivs i följande tabell. En asterisk (*) läggs till fältnamnet anger att det krävs. 

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID**       | En unik identifierare (inom en utgivarprofil för) för erbjudandet. Den här identifieraren syns i URL: er och insikter rapporter i produkten. Det har högst 50 tecken och kan använda gemena alfanumeriska tecken och bindestreck (-). (Det ID: t får inte sluta med ett bindestreck.) **Obs:** det här fältet kan inte ändras när ett erbjudande lanseras. <br> Till exempel om Contoso publicerar ett erbjudande med ID för erbjudande **exempelbehållaren**, tilldelas URL: en för Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **Utgivar-ID**     | Din organisations unika identifieraren i Azure Marketplace. Alla dina erbjudanden som ska associeras med ditt publisher-ID. Det här värdet kan inte ändras efter erbjudandets sparas. |
| **Namn**          | Visningsnamn för ditt erbjudande. Det här namnet visas i Azure Marketplace och partnerportalen i molnet. Det får innehålla högst 50 tecken. Vi rekommenderar att du använder ett beskrivande namn för varumärke för din produkt. Omfattar inte namnet på din organisation om det inte är hur din produkt släpps. Om du marknadsföring det här erbjudandet i andra webbplatser och publiceringar, se till att namnet exakt samma i alla publikationer. |
|  |  |

Välj **spara** att spara inställningarna erbjuder.

## <a name="next-steps"></a>Nästa steg

Använd den [SKU: er](./cpp-skus-tab.md) fliken Konfigurera SKU: er för ditt erbjudande.
