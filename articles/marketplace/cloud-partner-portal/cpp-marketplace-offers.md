---
title: Azure och AppSource Marketplace-erbjudanden | Microsoft Docs
description: Skapa och hantera erbjudanden för Azure och AppSource marknadsplatser
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: ca4979188830fcb53732750a3eaadfc2009c4f9a
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658716"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure och AppSource Marketplace-erbjudanden

Den här första delen av det här avsnittet introducerar allmän statusöversikt används för att skapa och hantera erbjudanden för Azure och AppSource marknadsplatser.  Den här innehåller bakgrunden som du behöver känna till för att hantera specifika erbjudandetyper, samt teknisk information som är gemensamma för alla erbjuder typer.  Merparten av det här avsnittet innehåller detaljerad information om hur du skapar och hanterar specifika erbjudandetyper.  

Följande videoklipp beskriver de olika funktionerna och olika erbjudanden tillgängliga typer i Azure Marketplace eller AppSource.  Den behandlar också viktiga tekniska och företag aspekter av att publicera en applikation eller tjänst i dessa marknadsplatser.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Skapa appar och tjänster för Azure Marketplace och AppSource - skapa 2018**

Läs mer om dessa marknadsplatser [publiceringsguide för Azure Marketplace och AppSource](../marketplace-publishers-guide.md).


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace och AppSource erbjuder typer

I följande tabell visas de aktuella erbjudandetyper som stöds av den [Cloud Partner Portal](https://cloudpartner.azure.com).  För varje typ av erbjudande visar den marketplace(s) där erbjudandet kan anges som en allmän beskrivning av erbjudande lösning teknik.

|                Erbjudandetyp                |  Marketplace  |   Beskrivning                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-program](./azure-applications/cpp-azure-app-offer.md) | Azure | Lösningen består av en eller flera virtuella datorer (VM), valfri anpassad Azure kod, distribueras via en Azure Resource Manager-mall.  Distributionen kan vara antingen av en lösningsmall kunden eller hanteras av utgivaren. Den här typen används för att ge mer flexibilitet än den angivna virtuella datorn erbjudandetypen.  |
| [Konsulttjänst](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Båda | Microsoft kvalificerade konsulter kan visa sina domänspecifika tjänster på Azure Marketplace eller AppSource.  Sin expertis hjälper kunder bedöma deras problem eller skapa och distribuera lösningar för rätt att uppfylla sina affärsmål.  |
| [Behållare](./containers/cpp-containers-offer.md)  | Azure | Lösningen är en Docker-behållaravbildning som tillhandahålls som en Kubernetes-baserad tjänst eller Azure Container instances. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Ett paket som utökar den här Sage (Företagsresursplanering) och företag hanteringssystemet. |
| [Dynamics 365 för kundengagemang](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Ett paket som utökar den här kunden resurs (CRM) hanteringssystemet via dess försäljning, service, projekt-tjänsten och fältet service-moduler.  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Ett paket som utökar den här Företagsresursplanering service (Företagsresursplanering) som har stöd för avancerade ekonomi, åtgärder, tillverkning och hantering av leverantörskedja. |
| [IoT Edge-modul](./iot-edge-module/cpp-offer-process-parts.md) | Azure | En Docker-kompatibla behållare som körs på en IoT Edge-enhet.  Den innehåller små databaserad moduler som använder en kombination av anpassad kod, andra Azure-tjänster och tjänster 3 part. |
| [Power BI-appen](./power-bi/cpp-power-bi-offer.md) | AppSource | Ett paket som använder dataflöden för att ansluta rapporter och instrumentpaneler till data i vanliga datalagring. |
| [SaaS-app](./saas-app/cpp-saas-offer.md) | Azure | Lösningen är en programvara som tjänst-prenumeration som hanteras av utgivare, vilka användare logga in via ett anpassat gränssnitt som utnyttjar Azure Active Directory. |
| [Virtuell dator](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Lösningen ingår i en virtuell dator distribueras till kundens prenumeration.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Mer information finns i [publicera guide av erbjudandetypen](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Nästa steg

Du kommer lära dig om de allmänna åtgärder som du kan utföra på marketplace-erbjudanden och vanliga tekniska attribut och tillgångar i avsnittet [hantera erbjudanden](./manage-offers/cpp-manage-offers.md).
