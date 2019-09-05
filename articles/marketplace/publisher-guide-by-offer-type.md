---
title: Fastställa publicerings alternativ i Azure Marketplace | Azure Marketplace
description: Den här artikeln beskrivs kriterierna för kvalificering och publicera krav partner försöker förstå hur du publicerar appar på Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 11/13/2018
ms.author: pabutler
ms.openlocfilehash: 8a06573e6bcb2d5a6c6c97ad19e168f9c71aaa13
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308222"
---
# <a name="publishing-guide-by-offer-type"></a>Publiceringsguide efter erbjudandetyp

När du [bestämmer dig för ett publicerings alternativ](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)kan du välja den erbjudande typ som ska användas för att presentera ditt erbjudande. 

 *Erbjudande typen* definierar erbjudande strukturen, som innehåller de metadata, artefakter och annat innehåll som används för att presentera erbjudandet i Marketplace.

Innan du kan skapa ett erbjudande måste du välja en erbjudande typ. Erbjudande typen motsvarar den typ av lösning, app eller tjänst erbjudande som du vill publicera, samt dess justering för Microsofts produkter och tjänster. 

En enda erbjudande typ kan konfigureras på olika sätt för att möjliggöra olika publicerings alternativ, anrop till åtgärd, etablering eller prissättning. Publicerings alternativet och konfigurationen av erbjudande typen överensstämmer också med kravet på erbjudanden och tekniska krav. 

Se till att granska kraven för butik och erbjudande typ och de tekniska publicerings kraven innan du skapar erbjudandet.

## <a name="list-of-offer-types"></a>Lista över erbjudande typer

Erbjudanden för Azure Marketplace-erbjudanden visas i tabellen nedan.

| **Erbjudande typ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| [**Virtuella datorer**](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) | Använd den virtuella datorns erbjudande typ när du distribuerar en virtuell installation till prenumerationen som är kopplad till din kund. |
| [**Solution-mallar**](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates) | Använd lösnings mal len (även kallat Azure-program) som erbjudande typ när din lösning kräver ytterligare distribution och konfigurations automatisering utöver en enkel virtuell dator. Solution templates kan använda många olika typer av Azure-resurser, inklusive men inte begränsat till virtuella datorer.  |
| [**Hanterade program**](https://docs.microsoft.com/azure/marketplace/marketplace-managed-apps) | Använd Azure app: den hanterade appens erbjudande typ när följande villkor är uppfyllda: <br> <ul> <li> Du distribuerar antingen en prenumeration baserad lösning för din kund med antingen en virtuell dator eller en hel IaaS lösning. </li> <li>Du eller din kund kräver att lösningen hanteras av en partner. </li> <ul> |
| [**SaaS-program**](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide) | Använd SaaS-appens erbjudande typ för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som en prenumeration. |
| [**Container-erbjudanden**](https://docs.microsoft.com/azure/marketplace/marketplace-containers) | Använd containerns erbjudande typ när din lösning är en Docker-behållar avbildning som tillhandahålls som en Kubernetes-baserad Azure Container Service. |
| [**IoT Microsoft Edge-moduler**](https://docs.microsoft.com/azure/marketplace/iot-edge-module) | Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge, och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. |
| [**Konsult tjänster**](https://docs.microsoft.com/azure/marketplace/consulting-services) | Konsult tjänster hjälper till att ansluta kunder till tjänster som stöder och utökar användningen av Azure, Dynamics 365 eller Power Suite-tjänster.|


Slutligen kan du se [**krav för Azure AD**](https://docs.microsoft.com/azure/marketplace/enable-appsource-marketplace-using-azure-ad) genom att ange alternativ och erbjudanden för att få mer information om krav för enkel inloggning.

## <a name="next-steps"></a>Nästa steg

*   Granska krav för berättigande i publiceringsalternativ erbjudandet typ avsnittet för att slutföra valet av och konfigurationen av ditt erbjudande.
*   Granska publicering mönster genom butik för exempel på hur din lösning mappar till en typ av erbjudande och konfiguration.

