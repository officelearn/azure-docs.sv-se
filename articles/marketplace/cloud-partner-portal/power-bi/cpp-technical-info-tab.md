---
title: Teknisk information för ett erbjudande om Power BI app | Azure Marketplace
description: Konfigurera tekniska informations fält för ett Power BI app-erbjudande för Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 02c98e38f26fff73d02dc4c2b838ad3428cd2996
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819035"
---
# <a name="power-bi-apps-technical-info-tab"></a>Fliken teknisk information för Power BI Apps

På sidan **nytt erbjudande** använder du fliken **teknisk information** för att tillhandahålla Power BI installations paketets URL och annan information som du behöver för att verifiera det nya erbjudandet.  För den första versionen är alla Power BI-appar kostnads fria och tillgängliga för nedladdning från AppSource. Därför kan du inte definiera lagerhållnings enheter (SKU: er) för den här erbjudande typen.

![Fliken teknisk information](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Fält för teknisk information 

Fyll i fälten som beskrivs i följande tabell på fliken **teknisk information** . En asterisk (*) i slutet av en fält etikett innebär att fältet är obligatoriskt.

|        Fält          |  Beskrivning                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Installations-URL\***     | Power BI skapar den här URL: en när du publicerar appen och marknadsför den till produktion.  Mer information finns i [publicera appar med instrument paneler och rapporter i Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Verifierings anvisningar**  |  Om du vill kan du lägga till instruktioner (upp till 3 000 tecken) för att hjälpa Microsoft validerings teamet att konfigurera, ansluta och testa din app. Inkludera vanliga konfigurations inställningar, konton, parametrar eller annan information som kan användas för att testa alternativet Anslut data. Den här informationen visas bara för validerings teamet och används endast i validerings syfte.  |
| **Skapas den här appen som ett Power BI innehålls paket?** | För närvarande används det här fältet endast internt. Låt standardinställningen vara **Nej**. Om du ändrar inställningen till **Ja**kan du stoppa publicerings processen.  |  
|  |  |


## <a name="next-steps"></a>Nästa steg

På fliken [information om butik](./cpp-storefront-details-tab.md) anger du marknadsföring och juridisk information för din app.

