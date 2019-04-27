---
title: Inställningar för virtuella datorer ger flik i Cloud Partner Portal för Azure Marketplace | Microsoft Docs
description: Beskriver fliken erbjuder inställningar som används för att skapa ett erbjudande för Azure Marketplace-datorn.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
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
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844356"
---
# <a name="virtual-machine-offer-settings-tab"></a>Virtuell dator erbjuder inställningsflik

Den **nytt erbjudande** för virtuella datorer öppnas i den första fliken med namnet **erbjuder inställningar**.  En tillagda asterisk (*) på fältnamnet indikerar att det krävs. 

![Ny sida för erbjudande för virtuella datorer](./media/publishvm_004.png)

I den **erbjuder inställningar** fliken måste du ange följande obligatoriska fält.

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID**       | En unik identifierare (inom en utgivarprofil för) för erbjudandet. Den här identifieraren kommer att synas på produkten URL: er, Azure Resource Manager-mallar och fakturering rapporter. Det har högst 50 tecken, endast bestå av alfanumeriska gemener och bindestreck (-), men får inte sluta med ett bindestreck. Det här fältet kan inte ändras när ett erbjudande lanseras. <br> Till exempel om Contoso publicerar ett erbjudande med ID för erbjudande **exempel vm**, den är tilldelad URL: en för Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Utgivare**     | Din organisations unika identifieraren i Azure Marketplace. Alla dina erbjudanden som ska associeras med ditt publisher-ID. Det här värdet kan inte ändras när erbjudandet har sparats. |
| **Namn**          | Visningsnamn för ditt erbjudande. Det här namnet visas i Azure Marketplace och partnerportalen i molnet. Det får innehålla högst 50 tecken. Här vägledningen är att inkludera ett beskrivande namn för varumärke för din produkt. Omfattar inte organisationens namn här om det inte är hur den släpps. Om du marknadsföring det här erbjudandet i andra webbplatser och publiceringar, se till att namnet exakt samma i alla publikationer. |
|  |  |
 
Klicka på **spara** spara förloppet. I nästa flik du till [SKU: er](./cpp-skus-tab.md) på ditt erbjudande.
