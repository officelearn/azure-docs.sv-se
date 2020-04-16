---
title: Microsofts kommersiella marknadsplats publiceringsguide efter erbjudandetyp
description: I den här artikeln beskrivs de erbjudandetyper som är tillgängliga på Microsofts kommersiella marknadsplats.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 6521f19ea566719fee39c238adda8e3a068e2c25
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419297"
---
# <a name="publishing-guide-by-offer-type"></a>Publiceringsguide efter erbjudandetyp

När du [har bestämt dig för ett publiceringsalternativ](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)är du redo att välja den erbjudandetyp som ska användas för att presentera erbjudandet.

 *Erbjudandetypen* definierar erbjudandestrukturen, som innehåller metadata, artefakter och annat innehåll som används för att presentera erbjudandet på marketplace.

Innan du kan skapa ett erbjudande måste du välja en erbjudandetyp. Erbjudandetypen motsvarar den typ av lösning, app eller tjänsterbjudande som du vill publicera, samt dess anpassning till Microsofts produkter och tjänster.

En enda erbjudandetyp kan konfigureras på olika sätt för att aktivera olika publiceringsalternativ, uppmaningar, etablering eller prissättning. Publiceringsalternativet och konfigurationen av erbjudandetypen anpassar sig också till erbjudandets berättigande och tekniska krav.

Var noga med att granska skyltfönster och erbjuda typ behörighetskrav och de tekniska publiceringskraven innan du skapar ditt erbjudande.

## <a name="list-of-offer-types"></a>Lista över erbjudandetyper

Azure Marketplace-erbjudandetyper visas i tabellen nedan.

| **Typ av erbjudande**    | **Beskrivning**  |
| :------------------- | :-------------------|
| [**Virtuella datorer**](./marketplace-virtual-machines.md) | Använd erbjudandetypen för den virtuella datorn när du distribuerar en virtuell installation till prenumerationen som är kopplad till kunden. |
| [**Lösningsmallar**](./marketplace-solution-templates.md) | Använd lösningsmallen (kallas även Azure-program) erbjudandetyp när din lösning kräver ytterligare distribution och konfigurationsautomatisering utöver en enda virtuell dator. Lösningsmallar kan använda många olika typer av Azure-resurser, inklusive men inte begränsat till virtuella datorer.  |
| [**Hanterade program**](./marketplace-managed-apps.md) | Använd Azure-appen: hanterad apperbjudandetyp när följande villkor krävs: <br> <ul> <li> Du distribuerar antingen en prenumerationsbaserad lösning för kunden med antingen en virtuell dator eller en hel IaaS-baserad lösning. </li> <li>Du eller din kund kräver att lösningen hanteras av en partner. </li> <ul> |
| [**SaaS-program**](./marketplace-saas-applications-technical-publishing-guide.md) | Använd erbjudandetypen SaaS-appen för att göra det möjligt för kunden att köpa din SaaS-baserade, tekniska lösning som prenumeration. |
| [**Container erbjudanden**](./marketplace-containers.md) | Använd erbjudandetypen Behållare när din lösning är en Docker-behållaravbildning som etablerats som en Kubernetes-baserad Azure-behållartjänst. |
| [**Azure IoT Edge-moduler**](./iot-edge-module.md) | Azure IoT Edge-moduler är de minsta beräkningsenheterna som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösningsspecifika kod. |
| [**Konsulttjänster**](./consulting-services.md) | Konsulttjänster hjälper till att ansluta kunder till tjänster för att stödja och utöka deras användning av Azure-, Dynamics 365- eller Power Suite-tjänster.|
| [**Office 365, Dynamics 365 och Power BI**](./appsource-offer-publishing-guide.md) | Du kan publicera AppSource-erbjudanden som bygger på eller utökar Dynamics 365, Office 365, Power BI och Power Apps.|
| [**Integrerade lösningar**](./integrated-solutions-for-publishers.md) | Du kan publicera integrerade, branschrejade lösningar som kombinerar teknik och tjänster som ett enda erbjudande.|

Information om enstaka inloggningskrav genom att lista alternativ och erbjudandetyper finns i [**Azure AD-krav**](./enable-appsource-marketplace-using-azure-ad.md).

## <a name="next-steps"></a>Nästa steg

- Granska behörighetskraven i motsvarande artikel för din erbjudandetyp (följande avsnitt) för att slutföra valet och konfigurationen av ditt erbjudande.
- Läs publiceringsmönstren efter skyltfönster för exempel på hur lösningen mappar till en erbjudandetyp och konfiguration.
