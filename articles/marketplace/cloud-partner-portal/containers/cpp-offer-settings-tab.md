---
title: Erbjudande inställningar för en Azure containers-avbildning | Azure Marketplace
description: Konfigurera erbjudande inställningar för en Azure-behållare.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 61d9fa535d2bec0a52351ba6199183ea899a0e1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146268"
---
# <a name="container-offer-settings-tab"></a>Fliken Inställningar för behållarens erbjudande

> [!IMPORTANT]
> Från den 13 april 2020 börjar vi flytta hanteringen av dina Azure Container-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett Azure Container-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) för att hantera dina migrerade erbjudanden.

Sidan **behållare > ny erbjudande** sida öppnas med fokus på fliken **erbjudande inställningar** . 

![Erbjudande identitet](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Ge identitets inställningar

Under **erbjudande identitet**måste du ange information för fälten som beskrivs i följande tabell. En asterisk (*) som läggs till i fält namnet visar att det är obligatoriskt. 

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***       | En unik identifierare (inom en utgivar profil) för erbjudandet. Den här identifieraren visas i produkt-URL: er och Insights-rapporter. Det får bestå av högst 50 tecken och kan använda gemena alfanumeriska tecken och bindestreck (-). (Identifieraren kan inte sluta med ett bindestreck.) **Obs:** Det här fältet kan inte ändras när ett erbjudande går live. <br> Om contoso till exempel publicerar ett erbjudande med **exempel behållaren erbjudande-** ID, tilldelas den URL: en `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`för Azure Marketplace. |
| **Utgivar-ID\***     | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden bör associeras med ditt utgivar-ID. Det här värdet kan inte ändras efter att erbjudandet har sparats. |
| **Name\***          | Ditt erbjudandes visnings namn. Det här namnet visas på Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vi rekommenderar att du använder ett identifierbart varumärkes namn för din produkt. Ta inte med din organisations namn om det inte är så här som produkten marknadsförs. Om du saluför det här erbjudandet i andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|  |  |

Spara inställningarna för erbjudandet genom att välja **Spara** .


## <a name="next-steps"></a>Nästa steg

Använd fliken [SKU: er](./cpp-skus-tab.md) för att konfigurera SKU: er för ditt erbjudande.
