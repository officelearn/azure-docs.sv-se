---
title: Introduktion till Azure Advisor | Microsoft Docs
description: Använda Azure Advisor för att optimera dina Azure-distributioner.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: e1d7edef304dc91829066f19b6974f0bcf0be0a5
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055761"
---
# <a name="introduction-to-azure-advisor"></a>Introduktion till Azure Advisor

Läs om de viktigaste funktionerna i Azure Advisor och få svar på vanliga frågor och svar.

## <a name="what-is-advisor"></a>Vad är Advisor?
Advisor är en personligt anpassad molnrådgivare som hjälper dig att följa bästa praxis för att optimera dina Azure-distributioner. Advisor analyserar din resurskonfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

Med Advisor kan du:
* Få proaktiva, användbara och personligt anpassade rekommendationer. 
* Förbättra prestanda, säkerhet och hög tillgänglighet för dina resurser, när du identifierar möjligheter att minska de totala Azure utgifter.
* Få rekommendationer med föreslagna åtgärder infogad.

Du kan komma åt Advisor i den [Azure-portalen](https://aka.ms/azureadvisordashboard). Logga in på den [portal](https://portal.azure.com), leta upp **Advisor** i navigeringsmenyn eller Sök efter den i den **alla tjänster** menyn.

Advisor-instrumentpanelen visar anpassade rekommendationer för alla dina prenumerationer.  Du kan använda filter för att visa rekommendationer för specifika prenumerationer och resurstyper.  Rekommendationer är uppdelad i fyra kategorier: 

* **Hög tillgänglighet**: att säkerställa och förbättra affärskontinuitet för dina verksamhetskritiska program. Mer information finns i [rekommendationer för hög tillgänglighet för Advisor](advisor-high-availability-recommendations.md).
* **Security**: att identifiera hot och sårbarheter som kan leda till säkerhetsproblem. Mer information finns i [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md).
* **Prestanda**: att förbättra hastigheten på dina program. Mer information finns i [Advisor prestandarekommendationer](advisor-performance-recommendations.md).
* **Kostnaden**: att optimera och minska dina totalpriset för Azure utgifter. Mer information finns i [Advisor kostnadsrekommendationer](advisor-cost-recommendations.md).

  ![Typer av Advisor-rekommendationen](./media/advisor-overview/advisor-dashboard.png)

Du kan klicka på en kategori om du vill visa en lista över rekommendationer i den kategorin och välj en rekommendation om du vill veta mer om den.  Du kan också lära dig om åtgärder som du kan utföra för att dra nytta av en affärsmöjlighet eller lösa ett problem.

![Kategori för Advisor-rekommendationen](./media/advisor-overview/advisor-ha-category-example.png) 

Välj den rekommenderade åtgärden för en rekommendation att implementera rekommendationen.  Ett enkelt gränssnitt öppnas där du kan implementera rekommendationen eller avser dokumentation som hjälper dig med implementering.  När du har implementerat en rekommendation, kan det ta upp till en dag för Advisor ska kunna identifiera som.

Du kan skjuta upp den för en angiven tidsperiod eller ta bort den om du inte tänker vidta omedelbara åtgärder på en rekommendation.  Om du inte vill få rekommendationer för en viss prenumeration eller resursgrupp kan du konfigurera Advisor för att endast skapa rekommendationer för angivna prenumerationer och resursgrupper.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-access-advisor"></a>Hur kommer jag åt Advisor?
Du kan komma åt Advisor i den [Azure-portalen](https://aka.ms/azureadvisordashboard). Logga in på den [portal](https://portal.azure.com), leta upp **Advisor** i navigeringsmenyn eller Sök efter den i den **alla tjänster** menyn.

Du kan också visa Advisor-rekommendationer via gränssnittet för VM-resurs. Välj en virtuell dator och bläddrar sedan till Advisor-rekommendationer i menyn. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Vilka behörigheter behöver jag att komma åt Advisor?
 
Du kan komma åt Advisor-rekommendationer som *ägare*, *deltagare*, eller *läsare* för en prenumeration.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Vilka resurser Advisor tillhandahåller rekommendationer för?

Advisor innehåller rekommendationer för virtuella datorer, tillgänglighetsuppsättningar, programgatewayer, App Services, SQL-servrar och Redis Cache.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Kan jag skjuta upp eller ignorera en rekommendation?

Om du vill skjuta upp eller ignorera en rekommendation klickar du på den **Skjut upp** länk. Du kan ange en Skjut upp period eller välj **aldrig** att Stäng rekommendationen.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:

* [Kom igång med Advisor](advisor-get-started.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)
* [Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Advisor kostnadsrekommendationer](advisor-cost-recommendations.md)
