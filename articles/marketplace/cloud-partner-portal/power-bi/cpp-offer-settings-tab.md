---
title: Erbjudandeinställningar för ett Power BI-apperbjudande | Azure Marketplace
description: Konfigurera erbjudandeinställningar för ett Power BI-apperbjudande för Microsoft AppSource marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: f3a8d740d391edc09a290d3dba4307af7eec00b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286361"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Fliken Inställningar för Erbjudanden i Power BI-appar

När du öppnar sidan **Nytt erbjudande** för tjänstappar visas först fliken **Erbjudandeinställningar.** Du anger de primära identifierarna och namnet på erbjudandet på den här fliken. En asterisk (*) anger ett obligatoriskt fält.

![Fliken Erbjudandeinställningar](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Fälten Offerinställningar 

På fliken **Erbjudandeinställningar** måste du ange information i följande obligatoriska fält. Obligatoriska fält åtalas av en asterisk (*).

|  Field        |  Beskrivning                                                               |
|---------------|----------------------------------------------------------------------------|
| **Erbjudande-ID\***  | En unik identifierare (i en utgivarprofil) för erbjudandet. Den här identifieraren visas i produkt-URL:er, Azure Resource Manager-mallar och faktureringsrapporter. Den maximala längden är 50 tecken. Den kan bara innehålla gemener alfanumeriska tecken och streck (-). Det kan inte sluta med ett streck. Den här identifieraren kan inte ändras när ett erbjudande har levts. Om Contoso publicerar ett erbjudande `sample-SvcApp`med erbjudande-ID tilldelas erbjudandet `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`Url:en för AppSource .      |
| **Publisher\*** | Organisationens unika identifierare i [AppSource](https://appsource.microsoft.com). Alla dina erbjudanden ska kopplas till ditt utgivar-ID. Det går inte att ändra det här värdet när erbjudandet har sparats.                         |
| **Namn\***      | Ett visningsnamn för ditt erbjudande. Det här namnet visas på AppSource och på Cloud Partner Portal. Den maximala längden är 50 tecken. Använd ett varumärke som känns igen för din produkt. Ta inte med organisationens namn här om inte appen marknadsförs med det namnet. Om du tillhandahåller det här erbjudandet på andra webbplatser och publikationer använder du samma namn i alla publikationer.    <br/>Om du släpper ett erbjudande under förhandsgranskningsperioden för `(Preview)` Power BI Apps lägger du till `Sample Scv App (Preview)`strängen i slutet av programmets namn, så här: . |
|     |     |


## <a name="next-steps"></a>Nästa steg

På nästa flik anger du [teknisk information](./cpp-technical-info-tab.md) för ditt erbjudande.
