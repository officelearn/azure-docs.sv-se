---
title: Azure-och AppSource Marketplace-erbjudanden
description: Skapa och hantera erbjudanden för Azure-och AppSource-marknadsplatser
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278492"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure-och AppSource Marketplace-erbjudanden

Den första delen av det här avsnittet beskriver de allmänna åtgärder som används för att skapa och hantera erbjudanden för Azure-och AppSource-Marketplace.  Den här delen ger den bakgrund som du behöver förstå för att hantera vissa erbjudande typer, samt teknisk information som är gemensam för alla typer av erbjudanden.  Merparten av det här avsnittet innehåller detaljerade anvisningar om hur du skapar och hanterar särskilda erbjudande typer.  

Följande video innehåller de olika funktionerna och olika typer av erbjudanden som finns tillgängliga i Azure Marketplace eller AppSource.  Den omfattar också viktiga tekniska och affärs aspekter av publicering av ett program eller en tjänst på dessa marknads platser.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Skapa appar och tjänster för Azure Marketplace och AppSource – build 2018**

Mer information om dessa marknads platser finns i [publicerings guiden för Azure Marketplace och AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Vanliga erbjudande åtgärder

Processen för att skapa ett nytt erbjudande varierar kraftigt mellan erbjudande typer, till exempel mellan ett [erbjudande för Azure-program](./azure-applications/cpp-azure-app-offer.md) och ett [konsult tjänst erbjudande](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Dessutom är många av de andra åtgärderna som du utför i ett erbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com) ganska standardiserade över erbjudande typer.  Dessa vanliga åtgärder – till exempel publicera, Visa status, uppdatera och ta bort – beskrivs i avsnittet [Hantera erbjudanden](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Test Drive

*Test Drive* är en Marketplace-funktion som ger kunderna "prova innan du köper" demonstrations alternativ för varje erbjudande som är aktiverat.  Test enhets kapaciteten är begränsad till följande delmängd av erbjudande typer: [Azure-program](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 för kund engagemang](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 för finanser och drift](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS-program](./saas-app/cpp-saas-offer.md)och [virtuella datorer](./virtual-machine/cpp-virtual-machine-offer.md).  Den här funktionen kräver att utgivaren skapar en mall för en testenhet som är anpassad för erbjudandet.  Mer information finns i avsnittet [Test Drive](./test-drive/what-is-test-drive.md).

Du kan söka i befintliga Marketplace-erbjudanden som har test enhets demonstrationer genom att använda [filtret test enhet](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Erbjudande typer för Azure Marketplace och AppSource

I följande tabell visas de aktuella erbjudande typerna som stöds av [Cloud Partner Portal](https://cloudpartner.azure.com).  För varje erbjudande typ listar den de Marketplace-platser där erbjudandet kan visas, samt en allmän beskrivning av erbjudande lösnings tekniken.

|                Erbjudande typ                |  Marketplace  |   Beskrivning                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-program](./azure-applications/cpp-azure-app-offer.md) | Azure | Lösningen består av en eller flera virtuella datorer (VM), valfri anpassad Azure-kod som distribueras via en Azure Resource Manager-mall.  Distributionen kan antingen vara kunden via en lösnings mall eller hanteras av utgivaren. Den här typen används för att ge mer flexibilitet än den angivna typen av erbjudande för virtuella datorer.  |
| [Konsult tjänst](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | dubbelrikta | Microsoft-kvalificerade konsulter kan lista sina företagsspecifika tjänster på antingen Azure Marketplace eller AppSource.  Deras expertis hjälper kunder att bedöma sina problem och skapa och distribuera rätt lösningar för att uppfylla sina affärs mål.  |
| [Container](./containers/cpp-containers-offer.md)  | Azure | Lösningen är en Docker-behållar avbildning etablerad som antingen en Kubernetes tjänst eller Azure Container instances. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Ett paket som utökar den här företags resurs planeringen (ERP) och företags hanterings systemet. |
| [Dynamics 365 för Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Ett paket som utökar detta CRM-system (Customer Resource Management) med hjälp av modulerna Sales, service, Project service och Field service  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Ett paket som utökar den här ERP-tjänsten (Enterprise Resource Planning) som har stöd för avancerad finans-, drift-, tillverknings-och leverans kedjas hantering |
| [IoT Edge modul](./iot-edge-module/cpp-offer-process-parts.md) | Azure | En Docker-kompatibel behållare som körs på en IoT Edge enhet.  Den består av små beräknings moduler som använder en kombination av anpassad kod, andra Azure-tjänster och tjänster från tredje part. |
| [Power BI app](./power-bi/cpp-power-bi-offer.md) | AppSource | En Power BI app som paketerar anpassningsbar Power BI innehåll, inklusive data uppsättningar, rapporter och instrument paneler |
| [SaaS-app](./saas-app/cpp-saas-offer.md) | Azure | Lösningen är en prenumeration på program vara som en tjänst, som hanteras av utgivaren, som användarna loggar in via ett anpassat gränssnitt som använder Azure Active Directory. |
| [Virtuell dator](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Lösningen finns i en enda virtuell dator som distribuerats till kundens prenumeration.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Mer information finns i [publicerings guide efter erbjudande typ](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Nästa steg

Du får lära dig om de allmänna åtgärder som du kan utföra på Marketplace-erbjudanden och deras gemensamma tekniska attribut och till gångar i artikeln [Hantera erbjudanden](./manage-offers/cpp-manage-offers.md).
