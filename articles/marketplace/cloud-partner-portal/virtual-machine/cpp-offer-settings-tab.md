---
title: Fliken Erbjudande för virtuella datorer i Cloud Partner Portal för Azure Marketplace
description: Beskriver fliken Erbjudandeinställningar som används för att skapa ett Azure Marketplace VM-erbjudande.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 94be2e5d3c2c941ab17401a743ea86acbe8b6252
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273808"
---
# <a name="virtual-machine-offer-settings-tab"></a>Fliken Inställningar för erbjudande på virtuell dator

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Virtual Machine-erbjudande](https://aka.ms/CreateAzureVMoffer) för att hantera dina migrerade erbjudanden.

Sidan **Nytt erbjudande** för virtuella datorer öppnas på den första fliken med namnet **Erbjudandeinställningar**.  

![Ny erbjudandesida för virtuella datorer](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Fälten Offerinställningar

På fliken **Erbjudandeinställningar** måste du ange följande fält.  En bifogad asterisk (*) i fältnamnet anger att det krävs. 

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***   | En unik identifierare (i en utgivarprofil) för erbjudandet. Den här identifieraren visas i produkt-URL:er, Azure Resource Manager-mallar och faktureringsrapporter. Den har en maximal längd på 50 tecken, kan bara bestå av gemener alfanumeriska tecken och streck (-), men kan inte sluta i ett streck. Det går inte att ändra det här fältet när ett erbjudande har levts. <br> Om Contoso till exempel publicerar ett erbjudande med **exempel-VM för**erbjudande-ID tilldelas den Azure Marketplace-URL:en `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Publisher\***  | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden ska kopplas till ditt utgivar-ID. Det går inte att ändra det här värdet när erbjudandet har sparats. |
| **Namn\***       | Visa namn för ditt erbjudande. Det här namnet visas på Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vägledning här är att inkludera ett igenkännligt varumärke för din produkt. Ta inte med organisationens namn här om det inte är så det marknadsförs. Om du marknadsför detta erbjudande på andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|   |   |
 
Klicka på **Spara** när du har angett alla fält. 


## <a name="next-steps"></a>Nästa steg

På nästa flik lägger du till [SKU:er i](./cpp-skus-tab.md) erbjudandet.
