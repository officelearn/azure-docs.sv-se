---
title: Azure SaaS-erbjudande programinställningar | Azure Marketplace
description: Konfigurera erbjuder inställningar för erbjudande för SaaS-program på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6903226ecfe1478b340e390c783c4e57af778f3e
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943527"
---
# <a name="saas-application-offer-settings-tab"></a>SaaS-program erbjuder inställningsflik

Den här artikeln beskriver hur du konfigurerar inställningarna för erbjudande för erbjudandet.

Den **SaaS-App > nytt erbjudande** öppnas med fokus på den **erbjuder inställningar** fliken. 

Använd fliken erbjuder inställningar för att konfigurera den **erbjuder identitet**, visas i nästa skärmdump. En asterisk (*) läggs till fältnamnet anger att det krävs.

![Fliken Erbjudandeinställningar](./media/saas-new-offer.png)

## <a name="offer-identity-settings"></a>Inställningar för erbjudande-ID

Under erbjuder identitet, måste du ange information för fälten som beskrivs i följande tabell. Required fields are indicted by an asterisk (*).

|    Fältnamn      |    Beskrivning    |
|  ---------------   |  ---------------  |
|  **Erbjudande-ID\***    |  En unik identifierare för erbjudandet i en utgivarprofil för. Detta ID syns i URL: er i produkten och fakturering rapporter. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett tankstreck och är begränsat till högst 50 tecken. Observera att det här fältet är låst när ett erbjudande lanseras. Till exempel om en utgivare, Contoso, publicerar ett erbjudande med erbjudande-ID exempel-vm, den visas i Azure marketplace som: https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview.                 |
|  **Utgivar-ID\***    |  Publicerings-ID är den unika identifieraren i Marketplace. Alla dina erbjudanden ska vara anslutna publisher-ID. Publicerings-ID kan inte ändras efter erbjudandets sparas...                |
|  **Namn\***      |   Det här är visningsnamnet för ditt erbjudande. Det här är det namn som visas i Azure Marketplace och i Azure Portal. Det får innehålla högst 50 tecken. Ange ett igenkännligt varumärke för produkten. Omfattar inte ditt företagsnamn, såvida inte som är hur den släpps. Om du marknadsföring erbjudandet på din egen webbplats, se till att namnet exakt hur den visas på din webbplats.               |
|  |  |

Välj **spara** spara förloppet.

## <a name="next-steps"></a>Nästa steg

[Fliken Teknisk information](./cpp-technical-info-tab.md)
