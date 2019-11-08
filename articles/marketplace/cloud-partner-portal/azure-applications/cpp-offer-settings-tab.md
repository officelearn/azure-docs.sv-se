---
title: Inställningar för Azure Application-erbjudande | Azure Marketplace
description: Konfigurera erbjudande inställningarna för ett erbjudande för Azure-program.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: c5e00f77ffeec61fb4fee4b4e8eb72b7c7ed1077
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827531"
---
# <a name="azure-application-offer-settings-tab"></a>Fliken Inställningar för Azure Application-erbjudande

Den här artikeln beskriver hur du konfigurerar erbjudande inställningarna för ett Azure-program.

Sidan **Azure-program > ny erbjudande** öppnas med fokus på fliken **erbjudande inställningar** . En asterisk (*) som läggs till i fält namnet visar att det är obligatoriskt.

![Formulär för erbjudande identitet](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Ge identitets inställningar

Under **erbjudande identitet**måste du ange information för fälten som beskrivs i följande tabell.  

|    Fält         |       Beskrivning                                                            |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***       | En unik identifierare (inom en utgivar profil) för erbjudandet. Den här identifieraren visas i produkt-URL: er och Insights-rapporter. Det får bestå av högst 50 tecken och kan använda gemena alfanumeriska tecken och bindestreck (-). (Identifieraren kan inte sluta med ett bindestreck.) **Obs:** Det här fältet kan inte ändras när ett erbjudande går live. <br> Om contoso till exempel publicerar ett erbjudande med **exempel behållare**för erbjudande-ID, tilldelas URL: en för Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **Utgivar-ID\***     | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden bör associeras med ditt utgivar-ID. Det här värdet kan inte ändras efter att erbjudandet har sparats. |
| **Namn\***          | Ditt erbjudandes visnings namn. Det här namnet visas på Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vi rekommenderar att du använder ett identifierbart varumärkes namn för din produkt. Ta inte med din organisations namn om det inte är så här som produkten marknadsförs. Om du saluför det här erbjudandet i andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|  |  |

Spara inställningarna för erbjudandet genom att välja **Spara** .

## <a name="next-steps"></a>Nästa steg

Använd fliken [SKU: er](./cpp-skus-tab.md) för att konfigurera SKU: er för ditt erbjudande.