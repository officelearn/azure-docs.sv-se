---
title: "Säkerhetsövervakning i Azure Security Center | Microsoft Docs"
description: "I den här artikeln får du hjälp att komma igång med övervakningsfunktionerna i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/28/2017
ms.author: yurid
ms.openlocfilehash: a14528013b34b912f4f2e1bc07094c7b20a0f63c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Övervakning av säkerhetshälsa i Azure Security Center
I den här artikeln berättar vi hur du använder övervakningsfunktionerna i Azure Security Center för att övervaka att fastställda principer efterlevs.

## <a name="what-is-security-health-monitoring"></a>Vad är övervakning av säkerhetshälsa?
Ofta tänker vi oss att övervakning är att sitta och titta och vänta på att något ska hända som vi sedan ska åtgärda. Säkerhetsövervakning handlar om att ha en proaktiv strategi där resurserna hela tiden kontrolleras så att system som inte uppfyller organisationens normer och principer hittas.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
När du har aktiverat [säkerhetsprinciper](security-center-policies.md) för resurser i en prenumeration analyserar Security Center resursernas säkerhet för upptäckt av eventuella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt. Beroende på antalet virtuella datorer och datorer som du har med agenten installerad kan det ta en timme eller mer att samla in information om de virtuella datorerna och datorns konfiguration, t.ex. status för säkerhetsuppdatering och operativsystemkonfiguration, så att informationen blir tillgänglig. Du kan se säkerhetsstatus för dina resurser och eventuella problem i bladet **Prevention** (Skydd). Problemen visas även i en lista på panelen **Recommendations (Rekommendationer)**.

Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

I avsnittet **Prevention** (Förebygga) kan du övervaka resursernas säkerhetsstatus. I följande exempel visas varje resurspanel (beräkning, nätverk, lagring data och program) med det totala antalet problem som har identifierats.

![Panelen resurssäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>Övervaka beräkning
Du kan se tre flikar när du klickar på panelen **Beräkna**:

- **Översikt**: Övervakning och rekommendationer.
- **Virtuella datorer och datorer**: Lista över alla virtuella datorer, datorer och det aktuella säkerhetstillståndet.
- **Cloud Services**: Lista över alla webb- och arbetsroller som övervakas av Security Center.

![Systemuppdatering av den virtuella datorn saknas](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

På varje flik kan det finnas olika alternativ, och i de olika avsnitten kan du välja ett individuellt alternativ och visa mer information om de åtgärder som rekommenderas för att åtgärda problemet.

#### <a name="monitoring-recommendations"></a>Rekommendationer för övervakning
I det här avsnittet visas hur många virtuella datorer och datorer som har initierats för automatisk etablering och deras aktuella status. När du klickar på den här posten öppnas **Övervaka problem med hälsotillstånd**.

![Övervaka problem med hälsotillstånd](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)

Virtuella datorer och vanliga datorer som Security Center inte kan övervaka visas i en lista. Välj en virtuell eller vanlig dator för detaljerad information. **Övervakningstillståndet** definierar problemet.

#### <a name="recommendations"></a>Rekommendationer
I den här delen finns ett antal [rekommendationer för de virtuella datorer och datorer](security-center-virtual-machine-recommendations.md) som övervakas via Azure Security Center. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet virtuella datorer och datorer som påverkas av den här rekommendationen. Den tredje kolumnen visar hur allvarligt problemet är, såsom illustreras på följande skärmbild:

![Rekommendationer för virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> Endast virtuella datorer som har minst en offentlig slutpunkt visas under **Nätverkshälsa** i listan **Nätverkstopologi**.
>

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du klickar på den. Om du till exempel klickar på **Systemuppdateringar saknas** visas en lista över virtuella datorer och datorer som saknar korrigeringsfiler samt allvarlighetsgrad för uppdateringen som saknas, på det sätt som visas i följande skärmbild:

![Saknade systemuppdateringar för virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

**Systemuppdateringar saknas** innehåller en sammanfattning av kritiska uppdateringar i diagramformat, en för Windows och en för Linux. Den andra delen har en tabell med följande information:

* **NAMN**: Namnet på den uppdatering som saknas.
* **ANTAL VM:AR OCH DATORER**: Det totala antalet virtuella datorer och datorer som saknar denna uppdatering.
* **STATE (STATUS)**: Här visas det aktuella tillståndet för rekommendationen:
  * **Öppen**: Rekommendationen har inte utförts än.
  * **Pågående**: Rekommendationen håller på att utföras och ingen åtgärd behövs från din sida.
  * **Löst**: Rekommendation har redan slutförts. (När problemet har lösts, inaktiveras posten).
* **SEVERITY (ALLVARLIGHETSGRAD)**: Här visas hur viktig rekommendationen är.
  * **Hög**: Det finns en säkerhetsrisk i en viktig resurs (program, virtuell dator eller nätverkssäkerhetsgrupp) som måste åtgärdas.
  * **Medelhög**: Det behövs mindre viktiga eller ytterligare åtgärder för att slutföra en process eller åtgärda en säkerhetsrisk.
  * **Low (Låg)**: Det finns en säkerhetsrisk som bör åtgärdas, men det måste inte göras omedelbart. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)

Om du vill visa information om rekommendationen klickar du på namnet på den uppdatering som saknas.

![Uppdateringar för en specifik virtuell dator saknas](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> Säkerhetsrekommendationerna här är desamma som de som visas i alternativet **Rekommendationer**. Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md). Ovanstående gäller inte bara för virtuella datorer och datorer, utan för alla resurser på panelen **Resource Health**.
>

#### <a name="vms--computers-section"></a>Avsnittet för virtuella datorer och datorer
I delen med virtuella datorer och datorer får du en överblick över alla virtuella datorer och datorer och rekommendationer för dessa. Varje kolumn representerar en uppsättning rekommendationer såsom visas på följande skärmbild:

![Översikt över alla virtuella datorer och rekommendationer](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

Det finns fyra typer av ikoner som visas i listan, dessa beskrivs i den här listan:

![icon1](./media/security-center-monitoring/security-center-monitoring-icon1.png) Icke-Azure-dator.

![icon2](./media/security-center-monitoring/security-center-monitoring-icon2.png) Virtuell dator med Azure Resource Manager.

![icon3](./media/security-center-monitoring/security-center-monitoring-icon3.png) Virtuell dator med klassisk Azure.

![icon4](./media/security-center-monitoring/security-center-monitoring-icon4.png) Virtuella datorer som endast identifieras från arbetsytan som är en del av den visade prenumerationen. Detta omfattar alla virtuella datorer från andra prenumerationer som rapporterar till arbetsytan i denna prenumeration, och virtuella datorer som installerades med SCOM-direktagent och som inte har något resurs-ID.

Genom ikonerna under de olika rekommendationerna kan du snabbt se vilka virtuella datorer och datorer som behöver åtgärdas och typ av rekommendation. Du kan även använda alternativet **Filter** för att välja vilka alternativ du vill se på denna skärm.

![Filter](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

I föregående exempel har en virtuell dator en kritisk rekommendation för slutpunktsskydd. Klicka på den för att få mer information om den virtuella datorn:

![Säkerhetsdetaljer för virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

Här ser du säkerhetsinformation om den virtuella datorn eller datorn. Längst ned ser du den rekommenderade åtgärden och allvarlighetsgraden på de olika problemen.

#### <a name="cloud-services-section"></a>Avsnittet Molntjänster
För molntjänster skapas en rekommendation när operativsystemets version är inaktuell, såsom visas på följande skärmbild:

![Hälsostatus för molntjänster](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

I ett scenario där du har en rekommendation (vilket inte är fallet i föregående exempel) måste du följa stegen i rekommendationen för att uppdatera versionen av operativsystemet. När en uppdatering är tillgänglig får du en avisering (röd eller orange beroende på hur allvarligt problemet är). Om du klickar på den här aviseringen på raden WebRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) eller WorkerRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) visas mer information om den här rekommendationen som i följande skärmbild:

![Molntjänstinformation](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Om du vill få en mer ingående förklaring av den här rekommendationen klickar du på **Uppdatera OS-version** under kolumnen **BESKRIVNING**.

![Molntjänstrekommendationer](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

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

### <a name="monitor-storage--data"></a>Övervaka lagring och data

När du klickar på **Lagring och data** i avsnittet **Skydd** öppnas **Dataresurser** med rekommendationer för SQL och lagring. Här finns även [rekommendationer](security-center-sql-service-recommendations.md) gällande databasens allmänna hälsoläge. Mer information om lagringskryptering finns i [Aktivera kryptering för Azure-lagringskontot i Azure Security Center](security-center-enable-encryption-for-storage-account.md).

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

### <a name="monitor-applications"></a>Övervakning av program

Om din arbetsbelastning i Azure innehåller program som ligger på [virtuella datorer (skapade via Azure Resource Manager)](../azure-resource-manager/resource-manager-deployment-model.md) med exponerade webbportar (TCP-portarna 80 och 443) kan de övervakas i Security Center. Då identifieras potentiella säkerhetsproblem och du får rekommendationer om hur problemen kan lösas. När du klickar på panelen **Program** öppnas **Program** där ett antal rekommendationer visas i avsnittet **Application recommendations** (Programrekommendationer). Här visas även programuppdelningen per värd, IP/domän och om det finns en WAF-lösning installerad:

![Programsäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

Precis som med de andra rekommendationerna kan du klicka på en rekommendation för att få se mer information om problemet och om hur du kan åtgärda det. I exemplet på följande bild visas ett program som identifierats som en oskyddad webbapp. När du klickar på webbappen som klassats som oskyddad blir följande alternativ tillgängligt:

![Information](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Här finns en lista över alla rekommendationer för det här programmet. När du klickar på rekommendationen **Lägg till en brandvägg för webbaserade program** öppnas **Lägg till en brandvägg för webbaserade program** med alternativ för att installera en brandvägg för webbaserade program (WAF) från en partner såsom visas på följande skärmbild.

![Dialogrutan Lägg till en brandvägg för webbaserade program](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Se även
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
