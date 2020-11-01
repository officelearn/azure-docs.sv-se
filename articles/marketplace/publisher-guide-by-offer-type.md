---
title: Publicerings guide efter erbjudande typ – Microsoft Commercial Marketplace
description: I den här artikeln beskrivs de erbjudande typer som är tillgängliga på Microsofts kommersiella marknads platser.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/06/2020
ms.openlocfilehash: 0d22e10a71c3eee2026f55a73074ba02c68c2b78
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146185"
---
# <a name="publishing-guide-by-offer-type"></a>Publiceringsguide efter erbjudandetyp

I den här artikeln beskrivs de erbjudande typer som är tillgängliga i den kommersiella marknads platsen. *Erbjudande typen* definierar erbjudande strukturen, som innehåller metadata, artefakter och annat innehåll som presenteras i den kommersiella marknads platsen.

När du har [bestämt dig för ett publicerings alternativ](determine-your-listing-type.md)måste du välja en erbjudande typ innan du börjar skapa ditt erbjudande i Partner Center. Erbjudande typen motsvarar den typ av lösning, app eller tjänst erbjudande som du vill publicera, samt dess justering för Microsofts produkter och tjänster.

Du kan konfigurera en enda erbjudande typ på olika sätt för att aktivera olika publicerings alternativ, list alternativ, etablering eller prissättning. Publicerings alternativet och konfigurationen av erbjudande typen överensstämmer också med kravet på erbjudanden och tekniska krav.

Se till att läsa igenom butiken för onlinebutik och erbjuda typ av krav och de tekniska publicerings kraven innan du skapar erbjudandet.

## <a name="list-of-offer-types"></a>Lista över erbjudande typer

I följande tabell visas de kommersiella Marketplace-erbjudande typerna i Partner Center.

| **Erbjudande typ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| [Azure Application: **hanterat program**](marketplace-managed-apps.md) | Använd typen Azure Application: erbjudande för hanterade program när följande villkor är uppfyllda: <br> <ul> <li>Du distribuerar antingen en prenumeration baserad lösning för din kund med antingen en virtuell dator eller en hel IaaS lösning. </li> <li>Du eller din kund kräver att lösningen hanteras av en partner. </li> <ul> |
| [Azure Application: **lösnings mal len**](marketplace-solution-templates.md) | Använd typ av lösnings mal len Azure Application: när din lösning kräver ytterligare distribution och konfigurations automatisering utöver en enskild virtuell dator. Solution templates kan använda många olika typer av Azure-resurser, inklusive men inte begränsat till virtuella datorer.  |
  | [**Azure-behållare**](marketplace-containers.md) | Använd typen av Azure Container-erbjudande när din lösning är en Docker-behållar avbildning som tillhandahålls som en Kubernetes-baserad Azure Container Service. |
| [**Virtuell Azure-dator**](marketplace-virtual-machines.md) | Använd den virtuella datorns erbjudande typ när du distribuerar en virtuell installation till prenumerationen som är kopplad till din kund. |
| [**Konsult tjänst**](consulting-services.md) | Konsult tjänster hjälper till att ansluta kunder till tjänster som stöder och utökar användningen av Azure, Dynamics 365 eller Power Suite-tjänster.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Du kan publicera AppSource-erbjudanden som bygger på eller utökar Dynamics 365 Business Central, Dynamics 365 kund engagemang, Power Apps och finans-och verksamhets appar.|
| [**IoT Edge modul**](iot-edge-module.md) | Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge, och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. |
| [**Hanterad tjänst**](partner-center-portal/create-new-managed-service-offer.md) | Du kan skapa hanterade tjänster och hantera kund delegerade prenumerationer eller resurs grupper via [Azure Lighthouse](../lighthouse/overview.md).|
| [**Power BI app** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Du kan publicera AppSource-erbjudanden som bygger på eller utökar Power BI och Microsoft 365.|
| [**Program vara som en tjänst**](plan-saas-offer.md) | Använd SaaS-typen (Software as a Service) för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som en prenumeration. Information om krav för enkel inloggning för SaaS-erbjudanden finns i [Azure AD och transactable SaaS-erbjudanden på den kommersiella marknads platsen](azure-ad-saas.md). |


## <a name="next-steps"></a>Nästa steg

- Granska kraven för berättigande i motsvarande artikel för din erbjudande typ för att slutföra valet och konfigurationen av ditt erbjudande.
- Granska publicerings mönstren för varje onlinebutik för att få exempel på hur din lösning mappas till en erbjudande typ och konfiguration.