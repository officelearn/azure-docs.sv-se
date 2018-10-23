---
title: Erbjudande för virtuell dator på Azure Marketplace | Microsoft Docs
description: Översikt över processen för att publicera ett erbjudande för virtuell dator på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639948"
---
# <a name="virtual-machine-offer"></a>Erbjudande för virtuell dator

Det här avsnittet beskriver de element för att publicera en virtuell dator (VM) och är avsedd som en vägledning för utgivaren för att den [Azure Marketplace](https://azuremarketplace.microsoft.com).  Från den här synpunkt, är den uppdelad i följande huvuddelar:

- [Förutsättningar](./cpp-prerequisites.md) – visar en lista över tekniska och affärsmässiga krav innan du skapar eller publicera ett erbjudande för virtuell dator
- [Skapa erbjudande för virtuell dator](./cpp-create-offer.md) – visar de steg som krävs för att skapa en ny virtuell dator erbjuder posten med den [partnerportalen i molnet](https://cloudpartner.azure.com)
- [Skapa VM tekniska resurser](./cpp-create-technical-assets.md) – hur du skapar tekniskt erbjuder tillgångar för en virtuell dator och hur du konfigurerar det här paketet som en virtuell dator på Azure Marketplace
- [Publicera erbjudande för VM](./cpp-publish-offer.md) – hur du skickar in erbjudande för publicering på Azure Marketplace


## <a name="vm-publishing-process-flow"></a>Processflöde för VM-publicering

Följande diagram illustrerar de övergripande stegen vid publicering av ett erbjudande för virtuell dator. 

![Publiceringsprocessen för virtuell dator](./media/publishvm_001.png)

1. Skapa erbjudande - All information och information om erbjudandet har konfigurerats, inklusive erbjudande-beskrivning, marknadsföring material, juridisk information, stöd för information och tillgången specifikationer.

2. Skapa affärs- och tekniska resurser – skapa företagstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska resurser för den associerade lösningen (här, virtuella datorer och anslutna diskar). 

3. Skapa SKU: N – skapa den associerade SKU: er som är associerade med erbjudandet och skicka dem.  En unik SKU måste anges för varje bild som du planerar att publicera. 
 
4. Certifiera och publicera erbjudande - när erbjudandet och tekniska resurser har slutförts, kan du skicka erbjudandet. Den här överföringen startar publiceringsprocessen där lösningen testas verifierats, certifierade, sedan ”lanseras” på marketplace.  

## <a name="next-steps"></a>Nästa steg

Innan du betraktar dessa steg måste du uppfylla de [tekniska krav och affärskrav](./cpp-prerequisites.md) för att publicera en virtuell dator till Microsoft Azure Marketplace. 
