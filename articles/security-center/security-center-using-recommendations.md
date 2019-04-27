---
title: Använd Azure Security Center-rekommendationer för att förbättra säkerheten | Microsoft Docs
description: " Lär dig hur du använder säkerhetsprinciper och säkerhetsrekommendationer i Azure Security Center för att minska en säkerhetsangrepp. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: monhaber
ms.openlocfilehash: 82a46ae9523c4c2778f86c445e35d0bec961307f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703458"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Använd Azure Security Center-rekommendationer för att förbättra säkerheten
Du kan minska risken för en betydande säkerhetshändelse genom att konfigurera en säkerhetsprincip för och implementera rekommendationer som tillhandahålls av Azure Security Center. Den här artikeln visar hur du använder säkerhetsprinciper och säkerhetsrekommendationer i Security Center för att minska en säkerhetsangrepp. 

Security Center körs automatiskt kontinuerlig sökningar för att analysera säkerhetsläget hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera säkerhetskontrollerna som behövs. Security Center uppdaterar sina rekommendationer inom 24 timmar, med följande undantag:

- Konfiguration av operativsystemet säkerhetsrekommendationer uppdateras inom 48 timmar
- Problem med rekommendationer om endpoint Protection uppdateras inom 8 timmar

## <a name="scenario"></a>Scenario
Det här scenariot visar hur du använder Security Center för att minska risken för en säkerhetsincident genom att övervaka Security Center-rekommendationer och vidta åtgärder. Scenariot använder fiktiva företag, Contoso och roller som visas i Security Center [planerings- och bruksanvisning](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). I det här fallet vill vi fokusera på rollerna för följande personer:

![Scenario-roller](./media/security-center-using-recommendations/scenario-roles.png)

Contoso migrerade nyligen några av sina lokala resurser till Azure. Contoso vill skydda sina resurser och minska säkerhetsrisken i sina resurser i molnet.

## <a name="use-azure-security-center"></a>Använda Azure Security Center
David från Contoso är IT-säkerhet, redan har valt att integrera Security Center för Contosos prenumerationer till Azure Security Center för att förhindra och identifiera säkerhetsproblem. 

Security Center analyserar säkerhetstillståndet för Contosos Azure-resurser automatiskt och använder standard säkerhetsprinciper. När Security Center identifierar potentiella säkerhetsproblem skapas **rekommendationer** baserat på de kontroller som angetts i säkerhetsprincipen. 

David körs standardnivån för Azure-säkerhet för alla sina prenumerationer att hämta den fullständiga uppsättning rekommendationer och säkerhetsfunktioner. Jeff som registrerar virtuella alla sina befintliga lokala servrar som inte ännu har migrerats till molnet så att han kan dra nytta av Security Center hybrid stöder även över sin [Windows](quick-onboard-windows-computer.md) och [Linux](quick-onboard-linux-computer.md) servrar.

Jeff är en molnansvarig. Jeff är ansvarig för att installera säkerhetskontroller i enlighet med Contosos säkerhetsprinciper. 

Jeff utför följande uppgifter:

- Övervaka säkerhetsrekommendationer från Security Center
- Utvärdera säkerhetsrekommendationer och bestämma om han ska tillämpa eller avvisa
- Tillämpa säkerhetsrekommendationer

### <a name="remediate-threats-using-recommendations"></a>Åtgärda hot med hjälp av rekommendationer
Som en del av sin dagliga övervakningsaktiviteter, Jeff loggar in på Azure och öppnar Security Center. 

1. Jeff väljer sin arbetsbelastning prenumerationer.

2. Jeff kontrollerar sin **skyddar score** för att få en övergripande bild av hur säker prenumerationerna är och ser han att sin poängen är 548.

3. Jeff har att bestämma vilka rekommendationer för att hantera först. Så Jeff klickar säker poäng och börjar hantera rekommendationer baserat på hur mycket det förbättrar sin [skyddar score påverkan](security-center-secure-score.md).

4. Eftersom Jeff har massor av anslutna virtuella datorer och servrar kan Jeff bestämmer sig för att fokusera på **beräknings- och appar**.

5. När Jeff klickar **beräknings- och appar**, han ser en lista över rekommendationer och hanterar dem enligt den säkra bedöma effekten.

6. Jeff har ett stort antal virtuella datorer som riktas mot Internet och eftersom deras hamnar exponeras han är oroliga att en angripare kan få kontroll över servrar. Så Jeff väljer att använda (**åtkomst till Virtuella just-in-time**) [security-center-just-i-time.md].

Jeff fortsätter att flytta via hög prioritet och rekommendationer Medelhög prioritet och fattar beslut om implementering. För varje rekommendation tittar Jeff på den detaljerade informationen angivna från Security Center att förstå vilka resurser som påverkas, säker poängen påverka är, vilka varje rekommendation innebär och steg att minimera olika problemen.

## <a name="conclusion"></a>Sammanfattning
Övervakningsfunktioner rekommendationer i Security Center kan du eliminera säkerhetsrisker innan en attack inträffar. När du reparera rekommendationer, förbättra dina säker poäng och säkerhetsstatus för dina arbetsbelastningar. Security Center identifierar automatiskt nya resurser som du distribuerar, utvärderar dem mot din säkerhetsprincip och ger nya rekommendationer för att skydda dem.


## <a name="next-steps"></a>Nästa steg
Kontrollera att du har en övervakningsprocessen på plats, där du regelbundet kontrollera rekommendationerna i Security Center så att du kan se till att skydda dina resurser över tid.

Det här scenariot visades hur du använder säkerhetsprinciper och säkerhetsrekommendationer i Security Center för att minska en säkerhetsangrepp. Se den [scenario med incidenthantering](security-center-incident-response.md) och lär dig att ha en incidenthanteringsplan på plats innan en attack äger rum.

Lär dig att svara på hot med [incidenthantering](security-center-incident-response.md).
