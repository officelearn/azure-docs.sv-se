---
title: Erbjudandeinställningar för en Azure Containers-avbildning | Azure Marketplace
description: Konfigurera erbjudandeinställningar för en Azure-behållare.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: dfdc588ffe3ca8c0f21e4111b6f4c66de162299d
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270187"
---
# <a name="container-offer-settings-tab"></a>Fliken Inställningar för behållarerbjudande

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Container-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Container-erbjudande](https://aka.ms/CreateContainerOffer) för att hantera dina migrerade erbjudanden.

Sidan **Behållare > nytt erbjudande** öppnas med fokus på fliken **Erbjudandeinställningar.** 

![Offer identitet](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Inställningar för erbjudandeidentitet

Under **Erbjudandeidentitet**måste du ange information för de fält som beskrivs i följande tabell. En asterisk (*) som läggs till i fältnamnet anger att det krävs. 

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***       | En unik identifierare (i en utgivarprofil) för erbjudandet. Den här identifieraren visas i produkt-URL:er och insiktersrapporter. Den har en maximal längd på 50 tecken och kan använda gemener alfanumeriska tecken och streck (-). (Identifieraren kan inte sluta med ett streck.) **Anm.:** Det går inte att ändra det här fältet när ett erbjudande har live. <br> Om Contoso till exempel publicerar ett erbjudande med **exempelbehållare för**erbjudande-ID `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`har den tilldelats Url:en för Azure Marketplace . |
| **Publisher-ID\***     | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden ska kopplas till ditt utgivar-ID. Det går inte att ändra det här värdet när erbjudandet har sparats. |
| **Namn\***          | Visningsnamnet för ditt erbjudande. Det här namnet visas på Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vi rekommenderar att du använder ett igenkännbart varumärke för din produkt. Ta inte med organisationens namn om det inte är så din produkt marknadsförs. Om du marknadsför detta erbjudande på andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|  |  |

Välj **Spara** om du vill spara erbjudandeinställningarna.


## <a name="next-steps"></a>Nästa steg

Använd fliken [SKU:er](./cpp-skus-tab.md) för att konfigurera SKU:erna för ditt erbjudande.
