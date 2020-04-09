---
title: Teknisk information för ett Power BI-apperbjudande | Azure Marketplace
description: Konfigurera tekniska informationsfält för ett Power BI-apperbjudande för Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d814ebc34193f5d7c0c3828d32aa3d2af29f3679
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981366"
---
# <a name="power-bi-apps-technical-info-tab"></a>Fliken Teknisk information för Power BI Apps

>[!Important]
>Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Power BI-appar till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [översikten över hur du skapar Power BI-appar](https://aka.ms/AzureCreatePBIServiceApp) för att hantera dina migrerade erbjudanden.

På sidan **Nytt erbjudande** använder du fliken **Teknisk information** för att ange URL:en för Power BI-installationspaketet och annan information som du behöver för att validera det nya erbjudandet.  För den första versionen är alla Power BI-appar kostnadsfria och är tillgängliga för nedladdning från AppSource. På grund av detta kan du inte definiera lagerhållningsenheter (SKU: er) för den här erbjudandetypen.

![Fliken Teknisk information](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Fälten Teknisk information 

Fyll i fälten som beskrivs i följande tabell på fliken **Teknisk information.** En asterisk (*) i slutet av en fältetikett innebär att fältet krävs.

|        Field          |  Beskrivning                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL för installationsprogrammet\***     | Power BI genererar den här WEBBADRESSEN när du publicerar appen och befordrar den till produktion.  Mer information finns i [Publicera appar med instrumentpaneler och rapporter i Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Valideringsinstruktioner**  |  Om du vill kan du lägga till instruktioner (upp till 3 000 tecken) som hjälper Microsofts valideringsteam att konfigurera, ansluta och testa appen. Inkludera vanliga konfigurationsinställningar, konton, parametrar eller annan information som kan användas för att testa alternativet Anslut data. Den här informationen visas bara för valideringsteamet och används endast för valideringsändamål.  |
| **Skapas den här appen som ett Power BI-innehållspaket?** | För närvarande används det här fältet endast internt. Lämna standardinställningen **Nr**. Om du ändrar inställningen till **Ja**kan du stoppa publiceringsprocessen.  |  
|  |  |


## <a name="next-steps"></a>Nästa steg

På fliken [Information om Store](./cpp-storefront-details-tab.md) tillhandahåller du marknadsföring och juridisk information för din app.

