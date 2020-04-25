---
title: Erbjudande för virtuella datorer på Azure Marketplace
description: Översikt över processen för att publicera ett virtuellt dator erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: b857cf8b7485beb884adf77d99f82d965f55a0ad
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142789"
---
# <a name="virtual-machine-offer"></a>Erbjudande på virtuella datorer

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett erbjudande för virtuell Azure-dator](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Det här avsnittet beskriver hur du publicerar ett nytt erbjudande för virtuella datorer på [Azure Marketplace](https://azuremarketplace.microsoft.com). Support tillhandahålls för både Windows-och Linux-baserade virtuella datorer, som innehåller en virtuell hård disk för operativ system (VHD) och noll eller flera data-VHD: er. | ![ikon för virtuell dator](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Publicerings översikt

Följande video, [optimera ditt Azure Marketplace-erbjudande](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), presenterar en översikt över Azure Marketplace, inklusive hur du publicerar på den här Marketplace (med hjälp av en lösning för virtuella datorer), hur du optimerar användar upplevelsen med din produkt sida och valfri test enhets upplevelse, hur användar leads genereras och hur du kan använda dem och optimera kund engagemang.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Process flöde för VM-publicering

Följande diagram illustrerar de övergripande stegen för att publicera ett erbjudande för virtuella datorer. 

![Process för VM-publicering](./media/publishvm_001.png)

1. Skapa erbjudandet – all information och information om erbjudandet konfigureras, inklusive erbjudande beskrivning, marknadsförings material, juridik, supportinformation och till gångs uppgifter.

2. Skapa företags-och teknik till gångar – skapa företags till gångar (juridiska dokument och marknadsförings material) och tekniska till gångar för den associerade lösningen (här, de virtuella datorerna och anslutna diskar). 

3. Skapa SKU – skapa tillhör ande SKU: er som är associerade med erbjudandet och skicka dem.  En unik SKU krävs för varje avbildning som du planerar att publicera. 
 
4. Certifiera och publicera erbjudandet – när erbjudandet och de tekniska till gångarna har slutförts kan du skicka in erbjudandet. Den här sändningen påbörjar publicerings processen där lösningen testas, verifieras, certifieras och sedan blir Live på Marketplace.  

## <a name="next-steps"></a>Nästa steg

Innan du tar hänsyn till de här stegen måste du uppfylla de [tekniska och affärsmässiga kraven](./cpp-prerequisites.md) för att publicera en virtuell dator till Microsoft Azure Marketplace. 
