---
title: Erbjudandeinställningar för en Azure IoT Edge-modul | Azure Marketplace
description: Konfigurera erbjudandeinställningar för en IoT Edge-modul.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: e7b7d04cd3d1ed9792f8ad3c7bc302e4a2c2180f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745020"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Fliken Inställningar för IoT-kantmoduler

>[!Important]
>Från och med den 30 mars 2020 börjar vi flytta hanteringen av dina IoT Edge-modulerbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett IoT Edge-modulerbjudande](https://aka.ms/AzureCreateIoT) för att hantera dina migrerade erbjudanden.

**IoT Edge Modules > nya erbjudande** sidan öppnas med fokus på fliken **Erbjudandeinställningar.** 

![Ny erbjudandesida för IoT Edge-moduler](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Inställningar för erbjudandeidentitet

Under **Erbjudandeidentitet**måste du ange information för de fält som beskrivs i följande tabell. En asterisk (*) som läggs till i fältnamnet anger att det krävs. 

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***       | En unik identifierare (i en utgivarprofil) för erbjudandet. Den här identifieraren visas i produkt-URL:er och insiktersrapporter. Den har en maximal längd på 50 tecken och kan använda gemener alfanumeriska tecken och streck (-). (Identifieraren kan inte sluta med ett streck.) **Anm.:** Det går inte att ändra det här fältet när ett erbjudande har live. <br> Om Contoso till exempel publicerar ett erbjudande med erbjudande-ID-exempel-iot-edge-module tilldelas `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`den Azure **Marketplace-URL:en**. |
| **Publisher\***     | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden ska kopplas till ditt utgivar-ID. Det går inte att ändra det här värdet när erbjudandet har sparats. |
| **Namn\***          | Visningsnamnet för ditt erbjudande. Det här namnet visas på Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vi rekommenderar att du använder ett igenkännbart varumärke för din produkt. Ta inte med organisationens namn om det inte är så din produkt marknadsförs. Om du marknadsför detta erbjudande på andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|  |  |


Välj **Spara** om du vill spara erbjudandeinställningarna.


## <a name="next-steps"></a>Nästa steg

Använd fliken [SKU:er](./cpp-skus-tab.md) för att konfigurera SKU:erna för ditt erbjudande.
