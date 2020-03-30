---
title: Azure- och AppSource Marketplace-erbjudanden
description: Skapa och hantera erbjudanden för Azure- och AppSource Marketplaces
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278492"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure- och AppSource Marketplace-erbjudanden

I den här första delen av det här avsnittet beskrivs de allmänna åtgärder som används för att skapa och hantera erbjudanden för Azure- och AppSource Marketplaces.  Den här delen innehåller den bakgrund du behöver förstå för att hantera specifika erbjudandetyper, samt teknisk information som är gemensam för alla erbjudandetyper.  Det mesta av det här avsnittet innehåller detaljerade instruktioner om hur du skapar och hanterar specifika erbjudandetyper.  

Följande video introducerar de olika funktionerna och olika erbjudandetyper som är tillgängliga i Azure Marketplace eller AppSource.  Det omfattar också viktiga tekniska och affärsmässiga aspekter av att publicera ett program eller en tjänst på dessa marknadsplatser.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Skapa appar och tjänster för Azure Marketplace och AppSource – Build 2018**

Mer information om dessa marknadsplatser finns i [Publiceringsguiden för Azure Marketplace och AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Vanliga erbjudandeåtgärder

Processen att skapa ett nytt erbjudande skiljer sig mycket mellan erbjudandetyper, till exempel mellan ett [Azure-programerbjudande](./azure-applications/cpp-azure-app-offer.md) och ett [erbjudande om konsulttjänster](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Många av de andra åtgärder som du utför på ett erbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com) är däremot ganska standardiserade över erbjudandetyper.  Dessa vanliga åtgärder – inklusive publicera, visa status, uppdatering och borttagning – beskrivs i avsnittet [Hantera erbjudanden](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Test Drive

*Test Drive* är en marknadsplats funktion som ger kunderna en "prova innan du köper" demonstration alternativ för varje erbjudande så aktiverat.  Test drive-funktionen är begränsad till följande delmängd av erbjudandetyper: [Azure-program,](./azure-applications/cpp-azure-app-offer.md) [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), Dynamics [365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), Dynamics [365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS-program](./saas-app/cpp-saas-offer.md)och [virtuella datorer](./virtual-machine/cpp-virtual-machine-offer.md).  Den här funktionen kräver att utgivaren skapar en Test Drive-mall, anpassad för erbjudandet.  Mer information finns i avsnittet [Provkörning](./test-drive/what-is-test-drive.md).

Du kan bläddra bland befintliga marketplace-erbjudanden som har Test Drive-demonstrationer genom att använda [provkörningsfiltret](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Erbjudandetyper för Azure Marketplace och AppSource

I följande tabell visas de aktuella erbjudandetyperna som stöds av [Cloud Partner Portal](https://cloudpartner.azure.com).  För varje erbjudandetyp visas de marknadsplatser där erbjudandet kan listas, samt en allmän beskrivning av erbjudandelösningstekniken.

|                Typ av erbjudande                |  Marketplace  |   Beskrivning                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-program](./azure-applications/cpp-azure-app-offer.md) | Azure | Lösningen består av en eller flera virtuella datorer ( VMs), valfri anpassad Azure-kod, som distribueras via en Azure Resource Manger-mall.  Distributionen kan antingen utföras av kunden via en lösningsmall eller hanteras av utgivaren. Den här typen används för att ge mer flexibilitet än den angivna erbjudandetypen för virtuella datorer.  |
| [Konsulttjänster](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Både | Microsoft-kvalificerade konsulter kan lista sina domänspecifika tjänster på antingen Azure Marketplace eller AppSource.  Deras expertis hjälper kunderna att bedöma sina problem och skapa och distribuera rätt lösningar för att uppfylla sina affärsmål.  |
| [Container](./containers/cpp-containers-offer.md)  | Azure | Lösningen är en Docker-behållaravbildning som etablerats som antingen en Kubernetes-baserad tjänst eller Azure Container-instanser. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Ett paket som utökar det här affärsresursplaneringssystemet (ERP) och affärshanteringssystemet. |
| [Dynamics 365 för Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Ett paket som utökar det här CRM-systemet (Customer Resource Management) genom dess moduler för försäljning, service, projektservice och fältservice  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Ett paket som utökar den här erp-tjänsten (Enterprise Resource Planning) som stöder avancerad finansiering, drift, tillverkning och hantering av leveranskedjan |
| [IoT Edge-modul](./iot-edge-module/cpp-offer-process-parts.md) | Azure | En Docker-kompatibel behållare som körs på en IoT Edge-enhet.  Den består av små beräkningsmoduler som använder en kombination av anpassad kod, andra Azure-tjänster och tjänster från tredje part. |
| [Power BI-app](./power-bi/cpp-power-bi-offer.md) | AppSource | En Power BI-app som paketerar anpassningsbart Power BI-innehåll, inklusive datauppsättningar, rapporter och instrumentpaneler |
| [SaaS-app](./saas-app/cpp-saas-offer.md) | Azure | Lösningen är en prenumeration som hanteras av utgivaren och som användarna loggar in via ett anpassat gränssnitt som använder Azure Active Directory. |
| [Virtuell dator](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Lösningen finns i en enda virtuell dator som distribueras till kundens prenumeration.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Mer information finns i [Publiceringsguide efter erbjudandetyp](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Nästa steg

Du får lära dig om de allmänna åtgärder du kan utföra på marketplace-erbjudanden och deras gemensamma tekniska attribut och tillgångar i artikeln [Hantera erbjudanden](./manage-offers/cpp-manage-offers.md).
