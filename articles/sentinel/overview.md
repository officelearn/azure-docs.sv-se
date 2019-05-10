---
title: Vad är förhandsversionen av Azure Sentinel? | Microsoft Docs
description: Läs mer om Azure Sentinel, de viktigaste funktionerna och hur det fungerar.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 7d64f02c7bebb6d4326281ba87d118eab075eba9
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228599"
---
# <a name="what-is-azure-sentinel-preview"></a>Vad är Azure Sentinel-förhandsversionen?

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinel-Microsoft Azure är en skalbar, molnbaserade **security information händelsehantering (SIEM)** och **security orchestration automatiska svar (SVÄVA)** lösning. Azure Sentinel levererar intelligent security analyser och hotinformation för hela företaget, vilket ger en enda lösning för avisering identifiering, threat synlighet, proaktiv jakt och svar på hot. 

Azure Sentinel är namnet öga vyn i hela företaget lindra stress av allt mer sofistikerade attacker, öka volymer av aviseringar och långa lösningen tidsramar.

- **Samla in data i molnskala** för alla användare, enheter, program och infrastruktur, både lokalt och i flera moln. 

- **Identifiera tidigare oupptäckt hot**, och minimera falska positiva identifieringar med Microsofts analys och oöverträffade hotinformation. 

- **Undersöka hot med artificiell intelligens**, och hitta misstänkta aktiviteter i skala, hjälp års cyberhot security arbete hos Microsoft. 

- **Svara på incidenter snabbt** med inbyggda orkestrering och automatisering av vanliga uppgifter.


![Azure Sentinel kärnfunktioner](./media/overview/core-capabilities.png)

Bygger på ett komplett utbud av befintliga Azure-tjänster, införlivar Azure Sentinel internt beprövade grundpelarna som Log Analytics och Logic Apps. Azure Sentinel berikar din undersökning och identifiering med artificiell Intelligens och tillhandahåller Microsofts threat intelligence stream och kan du överföra en egen hotinformation. 

 
## <a name="connect-to-all-your-data"></a>Ansluta till alla dina data

Att integrera Azure Sentinel, måste du först [ansluta till din säkerhet](connect-data-sources.md). Azure Sentinel levereras med ett antal anslutningsappar för Microsoft-lösningar, tillgängliga utanför rutan och ge i realtid integration, inklusive Microsoft Threat Protection lösningar och Microsoft 365 källor, till exempel Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security, och mycket mer. Det finns dessutom inbyggda anslutningar till bredare säkerhetsekosystemet för icke-Microsoft-lösningar. Du kan också använda common event format, Syslog eller REST-API för att ansluta dina datakällor med Azure Sentinel samt.  

![Datainsamlare](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Instrumentpaneler

När du har anslutit datakällor som du kan välja från ett galleri med [flytta skapat instrumentpaneler](quickstart-get-visibility.md#dashboards) som visa upp insikter från dina datakällor. Varje instrumentpanel är helt anpassningsbar – du kan lägga till egen logik eller ändra frågor eller du kan skapa en instrumentpanel från grunden.

Instrumentpaneler ger interaktiv visualisering med avancerade analyser för att din säkerhetsanalytiker få en bättre förståelse för vad som händer under ett angrepp. Undersökningsverktyg kan du djupdykning i alla fält, från alla data, att snabbt utveckla threat kontext. 

![Instrumentpaneler](./media/overview/dashboards.png)

## <a name="analytics"></a>Analytics

För att hjälpa dig att minska bruset och minimera antalet aviseringar som du måste granska och undersöka, Azure Sentinel använder [analyser för att korrelera aviseringar i fall](tutorial-detect-threats.md). **Fall** är grupper av relaterade varningar som tillsammans skapar en användbara möjliga hot som du kan undersöka och lösa. Använd inbyggda Korrelations-regler som – är, eller använda dem som utgångspunkt för att skapa din egen. Azure Sentinel innehåller också machine learning-regler för att mappa nätverksbeteende och Sök efter avvikelser i dina resurser. Analysresultaten ansluta punkter, genom att kombinera otillförlitliga varningar om olika entiteter till potentiella återgivna säkerhetsincidenter.

![Fall](./media/overview/cases.png)

## <a name="user-analytics"></a>Användaranalys

Med intern integrering av machine learning (ML), och [användaren analytics](user-analytics.md), Sentinel-Azure kan hjälpa dig att identifiera hot snabbt. Azure Sentinel integreras sömlöst med Azure Advanced Threat Protection att analysera användarbeteenden och prioritera vilka användare som du bör undersöka först, baserat på deras aviseringar och mönster för misstänkt aktivitet i Azure Sentinel och Microsoft 365.

![Användaranalys](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Security automatisering och orkestrering

Automatisera vanliga uppgifter och [förenkla security orchestration med strategiböcker](tutorial-respond-threats-playbook.md) som integreras med Azure-tjänster, samt dina befintliga verktyg. Bygger på grunden för Azure Logic Apps och Azure Sentinel automatisering och orkestrering lösningen ger en mycket utökningsbart arkitektur som möjliggör skalbar automatisering som nya tekniker och hot dyker upp. Du kan välja från ett växande galleri med inbyggda spelböcker för att skapa strategiböcker med Azure Logic Apps. Dessa inkluderar [över 200 anslutningsappar](https://docs.microsoft.com/azure/connectors/apis-list) för tjänster som Azure functions. Kopplingar kan du använda valfri egen kod i koden, ServiceNow, Jira, Zendesk, HTTP-begäranden, Microsoft Teams, Slack, Windows Defender ATP och Cloud App Security.

Om du använder ServiceNow biljettsystem kan du till exempel använda verktygen du använder Azure Logic Apps för att automatisera dina arbetsflöden och öppna en biljett i ServiceNow varje gång en viss händelse identifieras.

![Strategiböcker](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Undersökning

Azure Sentinel [djupgående undersökning](tutorial-investigate-cases.md) verktyg hjälper dig att förstå vilka och hitta grundorsaken till ett potentiellt hot. Du kan välja en entitet i interaktiva diagrammet så att de intressanta frågor för en specifik enhet Granska nedåt i entiteten och dess anslutningar till de grundläggande orsakerna till hotet. 

![Undersökning](./media/overview/investigation.png)


## <a name="hunting"></a>Hotjakt

Använd Azure Sentinel [kraftfulla jakt och sökfråga verktyg](hunting.md), baserat på det MITRE ramverk som gör det möjligt för proaktivt jakt efter säkerhetshot på din organisations datakällor, innan en avisering utlöses. När du upptäcker vilken jakt fråga ger värdefulla insikter om eventuella attacker, kan du också skapa anpassade detekteringsregler baserat på din fråga och visa upp dessa insikter som varningar till din security incident svarare. Vid jakt, kan du skapa bokmärken för intressanta händelser så att du kan gå tillbaka till dem senare, dela dem med andra och gruppera dem med andra correlating händelser för att skapa ett övertygande ärende för undersökning.

![Hotjakt](./media/overview/hunting.png)

## <a name="community"></a>Community

Sentinel-Azure-communityn är en kraftfull resurs för identifiering av hot och automatisering. Våra Microsoft-säkerhetsanalytiker hela tiden skapa och lägga till nya instrumentpaneler, spelböcker, jakt frågor och mer, publicera dem till gruppen som du kan använda i din miljö. Du kan hämta innehåll från diskussionsgruppen privat GitHub [databasen](https://aka.ms/asicommunity) att skapa anpassade instrumentpaneler, jakt frågor, anteckningsböcker och spelböcker för Sentinel-Azure. 

![Community](./media/overview/community.png)

## <a name="next-steps"></a>Nästa steg

- Om du vill komma igång med Azure Sentinel, behöver du en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Lär dig hur du [publicera dina data till Azure Sentinel](quickstart-onboard.md), och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
