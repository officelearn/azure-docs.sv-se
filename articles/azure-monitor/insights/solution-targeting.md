---
title: Riktar in sig på övervakningslösningar i Azure Monitor | Microsoft Docs
description: Riktar in sig på övervakningslösningar kan du begränsa övervakningslösningar till en specifik uppsättning med agenter.  Den här artikeln beskriver hur du skapar en omfattningskonfigurationen och tillämpa den på en lösning.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 4082847e1871fc03713471b0c043dddb80f91b0d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769241"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Riktar in sig på övervakningslösningar i Azure Monitor (förhandsversion)
Den distribueras automatiskt som standard att alla Windows- och Linux-agenter är anslutna till Log Analytics-arbetsytan när du lägger till en övervakningslösning till din prenumeration.  Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning med agenter.  Den här artikeln beskriver hur du använder **Lösningsmål** som är en funktion som gör att du kan använda ett omfång för dina lösningar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Hur du rikta en lösning
Det finns tre steg för att riktar in sig på en lösning som beskrivs i följande avsnitt. 


### <a name="1-create-a-computer-group"></a>1. Skapa en datorgrupp
Ange de datorer som du vill ska ingå i en omfattning genom att skapa en [datorgrupp](../platform/computer-groups.md) i Azure Monitor.  Datorgruppen kan baserat på en loggfråga eller importeras från andra källor, till exempel Active Directory eller WSUS-grupper. Som [som beskrivs nedan](#solutions-and-agents-that-cant-be-targeted), bara datorer som är anslutna direkt till Azure Monitor kommer att inkluderas i omfånget.

När du har den datorgrupp som skapats i din arbetsyta och sedan ska du inkludera den i en scope-konfiguration som kan tillämpas på en eller flera lösningar.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Skapa en omfattningskonfiguration
 En **Omfattningskonfigurationen** innehåller en eller flera datorgrupper och kan tillämpas på en eller flera lösningar. 
 
 Skapa en omfattningskonfiguration på följande sätt.  

 1. I Azure-portalen går du till **Log Analytics-arbetsytor** och välj din arbetsyta.
 2. I egenskaperna för arbetsytan under **datakällor för arbetsyta** Välj **Omfattningskonfigurationer**.
 3. Klicka på **Lägg till** att skapa en ny omfattningskonfigurationen.
 4. Ange ett **namn** för konfigurationen.
 5. Klicka på **Välj datorgrupper**.
 6. Välj datorgruppen som du skapade och eventuellt andra grupper för att lägga till i konfigurationen.  Klicka på **Välj**.  
 6. Klicka på **OK** att skapa omfattningskonfigurationen. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Gäller konfigurationen för en lösning.
När du har en omfattningskonfigurationen kan tillämpa du den på en eller flera lösningar.  Observera att en enda scope-konfiguration kan användas av flera lösningar, varje lösning kan bara använda en av omfattningskonfigurationen.

Tillämpa en omfattningskonfiguration på följande sätt.  

 1. I Azure-portalen går du till **Log Analytics-arbetsytor** och välj din arbetsyta.
 2. I egenskaperna för arbetsytan väljer **lösningar**.
 3. Klicka på den lösning du vill omfång.
 4. I egenskaperna för lösningen under **datakällor för arbetsyta** Välj **Lösningsmål**.  Om alternativet inte är tillgängligt sedan [den här lösningen kan inte riktas](#solutions-and-agents-that-cant-be-targeted).
 5. Klicka på **Lägg till omfattningskonfiguration**.  Om du redan har en konfiguration som tillämpas på den här lösningen och sedan på det här alternativet inte tillgängligt.  Du måste ta bort den befintliga konfigurationen innan du lägger till en annan.
 6. Klicka på konfigurationen som du skapade.
 7. Titta på den **Status** av konfigureringen för att se till att den visar **lyckades**.  Om statusen indikerar ett fel, klickar du på ellipsen till höger om konfiguration och välj **redigera omfattningskonfiguration** att göra ändringar.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Lösningar och agenter som inte kan riktas
Följande är villkoren för agenter och lösningar som inte kan användas med lösningsmål.

- Lösningsmål gäller endast för lösningar som distribueras till agenter.
- Lösningsmål gäller endast för lösningar som tillhandahålls av Microsoft.  Den gäller inte för lösningar [skapats av dig själv eller partner](solutions-creating.md).
- Du kan bara filtrera ut agents som ansluter direkt till Azure Monitor.  Lösningar distribuerar automatiskt till alla eventuella agenter som en del av en ansluten hanteringsgrupp för Operations Manager oavsett om de är med i en av omfattningskonfigurationen.

### <a name="exceptions"></a>Undantag
Lösningsmål kan inte användas med följande lösningar trots att de passar de angivna villkoren.

- Agenten bedömningen av replikeringstillståndet

## <a name="next-steps"></a>Nästa steg
- Läs mer om hur du övervakar lösningar, inklusive de lösningar som är tillgängliga för installation i miljön vid [lägga till Azure Log Analytics övervakningslösningar till din arbetsyta](solutions.md).
- Läs mer om hur du skapar datorgrupper i [datorgrupper i Azure Monitor logga frågor](../platform/computer-groups.md).
