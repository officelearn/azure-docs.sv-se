---
title: Vad är Azure Sentinel?| Microsoft-dokument
description: Lär dig mer om Azure Sentinel, dess nyckelfunktioner och hur det fungerar.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 7d0f246a96072a6e433b0762256467f1c20a45fe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581726"
---
# <a name="what-is-azure-sentinel"></a>Vad är Azure Sentinel?

Microsoft Azure Sentinel är en skalbar, **molnbaserad, säkerhetsinformation händelsehantering (SIEM)** och **säkerhet orkestrering automatiserad svar (SOAR)** lösning. Azure Sentinel levererar intelligent säkerhetsanalys och hotinformation i hela företaget, vilket ger en enda lösning för varningsidentifiering, hotsynlighet, proaktiv jakt och hothantering. 

Azure Sentinel är din fågelperspektiv över hela företaget lindra stressen av allt mer sofistikerade attacker, ökande volymer av aviseringar och långa tidsramar upplösning.

- **Samla in data i molnskala** för alla användare, enheter, program och infrastruktur, både lokalt och i flera moln. 

- **Identifiera tidigare oupptäckta hot**och minimera falska positiva identifieringar med hjälp av Microsofts analyser och oöverträffad hotinformation. 

- **Undersök hot med artificiell intelligens**– och jaga efter misstänkta aktiviteter i stor skala och utnyttja år av it-säkerhetsarbete på Microsoft. 

- **Svara snabbt på incidenter** med inbyggd orkestrering och automatisering av vanliga uppgifter.

![Kärnfunktioner i Azure Sentinel](./media/overview/core-capabilities.png)

Azure Sentinel bygger på hela skalan av befintliga Azure-tjänster och innehåller beprövade grunder, som Log Analytics och Logic Apps. Azure Sentinel berikar din undersökning och identifiering med AI och tillhandahåller Microsofts hotinformationsström och gör att du kan ta med din egen hotinformation. 

## <a name="connect-to-all-your-data"></a>Anslut till alla dina data

För att vara ombord på Azure Sentinel måste du först [ansluta till dina säkerhetskällor](connect-data-sources.md). Azure Sentinel levereras med ett antal anslutningsappar för Microsoft-lösningar, tillgängliga direkt och ger integrering i realtid, inklusive Microsoft Threat Protection-lösningar och Microsoft 365-källor, inklusive Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security med mera. Dessutom finns det inbyggda kopplingar till det bredare säkerhetsekosystemet för lösningar som inte kommer från Microsoft. Du kan också använda vanligt händelseformat, Syslog eller REST-API för att ansluta dina datakällor med Azure Sentinel också.  

![Datainsamlare](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Arbetsböcker

När du [har anslutit dina datakällor](quickstart-onboard.md) till Azure Sentinel kan du övervaka data med Azure Sentinel-integreringen med Azure Monitor-arbetsböcker, vilket ger mångsidighet när du skapar anpassade arbetsböcker. Arbetsböcker visas på olika sätt i Azure Sentinel, men det kan vara användbart för dig att se hur du [skapar interaktiva rapporter med Azure Monitor-arbetsböcker](../azure-monitor/app/usage-workbooks.md). Med Azure Sentinel kan du skapa anpassade arbetsböcker över dina data och även levereras med inbyggda arbetsboksmallar så att du snabbt kan få insikter om dina data så fort du ansluter en datakälla.

![Instrumentpaneler](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analys

För att hjälpa dig att minska bruset och minimera antalet aviseringar som du måste granska och undersöka använder Azure Sentinel [analyser för att korrelera aviseringar till incidenter](tutorial-detect-threats-built-in.md). **Incidenter** är grupper av relaterade aviseringar som tillsammans skapar ett användbart möjligt hot som du kan undersöka och lösa. Använd de inbyggda korrelationsreglerna som de är, eller använd dem som utgångspunkt för att skapa egna. Azure Sentinel innehåller också maskininlärningsregler för att mappa ditt nätverksbeteende och sedan leta efter avvikelser mellan dina resurser. Dessa analyser ansluter punkterna genom att kombinera lågåtergivningsvarningar om olika entiteter till potentiella säkerhetsincidenter med hög återgivning.

![Incidenter](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Säkerhetsautomatisering & orkestrering

Automatisera dina vanliga uppgifter och [förenkla säkerhetsorkestrering med spelböcker](tutorial-respond-threats-playbook.md) som integreras med Azure-tjänster samt dina befintliga verktyg. Azure Sentinels automatiserings- och orkestreringslösning bygger på grunden för Azure Logic Apps och erbjuder en mycket utökningsbar arkitektur som möjliggör skalbar automatisering när nya tekniker och hot uppstår. Om du vill skapa spelböcker med Azure Logic Apps kan du välja bland ett växande galleri med inbyggda spelböcker. Dessa inkluderar [200 + kopplingar](https://docs.microsoft.com/azure/connectors/apis-list) för tjänster som Azure-funktioner. Med anslutningsapparna kan du tillämpa all anpassad logik i kod, ServiceNow, Jira, Zendesk, HTTP-begäranden, Microsoft Teams, Slack, Windows Defender ATP och Cloud App Security.

Om du till exempel använder Ticketing-systemet För ServiceNow kan du använda de verktyg som tillhandahålls för att använda Azure Logic Apps för att automatisera dina arbetsflöden och öppna en biljett i ServiceNow varje gång en viss händelse identifieras.

![Strategiböcker](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Undersökning

Azure [Sentinel-verktyg](tutorial-investigate-cases.md) för förhandsversion hjälper dig att förstå omfattningen och hitta grundorsaken till ett potentiellt säkerhetshot. Du kan välja en entitet i det interaktiva diagrammet för att ställa intressanta frågor för en viss entitet och öka detaljnivån i den entiteten och dess anslutningar för att komma till orsaken till hotet. 

![Undersökning](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Hotjakt

Använd Azure Sentinels [kraftfulla sök- och frågeverktyg](hunting.md)för jakt , baserat på MITRE-ramverket, som gör att du proaktivt kan söka efter säkerhetshot i organisationens datakällor innan en avisering utlöses. När du har upptäckt vilken jaktfråga som ger insikter om värdefulla insikter om möjliga attacker kan du också skapa anpassade identifieringsregler baserat på din fråga och visa dessa insikter som aviseringar till dina säkerhetsincidentsvarare. Under jakt kan du skapa bokmärken för intressanta händelser, så att du kan återgå till dem senare, dela dem med andra och gruppera dem med andra korrelaterade händelser för att skapa en övertygande incident för utredning.

![Hotjakt](./media/overview/hunting.png)

## <a name="community"></a>Community

Azure Sentinel-communityn är en kraftfull resurs för identifiering och automatisering av hot. Våra Microsoft-säkerhetsanalytiker skapar och lägger ständigt till nya arbetsböcker, spelböcker, jaktfrågor med mera och skickar dem till communityn så att du kan använda dem i din miljö. Du kan hämta exempelinnehåll från den [repository](https://aka.ms/asicommunity) privata community-GitHub-databasen för att skapa anpassade arbetsböcker, jaktfrågor, anteckningsböcker och spelböcker för Azure Sentinel. 

![Community](./media/overview/community.png)

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/free/).
- Lär dig hur du [lägger in dina data i Azure Sentinel](quickstart-onboard.md)och får insyn i dina data och [potentiella hot](quickstart-get-visibility.md).
