---
title: Fastställa din publicering alternativ i Azure Marketplace | Azure
description: Den här artikeln beskrivs kriterierna för kvalificering och publicera krav partner försöker förstå hur du publicerar appar på Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/13/2018
ms.author: qianw211
ms.openlocfilehash: 9dd5b3232184170a59b82d5cafdc1a4cdcb4c172
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728003"
---
# <a name="publishing-guide-by-offer-type"></a>Publiceringsguide efter erbjudandetyp

När du [besluta om en publiceringsalternativ](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type), du är redo att välja den typ av erbjudande som används för att presentera ditt erbjudande. 

Erbjudandet är det publicera objekt som du ska skapa, publicera och hantera i den [Cloud Partner Portal](https://cloudpartner.azure.com). Den *erbjudandetyp* definierar strukturen för erbjudandet, som innehåller metadata, artefakter och annat innehåll som används för att presentera erbjudandet i marketplace.

Innan du kan skapa ett erbjudande, måste du välja en typ av erbjudande. Typ av erbjudande motsvarar typ av lösning, app, eller tjänsterbjudande som du önskar att publicera, samt dess justering till Microsoft-produkter och tjänster. 

En typ av erbjudande kan konfigureras på olika sätt i Cloud Partner Portal att aktivera olika publiceringsalternativ anrop till åtgärden, etablering och priser. Publicering alternativet och konfigurationen av typ av erbjudande också justera erbjudandet behörighet och tekniska krav. 

Glöm inte att granska storefront och erbjuda typ behörighetskraven och de tekniska kraven för publicering innan du skapar ditt erbjudande.

## <a name="list-of-offer-types"></a>Lista över typer av erbjudanden

Typer av Azure Marketplace-erbjudanden visas i tabellen nedan.

| **Erbjudandetyp**    | **Beskrivning**  |
| :------------------- | :-------------------|
| [**Virtuella datorer**](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) | Använd typ av erbjudande för virtuell dator när du distribuerar en virtuell installation till den prenumeration som är associerad med kunden. |
| [**Lösningsmallar**](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates) | Använd lösning erbjudandet malltyp när din lösning kräver ytterligare distribution och konfiguration av automation utöver en enkel virtuell dator. |
| [**Hanterade program**](https://docs.microsoft.com/azure/marketplace/marketplace-managed-apps) | Använda Azure-appen: hanterade erbjudande apptyp när följande villkor krävs: <br> <ul> <li> Du distribuerar antingen en prenumeration-baserad lösning för din kund som använder en virtuell dator eller en hel IaaS-baserad lösning. </li> <li>Du eller dina kunder kräver att lösningen hanteras av en partner. </li> <ul> |
| [**SaaS-program**](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide) | Aktivera kunden att köpa din SaaS-baserade, teknisk lösning som en prenumeration med hjälp av erbjudandetypen för SaaS-app. |
| [**Behållare-erbjudanden**](https://docs.microsoft.com/azure/marketplace/marketplace-containers) | Använd typ av erbjudande behållaren när din lösning är en Docker-behållaravbildning som tillhandahålls som en Kubernetes-baserade Azure-behållartjänst. |
| [**IoT Microsoft Edge-moduler**](https://docs.microsoft.com/azure/marketplace/iot-edge-module) | Azure IoT Edge-moduler är de minsta beräkning enheter som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster 3 part eller din egen Lösningsspecifika kod. |
| [**Konsulttjänster**](https://docs.microsoft.com/azure/marketplace/consulting-services) | Konsulttjänster på Azure Marketplace hjälpa till att ansluta kunder med stöd för och utöka deras användning av Azure-tjänster. |
| [**AppSource-erbjudanden**](https://docs.microsoft.com/azure/marketplace/appsource-offer-publishing-guide) | AppSource-storefront innehåller erbjudanden som bygger på eller utöka Dynamics 365, Office 365, Power BI och Power Apps. |

Slutligen finns i [ **Azure AD-kraven** ](https://docs.microsoft.com/azure/marketplace/enable-appsource-marketplace-using-azure-ad) genom att visa en lista över alternativ och tillhandahåller mer information om kraven för enkel inloggning.

## <a name="next-steps"></a>Nästa steg

*   Granska krav för berättigande i publiceringsalternativ erbjudandet typ avsnittet för att slutföra valet av och konfigurationen av ditt erbjudande.
*   Granska publicering mönster genom butik för exempel på hur din lösning mappar till en typ av erbjudande och konfiguration.
*   Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com) du skapar och konfigurerar ditt erbjudande.
