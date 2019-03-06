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
ms.date: 02/07/2019
ms.author: pbutlerm
ms.openlocfilehash: fd51a9f70d60219a9429ff66d6f1d404391f6fb0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446717"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure och AppSource Marketplace-erbjudanden

Den här första delen av det här avsnittet introducerar allmän statusöversikt används för att skapa och hantera erbjudanden för Azure och AppSource marknadsplatser.  Den här innehåller bakgrunden som du behöver känna till för att hantera specifika erbjudandetyper, samt teknisk information som är gemensamma för alla erbjuder typer.  Merparten av det här avsnittet innehåller detaljerad information om hur du skapar och hanterar specifika erbjudandetyper.  

Följande videoklipp beskriver de olika funktionerna och olika erbjudanden tillgängliga typer i Azure Marketplace eller AppSource.  Den behandlar också viktiga tekniska och företag aspekter av att publicera en applikation eller tjänst i dessa marknadsplatser.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Skapa appar och tjänster för Azure Marketplace och AppSource - skapa 2018**

Läs mer om dessa marknadsplatser [publiceringsguide för Azure Marketplace och AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Vanliga åtgärder för erbjudandet

Processen för att skapa ett nytt erbjudande skiljer sig avsevärt mellan olika typer av erbjudanden, till exempel mellan en [Azure-program erbjudandet](./azure-applications/cpp-azure-app-offer.md) och en [konsult tjänsterbjudande](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Däremot många av de andra åtgärderna du utföra på ett erbjudande i den [Cloud Partner Portal](https://cloudpartner.azure.com) är ganska standardiserade över typer av erbjudanden.  Dessa vanliga åtgärder, inklusive publicera, visa status, uppdatera och ta bort – beskrivs i avsnittet [hantera erbjudanden](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Test Drive

*Testkör* är en marketplace-funktion som ger kunderna ett ”testa innan du köper” demonstration alternativ för varje erbjudande möjlighet.  Test Drive-funktionen är begränsad till följande delmängden av typer av erbjudanden: [Azure-program](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 för kundengagemang](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 för Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [ SaaS-program](./saas-app/cpp-saas-offer.md), och [virtuella datorer](./virtual-machine/cpp-virtual-machine-offer.md).  Den här funktionen kräver utgivaren för att skapa en Test Drive-mall som är anpassad efter dess erbjudandet.  Mer information finns i avsnittet [Test Drive](./test-drive/what-is-test-drive.md).

Du kan bläddra i marketplace-erbjudanden som har Test Drive demonstrationer genom att använda den [test drive filter](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=test-drive). 


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
