---
title: Erbjudande om virtuell dator på Azure Marketplace
description: Översikt över processen för att publicera ett VM-erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: 0f2ae9fe6f006b5418ebee82b08a44188b7c58d3
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273077"
---
# <a name="virtual-machine-offer"></a>Erbjudande på virtuella datorer

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Virtual Machine-erbjudande](https://aka.ms/CreateAzureVMoffer) för att hantera dina migrerade erbjudanden.

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| I det här avsnittet beskrivs hur du publicerar ett nytt erbjudande om en virtuell dator på [Azure Marketplace](https://azuremarketplace.microsoft.com). Stöd ges för både Windows-baserade och Linux-baserade virtuella datorer, som innehåller en virtuell hårddisk (VHD) och noll eller fler virtuella data-hårddiskar. | ![Ikon för virtuell dator](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Översikt över publicering

Följande video, [Optimera ditt Azure Marketplace-erbjudande,](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player)ger en bred översikt över Azure Marketplace, inklusive hur du publicerar på den här marknadsplatsen (med hjälp av en lösning för virtuella datorer), hur du optimerar användarupplevelsen med din produktsida och valfri Test Drive-upplevelse, hur användarleads genereras och hur du kan använda dem och optimera kundengagemang.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Processflöde för VM-publicering

Följande diagram illustrerar stegen på hög nivå för att publicera ett vm-erbjudande. 

![Publiceringsprocess för virtuella datorer](./media/publishvm_001.png)

1. Skapa erbjudandet - All information och information om erbjudandet är konfigurerad, inklusive erbjudandebeskrivning, marknadsföringsmaterial, juridisk, supportinformation och tillgångsspecifikationer.

2. Skapa affärs- och tekniska tillgångar - Skapa affärstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska tillgångar för den associerade lösningen (här, de virtuella datorerna och bifogade diskar). 

3. Skapa SKU - Skapa associerade SKU:er som är associerade med erbjudandet och skicka dem.  En unik SKU krävs för varje bild som du planerar att publicera. 
 
4. Certifiera och publicera erbjudandet - När erbjudandet och de tekniska tillgångarna har slutförts kan du skicka in erbjudandet. Den här inlämningen startar publiceringsprocessen, där lösningen testas, valideras, certifieras och sedan "publiceras" på marknadsplatsen.  

## <a name="next-steps"></a>Nästa steg

Innan du överväger de här stegen måste du uppfylla de [tekniska kraven och affärskraven](./cpp-prerequisites.md) för att publicera en virtuell dator till Microsoft Azure Marketplace. 
