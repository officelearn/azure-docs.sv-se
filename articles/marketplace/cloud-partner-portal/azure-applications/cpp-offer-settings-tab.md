---
title: Inställningar för Azure-programerbjudande | Azure Marketplace
description: Konfigurera erbjudandeinställningarna för ett Azure-programerbjudande.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: d111471e909a091d8c887f13ad89d1da50cf72b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280124"
---
# <a name="azure-application-offer-settings-tab"></a>Fliken Inställningar för Azure-programerbjudande

I den här artikeln beskrivs hur du konfigurerar erbjudandeinställningarna för ett Azure-program.

Sidan **Azure Applications > Nytt erbjudande** öppnas med fokus på fliken **Erbjudandeinställningar.** En asterisk (*) som läggs till i fältnamnet anger att det krävs.

![Formuläret Offer identity](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Inställningar för erbjudandeidentitet

Under **Erbjudandeidentitet**måste du ange information för de fält som beskrivs i följande tabell.  

|    Field         |       Beskrivning                                                            |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***       | En unik identifierare (i en utgivarprofil) för erbjudandet. Den här identifieraren visas i produkt-URL:er och insiktersrapporter. Den har en maximal längd på 50 tecken och kan använda gemener alfanumeriska tecken och streck (-). (Identifieraren kan inte sluta med ett streck.) **Anm.:** Det går inte att ändra det här fältet när ett erbjudande har live. <br> Om Contoso till exempel publicerar ett erbjudande med **exempelbehållare för**erbjudande-ID `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`har den tilldelats Url:en för Azure Marketplace . |
| **Publisher-ID\***     | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden ska kopplas till ditt utgivar-ID. Det går inte att ändra det här värdet när erbjudandet har sparats. |
| **Namn\***          | Visningsnamnet för ditt erbjudande. Det här namnet visas på Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vi rekommenderar att du använder ett igenkännbart varumärke för din produkt. Ta inte med organisationens namn om det inte är så din produkt marknadsförs. Om du marknadsför detta erbjudande på andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|  |  |

Välj **Spara** om du vill spara erbjudandeinställningarna.

## <a name="next-steps"></a>Nästa steg

Använd fliken [SKU:er](./cpp-skus-tab.md) för att konfigurera SKU:erna för ditt erbjudande.
