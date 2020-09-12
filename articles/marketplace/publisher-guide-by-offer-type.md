---
title: Publicerings guide efter erbjudande typ – Microsoft Commercial Marketplace
description: I den här artikeln beskrivs de erbjudande typer som är tillgängliga på Microsofts kommersiella marknads platser.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: 565ad4267bcdab095a85ab537c10728981eadb0d
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484161"
---
# <a name="publishing-guide-by-offer-type"></a>Publiceringsguide efter erbjudandetyp

I den här artikeln beskrivs de erbjudande typer som är tillgängliga i den kommersiella marknads platsen. *Erbjudande typen* definierar erbjudande strukturen, som innehåller metadata, artefakter och annat innehåll som presenteras i den kommersiella marknads platsen.

När du har [bestämt dig för ett publicerings alternativ](determine-your-listing-type.md)måste du välja en erbjudande typ innan du börjar skapa ditt erbjudande. Erbjudande typen motsvarar den typ av lösning, app eller tjänst erbjudande som du vill publicera, samt dess justering för Microsofts produkter och tjänster.

Du kan konfigurera en enda erbjudande typ på olika sätt för att aktivera olika publicerings alternativ, list alternativ, etablering eller prissättning. Publicerings alternativet och konfigurationen av erbjudande typen överensstämmer också med kravet på erbjudanden och tekniska krav.

Se till att läsa igenom butiken för onlinebutik och erbjuda typ av krav och de tekniska publicerings kraven innan du skapar erbjudandet.

## <a name="list-of-offer-types"></a>Lista över erbjudande typer

Erbjudanden för Azure Marketplace-erbjudanden visas i tabellen nedan.

| **Erbjudande typ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| [**Virtuella datorer**](./marketplace-virtual-machines.md) | Använd den virtuella datorns erbjudande typ när du distribuerar en virtuell installation till prenumerationen som är kopplad till din kund. |
| [**Lösningsmallar**](./marketplace-solution-templates.md) | Använd lösnings mal len (även kallat Azure-program) som erbjudande typ när din lösning kräver ytterligare distribution och konfigurations automatisering utöver en enskild virtuell dator. Solution templates kan använda många olika typer av Azure-resurser, inklusive men inte begränsat till virtuella datorer.  |
| [**Hanterade program**](./marketplace-managed-apps.md) | Använd Azure app: den hanterade appens erbjudande typ när följande villkor är uppfyllda: <br> <ul> <li> Du distribuerar antingen en prenumeration baserad lösning för din kund med antingen en virtuell dator eller en hel IaaS lösning. </li> <li>Du eller din kund kräver att lösningen hanteras av en partner. </li> <ul> |
| [**SaaS**](plan-saas-offer.md) | Använd SaaS-typen (Software as a Service) för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som en prenumeration. |
| [**Container-erbjudanden**](./marketplace-containers.md) | Använd containerns erbjudande typ när din lösning är en Docker-behållar avbildning som tillhandahålls som en Kubernetes-baserad Azure Container Service. |
| [**Azure IoT Edge moduler**](./iot-edge-module.md) | Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge, och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. |
| [**Konsulttjänster**](./consulting-services.md) | Konsult tjänster hjälper till att ansluta kunder till tjänster som stöder och utökar användningen av Azure, Dynamics 365 eller Power Suite-tjänster.|
| [**Microsoft 365, Dynamics 365 och Power BI**](./appsource-offer-publishing-guide.md) | Du kan publicera AppSource-erbjudanden som bygger på eller utökar Dynamics 365, Microsoft 365, Power BI och Power Apps.|
| [**Integrerade lösningar**](./integrated-solutions-for-publishers.md) | Du kan publicera integrerade, branschledande lösningar som kombinerar teknik och tjänster som ett enda erbjudande.|

Information om krav för enkel inloggning genom att lista alternativ och erbjudande typer finns i [**krav för Azure AD**](./enable-appsource-marketplace-using-azure-ad.md).

## <a name="next-steps"></a>Nästa steg

- Granska kraven för berättigande i motsvarande artikel för din erbjudande typ för att slutföra valet och konfigurationen av ditt erbjudande.
- Granska publicerings mönstren för varje onlinebutik för att få exempel på hur din lösning mappas till en erbjudande typ och konfiguration.
