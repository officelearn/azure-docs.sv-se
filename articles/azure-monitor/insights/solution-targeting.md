---
title: Inriktning på övervakningslösningar i Azure Monitor | Microsoft-dokument
description: Med inriktning på övervakningslösningar kan du begränsa övervakningslösningar till en viss uppsättning agenter.  I den här artikeln beskrivs hur du skapar en scopekonfiguration och tillämpar den på en lösning.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: dd3279db67fb45aee43cf1b0ef1bebf49433eef4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663240"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Inriktning övervakningslösningar i Azure Monitor (förhandsversion)
När du lägger till en övervakningslösning i din prenumeration distribueras den automatiskt som standard till alla Windows- och Linux-agenter som är anslutna till logganalysarbetsytan.  Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter.  I den här artikeln beskrivs hur du använder **lösningsinriktning,** som är en funktion som gör att du kan använda ett scope på dina lösningar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Hur man riktar en lösning
Det finns tre steg för att rikta in sig på en lösning enligt beskrivningen i följande avsnitt. 


### <a name="1-create-a-computer-group"></a>1. Skapa en datorgrupp
Du anger de datorer som du vill inkludera i ett scope genom att skapa en [datorgrupp](../platform/computer-groups.md) i Azure Monitor.  Datorgruppen kan baseras på en loggfråga eller importeras från andra källor, till exempel Active Directory eller WSUS-grupper. Som [beskrivs nedan](#solutions-and-agents-that-cant-be-targeted)inkluderas endast datorer som är direkt anslutna till Azure Monitor i omfånget.

När du har skapat datorgruppen på arbetsytan inkluderar du den i en scopekonfiguration som kan tillämpas på en eller flera lösningar.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Skapa en scopekonfiguration
 En **scopekonfiguration** innehåller en eller flera datorgrupper och kan tillämpas på en eller flera lösningar. 
 
 Skapa en scopekonfiguration med hjälp av följande process.  

 1. I Azure-portalen navigerar du till **Log Analytics-arbetsytor** och väljer din arbetsyta.
 2. I egenskaperna för arbetsytan under **Arbetsytedatakällor** väljer du **Scopekonfigurationer**.
 3. Klicka på **Lägg till** om du vill skapa en ny scopekonfiguration.
 4. Ange ett **namn** för scopekonfigurationen.
 5. Klicka på **Välj datorgrupper**.
 6. Markera den datorgrupp som du har skapat och eventuellt alla andra grupper som ska läggas till i konfigurationen.  Klicka på **Markera**.  
 6. Klicka på **OK** om du vill skapa scopekonfigurationen. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Tillämpa scopekonfigurationen på en lösning.
När du har en scopekonfiguration kan du använda den på en eller flera lösningar.  Observera att även om en enda scopekonfiguration kan användas med flera lösningar, kan varje lösning bara använda en scopekonfiguration.

Använda en scopekonfiguration med hjälp av följande process.  

 1. I Azure-portalen navigerar du till **Log Analytics-arbetsytor** och väljer din arbetsyta.
 2. I egenskaperna för arbetsytan väljer du **Lösningar**.
 3. Klicka på den lösning du vill begränsa.
 4. I egenskaperna för lösningen under **Arbetsytedatakällor** väljer du **Lösningsinriktning**.  Om alternativet inte är tillgängligt [kan den här lösningen inte riktas](#solutions-and-agents-that-cant-be-targeted).
 5. Klicka på **Lägg till scopekonfiguration**.  Om du redan har en konfiguration som tillämpas på den här lösningen kommer det här alternativet inte att vara tillgängligt.  Du måste ta bort den befintliga konfigurationen innan du lägger till en annan.
 6. Klicka på scopekonfigurationen som du skapade.
 7. Titta på **konfigurationens status** för att säkerställa att den visar **lyckades**.  Om statusen anger ett fel klickar du på ellipsen till höger om konfigurationen och väljer **Redigera scopekonfiguration** för att göra ändringar.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Lösningar och agenter som inte kan riktas
Följande är kriterierna för agenter och lösningar som inte kan användas med lösningsinriktning.

- Lösningsinriktning gäller endast lösningar som distribueras till agenter.
- Lösningsinriktning gäller endast lösningar som tillhandahålls av Microsoft.  Det gäller inte för lösningar [som skapats av dig själv eller partners.](solutions-creating.md)
- Du kan bara filtrera bort agenter som ansluter direkt till Azure Monitor.  Lösningar distribueras automatiskt till alla agenter som ingår i en ansluten Hanteringsgrupp i Operations Manager oavsett om de ingår i en scopekonfiguration eller inte.

### <a name="exceptions"></a>Undantag
Lösningsinriktning kan inte användas med följande lösningar även om de uppfyller de angivna kriterierna.

- Agent Hälsobedömning

## <a name="next-steps"></a>Nästa steg
- Läs mer om övervakningslösningar, inklusive de lösningar som finns att installera i din miljö på [Lägg till Azure Log Analytics-övervakningslösningar på din arbetsyta](solutions.md).
- Läs mer om hur du skapar datorgrupper på [datorgrupper i Azure Monitor-loggfrågor](../platform/computer-groups.md).
