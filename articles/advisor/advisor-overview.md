---
title: Introduktion till Azure Advisor | Microsoft Docs
description: "Använd Azure Advisor för att optimera din Azure-distributioner."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 96925f251cf4984a11516a962740e19a7b9589dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-advisor"></a>Introduktion till Azure Advisor

Lär dig mer om Azure Advisor och de viktigaste funktionerna och få svar på vanliga frågor och svar.

## <a name="what-is-advisor"></a>Vad är Advisor?
Advisor är en anpassad cloud konsult som hjälper dig att följa de bästa metoderna för att optimera din Azure-distributioner. Den analyserar dina resurskonfigurationen och telemetri och rekommenderar lösningar som hjälper dig att förbättra kostnadseffektivitet, prestanda, hög tillgänglighet och säkerhet för dina Azure-resurser.

Med Advisor kan du:
* Hämta proaktiv, tillförlitlig och anpassade rekommendationer och metodtips. 
* Förbättra prestanda, säkerhet och hög tillgänglighet för dina resurser, som du identifiera möjligheter att minska den totala Azure tillbringar.
* Få rekommendationer med infogad föreslagna åtgärder.

Du kan komma åt Advisor i den [Azure-portalen](https://aka.ms/azureadvisordashboard). Logga in på den [portal](https://portal.azure.com)väljer **Bläddra**, och bläddra sedan till **Azure Advisor**. Advisor-instrumentpanelen innehåller anpassad rekommendationer för den valda prenumerationen. 

Rekommendationerna delas in i fyra kategorier: 

* **Hög tillgänglighet**: att säkerställa och förbättra kontinuiteten i dina verksamhetskritiska program. Mer information finns i [hög tillgänglighet för Advisor-rekommendationer](advisor-high-availability-recommendations.md).

* **Säkerhet**: identifiera hot och säkerhetsrisker som kan leda till säkerhetsintrång. Mer information finns i [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md).

* **Prestanda**: att förbättra hastighet för dina program. Mer information finns i [Advisor-rekommendationer](advisor-performance-recommendations.md).

* **Kostnad**: för att optimera och minska din övergripande Azure tillbringar. Mer information finns i [kostnaden för Advisor-rekommendationer](advisor-cost-recommendations.md).

  ![Typer för Advisor-rekommendationer](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Om du vill komma åt Advisor-rekommendationer, måste du först *registrera prenumerationen* med Advisor. En prenumeration registreras när en *prenumeration ägare* startar Advisor instrumentpanelen och klickar på den **få rekommendationer** knappen. Det här är en *engångsåtgärd*. När prenumerationen har registrerats kan du komma åt Advisor-rekommendationer som *ägare*, *deltagare*, eller *Reader* för en prenumeration, resursgrupp eller en viss resurs.

Du kan klicka på en rekommendation om du vill veta mer om den. Du kan också information om åtgärder som du kan utföra för att dra nytta av en affärsmöjlighet eller lösa ett problem. 

Advisor-rekommendationer med infogad åtgärder eller dokumentationen länkar. Klicka på en infogad åtgärd tar dig igenom en ”interaktiv användare resa” att genomföra den. Klicka på en länk i dokumentationen hänvisar till dokumentationen som beskriver hur du implementerar åtgärden manuellt. 

Advisor uppdaterar rekommendationer varje timme. Om du inte tänker vidta omedelbara åtgärder på en rekommendation du stänga den viloläge för en angiven tidsperiod. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-access-advisor"></a>Hur kommer jag åt Advisor?
Du kan komma åt Advisor i den [Azure-portalen](https://aka.ms/azureadvisordashboard). Logga in på den [portal](https://portal.azure.com)väljer **Bläddra**, och bläddra sedan till **Azure Advisor**. Advisor-instrumentpanelen innehåller anpassad rekommendationer för den valda prenumerationen. 

Du kan också visa Advisor-rekommendationer via resursbladet för virtuell dator. Välj en virtuell dator och bläddrar sedan till Advisor-rekommendationer i menyn. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Vilka behörigheter som behöver att komma åt Advisor?

Om du vill komma åt Advisor-rekommendationer, måste du först *registrera prenumerationen* med Advisor. En prenumeration registreras när en *prenumeration ägare* startar Advisor instrumentpanelen och klickar på den **få rekommendationer** knappen. Det här är en *engångsåtgärd*. När prenumerationen har registrerats kan du komma åt Advisor-rekommendationer som *ägare*, *deltagare*, eller *Reader* för en prenumeration, resursgrupp eller en viss resurs.

### <a name="how-often-are-advisor-recommendations-updated"></a>Hur ofta uppdateras Advisor-rekommendationer uppdateras?

Advisor-rekommendationer uppdateras varje timme.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Vilka resurser Advisor ger rekommendationer för?

Advisor innehåller rekommendationer för virtuella datorer, tillgänglighetsuppsättningar, programgatewayer, Apptjänster, SQL-servrar, SQL-databaser och Redis-Cache.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Kan jag viloläge eller ignorera en rekommendation?

Viloläge eller ignorera en rekommendation genom att klicka på den **viloläge** knappen eller länken. Du kan ange en tid för viloläge period eller välja **aldrig** stänga rekommendationen.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:

* [Kom igång med Advisor](advisor-get-started.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)
* [Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Kostnad Advisor-rekommendationer](advisor-cost-recommendations.md)
