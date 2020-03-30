---
title: Ansluta Windows säkerhetshändelsedata till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Windows säkerhetshändelsedata till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124970"
---
# <a name="connect-windows-security-events"></a>Ansluta till Windows säkerhetshändelser 

Med security events-anslutningen kan du strömma alla säkerhetshändelser från dina Windows-system (servrar och arbetsstationer, fysiska och virtuella) till din Azure Sentinel-arbetsyta. På så sätt kan du visa Windows-säkerhetshändelser på instrumentpanelerna, använda dem för att skapa anpassade aviseringar och förlita dig på dem för att förbättra dina undersökningar, vilket ger dig mer insikt i organisationens nätverk och utökar dina säkerhetsåtgärder Kapacitet. Du kan välja vilka händelser som ska strömmas bland följande uppsättningar:<a name="event-sets"></a>

- **Alla händelser** – Alla Windows-säkerhets- och AppLocker-händelser.
- **Vanliga** - En standarduppsättning händelser för granskning. En fullständig användare granskningsspår ingår i den här uppsättningen. Den innehåller till exempel både användarloggning och användarloggningshändelser (händelse-ID 4624, 4634). Det finns också granskningsåtgärder som säkerhetsgruppändringar, viktiga domänkontrollant Kerberos-åtgärder och andra typer av händelser i linje med accepterade metodtips.

    Den **gemensamma** händelseuppsättningen kan innehålla vissa typer av händelser som inte är så vanliga.  Detta beror på att den viktigaste punkten i den **gemensamma** uppsättningen är att minska antalet händelser till en mer hanterbar nivå, samtidigt som full verifieringskedja kapacitet.

- **Minimal** - En liten uppsättning händelser som kan tyda på potentiella hot. Den här uppsättningen innehåller inte en fullständig granskningshistorik. Det omfattar endast händelser som kan tyda på en lyckad överträdelse, och andra viktiga händelser som har mycket låga förekomst. Den innehåller till exempel lyckade och misslyckade användarinloggningar (händelse-ID 4624, 4625), men den innehåller inte utloggningsinformation (4634) som, även om den är viktig för granskning, inte är meningsfull för identifiering av överträdelser och har relativt hög volym. Merparten av datavolymen för den här uppsättningen består av inloggningshändelser och processskapande händelser (händelse-ID 4688).

- **Ingen** - Inga säkerhets- eller AppLocker-händelser. (Den här inställningen används för att inaktivera kopplingen.)

    Följande lista innehåller en fullständig uppdelning av händelse-ID:na för säkerhet och appskåp för varje uppsättning:

    | Händelseuppsättning | Insamlade händelse-ID:er |
    | --- | --- |
    | **Minimal** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Gemensamma** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702 , 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793 , 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003 , 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> Security Events-samlingen inom ramen för en enda arbetsyta kan konfigureras från antingen Azure Security Center eller Azure Sentinel, men inte båda. Om du ar in på Azure Sentinel på en arbetsyta som redan kör Azure Security Center och är inställd på att samla in säkerhetshändelser har du två alternativ:
> - Lämna samlingen Säkerhetshändelser i Azure Security Center som den är. Du kommer att kunna fråga och analysera dessa händelser i Azure Sentinel samt i Azure Security Center. Du kommer dock inte att kunna övervaka anslutningens anslutningsstatus eller ändra dess konfiguration i Azure Sentinel. Om detta är viktigt för dig, överväga det andra alternativet.
>
> - [Inaktivera samlingen Säkerhetshändelser](../security-center/security-center-enable-data-collection.md) i Azure Security Center och lägg först till security events-kopplingen i Azure Sentinel. Precis som med det första alternativet kan du fråga efter och analysera händelser i både Azure Sentinel och Azure Security Center, men du kommer nu att kunna övervaka anslutningsstatusen för anslutningen eller ändra dess konfiguration i - och endast i - Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurera windows security events-anslutningsappen

Så här samlar du in dina Windows-säkerhetshändelser i Azure Sentinel:

1. Välj Datakopplingar på **navigeringsmenyn**i Azure Sentinel . Klicka på **Säkerhetshändelser**i listan över kopplingar och sedan på knappen **Öppna kopplingssidan** längst ned till höger. Följ sedan anvisningarna på skärmen under fliken **Instruktioner,** enligt beskrivningen i resten av det här avsnittet.

1. Kontrollera att du har rätt behörighet enligt beskrivningen under **Förutsättningar**.

1. Hämta och installera [Log Analytics-agenten](../azure-monitor/platform/log-analytics-agent.md) (kallas även Microsoft Monitoring Agent eller MMA) på de datorer som du vill strömma säkerhetshändelser till Azure Sentinel för.

    För virtuella Azure-datorer:
    
    1. Klicka på **Installera agent på Azure Windows Virtual Machine**och sedan på länken som visas nedan.
    1. För varje virtuell dator som du vill ansluta klickar du på dess namn i listan som visas till höger och klickar sedan på **Anslut**.

    För windowsdatorer som inte är Azure (fysiska, virtuella på prem eller virtuella i ett annat moln):

    1. Klicka på **Installera agent på icke-Azure Windows Machine**och sedan på länken som visas nedan.
    1. Klicka på lämpliga nedladdningslänkar som visas till höger under **Windows-datorer**.
    1. Med hjälp av den nedladdade körbara filen installerar du agenten på de Windows-system du väljer och konfigurerar den med hjälp av **arbetsyte-ID och nycklar** som visas under de nedladdningslänkar som nämns ovan.

    > [!NOTE]
    >
    > Om du vill tillåta Windows-system utan nödvändig internetanslutning för att fortfarande strömma händelser till Azure Sentinel laddar du ned och installerar **OMS Gateway** på en separat dator, med hjälp av länken längst ned till höger, för att fungera som en proxy.  Du måste fortfarande installera Log Analytics-agenten på varje Windows-system vars händelser du vill samla in.
    >
    > Mer information om det här scenariot finns i [dokumentationen **för Log Analytics gateway** ](../azure-monitor/platform/gateway.md).

    Ytterligare installationsalternativ och mer information finns i [dokumentationen **till Log Analytics-agenten** ](../azure-monitor/platform/agent-windows.md).

1. Välj vilken händelseuppsättning ([Alla, Vanliga eller Minimal](#event-sets)) som du vill strömma.

1. Klicka på **Uppdatera**.

1. Om du vill använda det relevanta schemat `SecurityEvent` i Logganalys för Windows-säkerhetshändelser skriver du i frågefönstret.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta cirka 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows-säkerhetshändelser till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel med hjälp av [inbyggda](tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.

