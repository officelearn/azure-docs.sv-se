---
title: Fliken Inställningar för erbjud virtuell dator i Cloud Partner Portal för Azure Marketplace
description: Beskriver fliken erbjudande inställningar som används för att skapa ett erbjudande för virtuella Azure Marketplace-datorer.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 6f7b90f6b02999869026db24836091233692143c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824446"
---
# <a name="virtual-machine-offer-settings-tab"></a>Fliken Inställningar för erbjud virtuell dator

Sidan **ny erbjudande** för virtuella datorer öppnas på den första fliken med namnet **erbjudande inställningar**.  

![Ny erbjudande sida för virtuella datorer](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Fält för erbjudande inställningar

På fliken **erbjudande inställningar** måste du ange följande fält.  En asterisk (*) som lagts till i fält namnet anger att det är obligatoriskt. 

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***   | En unik identifierare (inom en utgivar profil) för erbjudandet. Den här identifieraren visas i produkt-URL: er, Azure Resource Manager mallar och fakturerings rapporter. Det får innehålla högst 50 tecken, och får bara bestå av gemena alfanumeriska tecken och bindestreck (-), men får inte sluta med ett bindestreck. Det här fältet kan inte ändras när ett erbjudande går live. <br> Om contoso till exempel publicerar ett erbjudande med ett erbjudande-ID **-exempel – VM**, tilldelas URL: en för Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Utgivare\***  | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden bör associeras med ditt utgivar-ID. Det här värdet kan inte ändras när erbjudandet har sparats. |
| **Namn\***       | Visnings namn för ditt erbjudande. Det här namnet visas i Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vägledning här är att inkludera ett identifierat varumärke för din produkt. Ta inte med din organisations namn här om det inte är så att det marknadsförs. Om du saluför det här erbjudandet i andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|   |   |
 
Klicka på **Spara** när du har angett alla fält. 


## <a name="next-steps"></a>Nästa steg

På nästa flik kommer du att lägga till [SKU: er](./cpp-skus-tab.md) i erbjudandet.
