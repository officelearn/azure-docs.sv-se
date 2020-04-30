---
title: Erbjudande inställningar för en Azure IoT Edge-modul | Azure Marketplace
description: Konfigurera erbjudande inställningar för en IoT Edge-modul.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d6d2306dfd44bad6e1f903c3cef3eeb4993f4cd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148026"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Fliken Inställningar i IoT Edge modul

>[!Important]
>Från och med 13 april 2020 kommer vi att börja flytta hantering av IoT Edge modul-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett IoT Edge modul-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) för att hantera dina migrerade erbjudanden.

**IoT Edge modulerna > nya erbjudande** sidan öppnas med fokus på fliken **erbjudande inställningar** .

![Ny erbjudande sida för IoT Edge moduler](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Ge identitets inställningar

Under **erbjudande identitet**måste du ange information för fälten som beskrivs i följande tabell. En asterisk (*) som läggs till i fält namnet visar att det är obligatoriskt. 

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Erbjudande-ID\***       | En unik identifierare (inom en utgivar profil) för erbjudandet. Den här identifieraren visas i produkt-URL: er och Insights-rapporter. Det får bestå av högst 50 tecken och kan använda gemena alfanumeriska tecken och bindestreck (-). (Identifieraren kan inte sluta med ett bindestreck.) **Obs:** Det här fältet kan inte ändras när ett erbjudande går live. <br> Om contoso till exempel publicerar ett erbjudande med ID **-exemplet-IoT-Edge-module**, tilldelas URL: en `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`för Azure Marketplace. |
| **Utgivare\***     | Organisationens unika identifierare på Azure Marketplace. Alla dina erbjudanden bör associeras med ditt utgivar-ID. Det här värdet kan inte ändras efter att erbjudandet har sparats. |
| **Name\***          | Ditt erbjudandes visnings namn. Det här namnet visas på Azure Marketplace och i Cloud Partner Portal. Det får innehålla högst 50 tecken. Vi rekommenderar att du använder ett identifierbart varumärkes namn för din produkt. Ta inte med din organisations namn om det inte är så här som produkten marknadsförs. Om du saluför det här erbjudandet i andra webbplatser och publikationer, se till att namnet är exakt detsamma i alla publikationer. |
|  |  |


Spara inställningarna för erbjudandet genom att välja **Spara** .


## <a name="next-steps"></a>Nästa steg

Använd fliken [SKU: er](./cpp-skus-tab.md) för att konfigurera SKU: er för ditt erbjudande.
