---
title: Erbjudande inställningar för ett erbjudande för Power BI app | Azure Marketplace
description: Konfigurera erbjudande inställningar för ett Power BI app-erbjudande för Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822667"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Fliken Inställningar för att erbjuda Power BI appar

När du öppnar den **nya erbjudande** sidan för tjänst program ser du först fliken **erbjudande inställningar** . Du anger de primära identifierarna och namnet på ditt erbjudande på den här fliken. En asterisk (*) anger ett obligatoriskt fält.

![Fliken Erbjudandeinställningar](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Fält för erbjudande inställningar 

På fliken **erbjudande inställningar** måste du ange information i följande obligatoriska fält. Obligatoriska fält förutsägs av en asterisk (*).

|  Fält        |  Beskrivning                                                               |
|---------------|----------------------------------------------------------------------------|
| **Erbjudande-ID\***  | En unik identifierare (inom en utgivar profil) för erbjudandet. Den här identifieraren visas i produkt-URL: er, Azure Resource Manager mallar och fakturerings rapporter. Den maximala längden är 50 tecken. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). Det får inte sluta med ett bindestreck. Den här identifieraren kan inte ändras när ett erbjudande går live. Om contoso publicerar ett erbjudande med erbjudande-ID `sample-SvcApp`, tilldelas erbjudandet AppSource-URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Utgivare\*** | Organisationens unika identifierare i [AppSource](https://appsource.microsoft.com). Alla dina erbjudanden bör associeras med ditt utgivar-ID. Det här värdet kan inte ändras när erbjudandet har sparats.                         |
| **Namn\***      | Ett visnings namn för ditt erbjudande. Det här namnet kommer att visas på AppSource och på Cloud Partner Portal. Den maximala längden är 50 tecken. Använd ett varumärkes namn som är igenkännligt för din produkt. Ta inte med din organisations namn här om inte appen marknadsförs med det namnet. Om du ger det här erbjudandet på andra webbplatser och publikationer, använder du samma namn i alla publikationer.    <br/>Om du släpper ett erbjudande under för hands versions perioden för Power BI appar lägger du till strängen `(Preview)` i slutet av programmets namn, så här: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Nästa steg

På nästa flik anger du [teknisk information](./cpp-technical-info-tab.md) för ditt erbjudande.
