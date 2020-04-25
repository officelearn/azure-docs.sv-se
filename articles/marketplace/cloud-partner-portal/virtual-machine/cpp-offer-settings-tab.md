---
title: Fliken Inställningar för erbjud virtuell dator i Cloud Partner Portal för Azure Marketplace
description: Beskriver fliken erbjudande inställningar som används för att skapa ett erbjudande för virtuella Azure Marketplace-datorer.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: abeadf5f3c0a19212a12256e06602e840c9d1fa9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146817"
---
# <a name="virtual-machine-offer-settings-tab"></a>Fliken Inställningar för erbjud virtuell dator

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett erbjudande för virtuell Azure-dator](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

Sidan **ny erbjudande** för virtuella datorer öppnas på den första fliken med namnet **erbjudande inställningar**.  

![Ny erbjudande sida för virtuella datorer](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Fält för erbjudande inställningar

På fliken **erbjudande inställningar** måste du ange följande fält.  En asterisk (*) som lagts till i fält namnet anger att det är obligatoriskt. 

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***   | En unik identifierare (inom en utgivar profil) för erbjudandet. Den här identifieraren visas i produkt-URL: er, Azure Resource Manager mallar och fakturerings rapporter. Det får innehålla högst 50 tecken, och får bara bestå av gemena alfanumeriska tecken och bindestreck (-), men får inte sluta med ett bindestreck. Det här fältet kan inte ändras när ett erbjudande går live. <br> Om contoso till exempel publicerar ett erbjudande med ett erbjudande-ID **-exempel – VM**, tilldelas URL: en `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` för Azure Marketplace. |
| **Förläggare\***  | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden bör associeras med ditt utgivar-ID. Det här värdet kan inte ändras när erbjudandet har sparats. |
| **Name\***       | Visnings namn för ditt erbjudande. Det här namnet visas i Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vägledning här är att inkludera ett identifierat varumärke för din produkt. Ta inte med din organisations namn här om det inte är så att det marknadsförs. Om du saluför det här erbjudandet i andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|   |   |
 
Klicka på **Spara** när du har angett alla fält. 


## <a name="next-steps"></a>Nästa steg

På nästa flik kommer du att lägga till [SKU: er](./cpp-skus-tab.md) i erbjudandet.
