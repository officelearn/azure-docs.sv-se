---
title: Säkerhetsövervakning i Azure Security Center | Microsoft Docs
description: I den här artikeln får du hjälp att komma igång med övervakningsfunktionerna i Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: dc362306e20b4d39aa73a552e47cbcbd3037edbd
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063399"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Övervakning av säkerhetshälsa i Azure Security Center
I den här artikeln berättar vi hur du använder övervakningsfunktionerna i Azure Security Center för att övervaka att fastställda principer efterlevs.

## <a name="what-is-security-health-monitoring"></a>Vad är övervakning av säkerhetshälsa?
Ofta tänker vi oss att övervakning är att sitta och titta och vänta på att något ska hända som vi sedan ska åtgärda. Säkerhetsövervakning handlar om att ha en proaktiv strategi där resurserna hela tiden kontrolleras så att system som inte uppfyller organisationens normer och principer hittas.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
När du har aktiverat [säkerhetsprinciper](security-center-policies.md) för resurser i en prenumeration analyserar Security Center resursernas säkerhet för upptäckt av eventuella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt. Beroende på antalet virtuella datorer och datorer som du har med agenten installerad kan det ta en timme eller mer att samla in information om de virtuella datorerna och datorns konfiguration, t.ex. status för säkerhetsuppdatering och operativsystemkonfiguration, så att informationen blir tillgänglig. Du kan se säkerhetsstatus för dina resurser och eventuella problem i bladet **Prevention** (Skydd). Problemen visas även i en lista på panelen **Recommendations (Rekommendationer)**.

Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

Under **Övervakning av resurshälsa** kan du övervaka säkerhetsstatus för dina resurser. I följande exempel kan du se att det totala antalet problem som identifierats visas på panelen för varje resurs (compute och appar, nätverk, datasäkerhet samt identitet och åtkomst).

![Panelen resurssäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>Övervaka compute och appar
Mer information finns i [Skydda dina datorer och program i Azure Security Center](security-center-virtual-machine-recommendations.md).

### <a name="monitor-virtual-networks"></a>Övervakning av virtuella nätverk
Om du klickar på **Nätverk** öppnas bladet **Nätverk** med mer information som på följande skärmbild:

![Bladet Nätverk](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Nätverksrekommendationer
Liksom vid resurshälsoinformationen för virtuella datorer visas här en sammanfattande lista längst upp och en lista med övervakade nätverk längst ned.

I listan med detaljerad status för nätverk visas potentiella säkerhetsproblem och [rekommendationer](security-center-network-recommendations.md). Följande säkerhetsproblem kan visas:

* Nästa generations brandvägg inte installerad
* Nätverkssäkerhetsgrupper i undernät inte aktiverade
* Nätverkssäkerhetsgrupper på virtuella datorer är inte aktiverade
* Begränsa extern åtkomst genom offentlig extern slutpunkt
* Felfria internetuppkopplade slutpunkter

När du klickar på en rekommendation visas mer information om rekommendationen som i följande exempel:

![Information om en rekommendation i Nätverk](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

I det här exemplet har **Konfigurera saknade nätverkssäkerhetsgrupper för undernät** en lista med undernät och virtuella datorer som saknar skyddet som nätverkssäkerhetsgrupper ger. Om du klickar på det undernät som du vill använda för nätverkssäkerhetsgruppen visas **Välj nätverkssäkerhetsgrupp**. Här väljer du den mest lämpade nätverkssäkerhetsgruppen för undernätet eller skapar en ny grupp.

#### <a name="internet-facing-endpoints-section"></a>Delen med internetuppkopplade slutpunkter
I delen **Internetuppkopplade slutpunkter** ser du de virtuella datorer som är konfigurerade med en internetuppkopplad slutpunkt och aktuell status för dem.

![Virtuella datorer som konfigurerats med internetuppkopplad slutpunkt och status](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Den här tabellen har det slutpunktsnamn som representerar den virtuella datorn, den internetuppkopplade IP-adressen och den aktuella statusen för allvarlighetsgrad för nätverkssäkerhetsgruppen och NGFW. Tabellen är sorterad efter allvarlighetsgrad:

* Röd (högst upp): hög prioritet och bör åtgärdas omedelbart
* Orange: medelhög prioritet och bör åtgärdas så snart som möjligt
* Grön (längst ned): god status

#### <a name="networking-topology-section"></a>Delen med nätverkstopologi
I delen **Nätverkstopologi** visas en hierarkisk vy över resurserna såsom visas på följande skärmbild:

![Hierarkisk visning över resurser i avsnittet om nätverkstopologi](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Den här tabellen är sorterad (virtuella datorer och undernät) efter allvarlighetsgrad:

* Röd (högst upp): hög prioritet och bör åtgärdas omedelbart
* Orange: medelhög prioritet och bör åtgärdas så snart som möjligt
* Grön (längst ned): god status

I den här topologiska vyn har du [virtuella nätverk](../virtual-network/virtual-networks-overview.md), [virtuella nätverksgatewayer](/vpn-gateway/vpn-gateway-site-to-site-create.md) och [virtuella nätverk (klassiska)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md) på översta nivån. På andra nivån hittar du undernät och på den tredje de virtuella datorer som finns på dessa undernät. I den högra kolumnen visas aktuell status för resursernas nätverkssäkerhetsgrupper, som i följande exempel:

![Status för nätverkssäkerhetsgruppen i avsnittet Nätverkstopologi](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

Längst ned på det här bladet finns rekommendationer för den här virtuella datorn som liknar vad som beskrivs ovan. Om du klickar på en av rekommendationerna får du mer information och kan utföra den säkerhetskontroll eller säkerhetskonfiguration som behövs.

### <a name="monitor-data-security"></a>Övervaka datasäkerhet

När du klickar på **Datasäkerhet** i avsnittet **Skydd** öppnas **Dataresurser** med rekommendationer för SQL och lagring. Här finns även [rekommendationer](security-center-sql-service-recommendations.md) gällande databasens allmänna hälsoläge. Mer information om lagringskryptering finns i [Aktivera kryptering för Azure-lagringskontot i Azure Security Center](security-center-enable-encryption-for-storage-account.md).

![Dataresurser](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Under **SQL-rekommendationer**, kan du klicka på valfri rekommendation och få mer information om ytterligare åtgärder för att lösa ett problem. Följande exempel visar den expanderade rekommendationen **Databasgranskning och hotidentifiering på SQL-databaser**.

![Information om en SQL-rekommendation](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Under **Aktivera granskning på SQL-databaser** visas följande information:

* en lista med SQL-databaser
* vilken server de ligger på
* information om huruvida inställningen har ärvts från servern eller om den är unik för databasen
* aktuell status
* problemets allvarlighetsgrad

När du klickar på databasen för att utföra rekommendationen öppnas **Granskning och hotidentifiering** som på följande skärmbild.

![Granskning och hotidentifiering](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Du aktiverar granskning genom att välja **PÅ** under alternativet **Granskning**.

### <a name="monitor-identity--access"></a>Övervaka identitet och åtkomst

Mer information finns i [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md).

## <a name="see-also"></a>Se även
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
