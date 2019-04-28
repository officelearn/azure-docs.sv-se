---
title: Erbjudandeinställningar för en Azure IoT Edge-modulen | Microsoft Docs
description: Konfigurera inställningar för erbjudande för en IoT Edge-modul.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a2ec41d7e2f21dc1550cd3c56009204842e1641
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912102"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge-modul erbjuder inställningsflik

Den **IoT Edge-moduler > nytt erbjudande** öppnas med fokus på den **erbjuder inställningar** fliken. 

![Ny sida i erbjudandet för IoT Edge-moduler](./media/iot-edge-module-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Inställningar för erbjudande-ID

Under **erbjuder identitet**, måste du ange information för fälten som beskrivs i följande tabell. En asterisk (*) läggs till fältnamnet anger att det krävs. 

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID**       | En unik identifierare (inom en utgivarprofil för) för erbjudandet. Den här identifieraren syns i URL: er och insikter rapporter i produkten. Det har högst 50 tecken och kan använda gemena alfanumeriska tecken och bindestreck (-). (Det ID: t får inte sluta med ett bindestreck.) **Obs!** Det här fältet kan inte ändras när ett erbjudande lanseras. <br> Till exempel om Contoso publicerar ett erbjudande med ID för erbjudande **-iot-edge-exempelmodulen**, tilldelas URL: en för Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Utgivare**     | Din organisations unika identifieraren i Azure Marketplace. Alla dina erbjudanden som ska associeras med ditt publisher-ID. Det här värdet kan inte ändras efter erbjudandets sparas. |
| **Namn**          | Visningsnamn för ditt erbjudande. Det här namnet visas i Azure Marketplace och partnerportalen i molnet. Det får innehålla högst 50 tecken. Vi rekommenderar att du använder ett beskrivande namn för varumärke för din produkt. Omfattar inte namnet på din organisation om det inte är hur din produkt släpps. Om du marknadsföring det här erbjudandet i andra webbplatser och publiceringar, se till att namnet exakt samma i alla publikationer. |
|  |  |

Välj **spara** att spara inställningarna erbjuder.

## <a name="next-steps"></a>Nästa steg

Använd den [SKU: er](./cpp-skus-tab.md) fliken Konfigurera SKU: er för ditt erbjudande.
