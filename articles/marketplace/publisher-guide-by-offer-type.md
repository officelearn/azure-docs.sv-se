---
title: Publicerings guide efter erbjudande typ – Microsoft Commercial Marketplace
description: I den här artikeln beskrivs de erbjudande typer som är tillgängliga på Microsofts kommersiella marknads platser.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 6e889fef16d8ad80d52810207f0328a765ca9cfe
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958505"
---
# <a name="publishing-guide-by-offer-type"></a>Publiceringsguide efter erbjudandetyp

I den här artikeln beskrivs de erbjudande typer som är tillgängliga i den kommersiella marknads platsen. *Erbjudande typen* definierar erbjudande strukturen, som innehåller de metadata, artefakter och annat innehåll som används för att presentera erbjudandet i Marketplace.

Innan du kan skapa ett erbjudande, och när du har [bestämt dig för ett publicerings alternativ](determine-your-listing-type.md), måste du välja en erbjudande typ som ska användas för att presentera ditt erbjudande. Erbjudande typen motsvarar den typ av lösning, app eller tjänst erbjudande som du vill publicera, samt dess justering för Microsofts produkter och tjänster.

En enda erbjudande typ kan konfigureras på olika sätt för att möjliggöra olika publicerings alternativ, anrop till åtgärd, etablering eller prissättning. Publicerings alternativet och konfigurationen av erbjudande typen överensstämmer också med kravet på erbjudanden och tekniska krav.

Se till att granska kraven för butik och erbjudande typ och de tekniska publicerings kraven innan du skapar erbjudandet.

## <a name="list-of-offer-types"></a>Lista över erbjudande typer

Erbjudanden för Azure Marketplace-erbjudanden visas i tabellen nedan.

| **Erbjudande typ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| [**Virtuella datorer**](./marketplace-virtual-machines.md) | Använd den virtuella datorns erbjudande typ när du distribuerar en virtuell installation till prenumerationen som är kopplad till din kund. |
| [**Lösningsmallar**](./marketplace-solution-templates.md) | Använd lösnings mal len (även kallat Azure-program) som erbjudande typ när din lösning kräver ytterligare distribution och konfigurations automatisering utöver en enskild virtuell dator. Solution templates kan använda många olika typer av Azure-resurser, inklusive men inte begränsat till virtuella datorer.  |
| [**Hanterade program**](./marketplace-managed-apps.md) | Använd Azure app: den hanterade appens erbjudande typ när följande villkor är uppfyllda: <br> <ul> <li> Du distribuerar antingen en prenumeration baserad lösning för din kund med antingen en virtuell dator eller en hel IaaS lösning. </li> <li>Du eller din kund kräver att lösningen hanteras av en partner. </li> <ul> |
| [**SaaS-program**](./partner-center-portal/create-new-saas-offer.md) | Använd SaaS-appens erbjudande typ för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som en prenumeration. |
| [**Container-erbjudanden**](./marketplace-containers.md) | Använd containerns erbjudande typ när din lösning är en Docker-behållar avbildning som tillhandahålls som en Kubernetes-baserad Azure Container Service. |
| [**Azure IoT Edge moduler**](./iot-edge-module.md) | Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge, och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. |
| [**Konsulttjänster**](./consulting-services.md) | Konsult tjänster hjälper till att ansluta kunder till tjänster som stöder och utökar användningen av Azure, Dynamics 365 eller Power Suite-tjänster.|
| [**Office 365, Dynamics 365 och Power BI**](./appsource-offer-publishing-guide.md) | Du kan publicera AppSource-erbjudanden som bygger på eller utökar Dynamics 365, Office 365, Power BI och Power Apps.|
| [**Integrerade lösningar**](./integrated-solutions-for-publishers.md) | Du kan publicera integrerade, branschledande lösningar som kombinerar teknik och tjänster som ett enda erbjudande.|

Information om krav för enkel inloggning genom att lista alternativ och erbjudande typer finns i [**krav för Azure AD**](./enable-appsource-marketplace-using-azure-ad.md).

## <a name="next-steps"></a>Nästa steg

- Granska behörighets kraven i motsvarande artikel för din erbjudande typ (följande avsnitt) för att slutföra valet och konfigurationen av ditt erbjudande.
- Granska publicerings mönstren efter butik för exempel på hur din lösning mappar till en erbjudande typ och konfiguration.
