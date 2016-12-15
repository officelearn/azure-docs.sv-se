---
title: "Övervakning av säkerhetshälsa i Azure Security Center | Microsoft Docs"
description: "I den här artikeln får du hjälp att komma igång med övervakningsfunktionerna i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: aac0354a8fbfb73bab3e7b5da7d9aefa69c9eb16
ms.openlocfilehash: 5251ab851dd2934050747cd3ad7b2c4f0b9e98bb


---
# <a name="security-health-monitoring-in-azure-security-center"></a>Övervakning av säkerhetshälsa i Azure Security Center
I den här artikeln berättar vi hur du använder övervakningsfunktionerna i Azure Security Center för att övervaka att fastställda principer efterlevs.

## <a name="what-is-security-health-monitoring"></a>Vad är övervakning av säkerhetshälsa?
Ofta tänker vi oss att övervakning är att sitta och titta och vänta på att något ska hända som vi sedan ska åtgärda. Säkerhetsövervakning handlar om att ha en proaktiv strategi där resurserna hela tiden kontrolleras så att system som inte uppfyller organisationens normer och principer hittas.

## <a name="monitoring-security-health"></a>Övervakning av säkerhetshälsa
När du har aktiverat [säkerhetsprinciper](security-center-policies.md) för resurser i en prenumeration analyserar Security Center resursernas säkerhet för upptäckt av eventuella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt. Det kan ta en timme eller mer för information om konfiguration av virtuell dator, till exempel uppdatera säkerhetsstatus och konfiguration av operativsystem, att bli tillgänglig. Du kan se säkerhetsstatus för dina resurser och eventuella problem i bladet **Resurssäkerhetshälsa**. Problemen visas även i en lista på bladet **Recommendations (Rekommendationer)**.

Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

I rutan **Resurssäkerhetshälsa** kan du övervaka dina resursers säkerhetstillstånd. I följande exempel ser du att ett antal problem har hög och medelhög allvarlighetsgrad och kräver uppmärksamhet. Vilka säkerhetsprinciper som är aktiverade påverkar vad det är som övervakas.

![Panelen resurssäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig1-new4.png)

Om en säkerhetsrisk som måste åtgärdas upptäcks, till exempel en virtuell dator där det saknas säkerhetsuppdateringar eller ett undernät som inte har någon [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md), ser du det här.

### <a name="monitor-virtual-machines"></a>Övervakning av virtuella datorer
När du klickar på **Virtuella datorer** i panelen **Resurssäkerhetshälsa** visar bladet **Virtuella datorer** mer information om integration, förebyggande åtgärder och en lista över alla virtuella datorer som Security Center övervakar såsom visas på följande skärmbild.

![Systemuppdatering av den virtuella datorn saknas](./media/security-center-monitoring/security-center-monitoring-fig2-ga.png)

* Förberedande åtgärder
* Rekommendationer för virtuella datorer
* Virtuella datorer

Om du klickar på de olika alternativen i de olika avsnitten, visas mer information om de åtgärder som rekommenderas för att åtgärda problemet. Följande avsnitt förklarar dessa områden i detalj.

#### <a name="monitoring-recommendations"></a>Rekommendationer för övervakning
I det här avsnittet visas hur många virtuella datorer som initierats för datainsamling och deras aktuella status. När datainsamling har initierats för alla virtuella datorer kan de börja ta emot säkerhetsprinciper från Security Center. Om du klickar på det här alternativet öppnas bladet **Installationsstatus för datainsamling**, där du kan se namnen på alla virtuella datorer och aktuell status för datainsamlingen i kolumnen **INSTALLATIONSSTATUS** såsom visas på följande skärmbild.

![Initieringsstatus för virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig3-ga.png)

#### <a name="virtual-machine-recommendations"></a>Rekommendationer för virtuella datorer
I den här delen finns ett antal [rekommendationer för de virtuella datorer](security-center-virtual-machine-recommendations.md) som övervakas via Azure Security Center. Den första kolumnen visar rekommendationen. Den andra kolumnen visar det totala antalet virtuella datorer som påverkas av den här rekommendationen. Den tredje kolumnen visar hur allvarligt problemet är, såsom illustreras på följande skärmbild.

![Rekommendationer för virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig4-ga.png)

> [!NOTE]
> Endast virtuella datorer som har minst en offentlig slutpunkt visas i bladet **Nätverkshälsa** i listan över **Nätverkstopologi**.
> 
> 

Varje rekommendation har en uppsättning åtgärder som du kan utföra när du klickar på den. Om du till exempel klickar på **Systemuppdateringar saknas** öppnas bladet **Systemuppdateringar saknas**. Det visar de virtuella datorer som saknar uppdateringar och allvarlighetsgraden för saknad uppdatering såsom visas på följande skärmbild.

![Saknade systemuppdateringar för virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig5-ga.png)

I bladet **Systemuppdateringar saknas** finns en tabell med följande information:

* **VIRTUAL MACHINE (VIRTUELL DATOR)**: Här står namnet på de virtuella datorer som det saknas uppdateringar på.
* **SYSTEM UPDATES (SYSTEMUPPDATERINGAR)**: Här visas antalet systemuppdateringar som saknas.
* **SENASTE GENOMSÖKNING**: Här står den tid då den virtuella datorn senast genomsöktes för kontroll av uppdateringar.
* **STATE (STATUS)**: Här visas det aktuella tillståndet för rekommendationen:
  * **Öppen**: Rekommendationen har inte utförts än.
  * **Pågående**: Rekommendationen håller på att utföras och ingen åtgärd behövs från din sida.
  * **Löst**: Rekommendation har redan slutförts. (När problemet har lösts, inaktiveras posten).
* **SEVERITY (ALLVARLIGHETSGRAD)**: Här visas hur viktig rekommendationen är.
  * **Hög**: Det finns en säkerhetsrisk i en viktig resurs (program, virtuell dator eller nätverkssäkerhetsgrupp) som måste åtgärdas.
  * **Medelhög**: Det behövs mindre viktiga eller ytterligare åtgärder för att slutföra en process eller åtgärda en säkerhetsrisk.
  * **Low (Låg)**: Det finns en säkerhetsrisk som bör åtgärdas, men det måste inte göras omedelbart. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)

För att visa information om rekommendationen klickar du på namnet på den virtuella datorn. Ett nytt blad för den virtuella datorn öppnas med en lista med uppdateringar såsom visas på följande skärmbild.

![Uppdateringar för en specifik virtuell dator saknas](./media/security-center-monitoring/security-center-monitoring-fig6-ga.png)

> [!NOTE]
> Säkerhetsrekommendationerna här är desamma som de som visas i bladet **Rekommendationer**. Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md). Ovanstående gäller inte bara för virtuella datorer, utan för alla resurser i panelen **Resurshälsa**.
> 
> 

#### <a name="virtual-machines-section"></a>Delen Virtuella datorer
I delen med virtuella datorer får du en överblick över alla virtuella datorer och rekommendationer. Varje kolumn representerar en uppsättning rekommendationer såsom visas på följande skärmbild:

![Översikt över alla virtuella datorer och rekommendationer](./media/security-center-monitoring/security-center-monitoring-fig7-ga.png)

Genom ikonerna under de olika rekommendationerna kan du snabbt se vilka virtuella datorer som behöver åtgärdas och typ av rekommendation.

I föregående exempel har en virtuell dator en kritisk rekommendation för slutpunktsskydd. Klicka på den för att få mer information om den virtuella datorn. Ett nytt blad som öppnas representerar den här virtuella datorn såsom visas på följande skärmbild.

![Säkerhetsdetaljer för virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig8-ga.png)

Det här bladet visar säkerhetsinformation om den virtuella datorn. Längst ned ser du den rekommenderade åtgärden och allvarlighetsgraden på de olika problemen.

#### <a name="cloud-services-preview-section"></a>Avsnittet Molntjänster (förhandsversion)
Hälsostatus för molntjänster anges i panelen med **Säkerhetshälsa** för virtuella datorer. En rekommendation skapas när operativsystemets version är inaktuell såsom visas på följande skärmbild:

![Hälsostatus för molntjänster](./media/security-center-monitoring/security-center-monitoring-fig8-new2.png)

Du måste följa anvisningarna i rekommendationen för att uppdatera versionen av operativsystemet. Om du till exempel klickar på den röda aviseringen i WebRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) eller WorkerRole1 (kör Windows Server med din webbapp automatiskt distribuerad till IIS) öppnas ett nytt blad med mer information om den här rekommendationen såsom visas på följande skärmbild:

![Molntjänstinformation](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Om du vill få en mer ingående förklaring av den här rekommendationen klickar du på **Uppdatera OS-version** under kolumnen **BESKRIVNING**. Bladet **Uppdatera OS-version (förhandsgranskning)** öppnas med mer information.

![Molntjänstrekommendationer](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>Övervakning av virtuella nätverk
Om du klickar på **Nätverk** i rutan **Resurssäkerhetshälsa** öppnas bladet **Nätverk** med mer information som på följande skärmbild:

![Bladet Nätverk](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Nätverksrekommendationer
Liksom vid resurshälsoinformationen för virtuella datorer visas på det här bladet en sammanfattande lista längst upp och en lista med övervakade nätverk längst ned.

I listan med detaljerad status för nätverk visas potentiella säkerhetsproblem och [rekommendationer](security-center-network-recommendations.md). Följande säkerhetsproblem kan visas:

* Nästa generations brandvägg inte installerad
* Nätverkssäkerhetsgrupper i undernät inte aktiverade
* Nätverkssäkerhetsgrupper på virtuella datorer är inte aktiverade
* Begränsa extern åtkomst genom offentlig extern slutpunkt
* Felfria internetuppkopplade slutpunkter

När du klickar på en rekommendation öppnas ett nytt blad med mer information om rekommendationen såsom visas i följande exempel.

![Information om en rekommendation i bladet Nätverk](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

I det här exemplet ser du bladet **Konfigurera saknade nätverkssäkerhetsgrupper för undernät**, där det finns en lista med undernät och virtuella datorer som saknar skyddet som nätverkssäkerhetsgrupper ger. Om du klickar på det undernätet som du vill använda för nätverkssäkerhetsgruppen öppnas ett annat blad.

I bladet **Välj nätverkssäkerhetsgrupp** väljer du den mest lämpade nätverkssäkerhetsgruppen för undernätet eller skapar en ny grupp.

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

I den här topologiska vyn har du [virtuella nätverk](../virtual-network/virtual-networks-overview.md), [virtuella nätverksgatewayer](../vpn-gateway/vpn-gateway-site-to-site-create.md) och [virtuella nätverk (klassiska)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) på översta nivån. På andra nivån hittar du undernät och på den tredje de virtuella datorer som finns på dessa undernät. I den högra kolumnen visas aktuell status för resursernas nätverkssäkerhetsgrupper, som i följande exempel:

![Status för nätverkssäkerhetsgruppen i avsnittet Nätverkstopologi](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

Längst ned på det här bladet finns rekommendationer för den här virtuella datorn som liknar vad som beskrivs ovan. Om du klickar på en av rekommendationerna får du mer information och kan utföra den säkerhetskontroll eller säkerhetskonfiguration som behövs.

### <a name="monitor-data"></a>Övervaka data
Om du klickar på **Data** i rutan **Resurssäkerhetshälsa** öppnas bladet **SQL** med rekommendationer för problem med sådant som att funktionerna för granskning och transparent datakryptering inte är aktiverade. Här finns även [rekommendationer](security-center-sql-service-recommendations.md) gällande databasens allmänna hälsoläge.

![SQL-resurshälsa](./media/security-center-monitoring/security-center-monitoring-fig13-ga.png)

Du kan klicka på en rekommendation och få mer information om ytterligare åtgärder för att lösa ett problem. I följande exempel har vi klickat på rekommendationen **Databasgranskning inte aktiverad**.

![Information om en SQL-rekommendation](./media/security-center-monitoring/security-center-monitoring-fig14-ga.png)

I bladet **Enable Auditing on SQL databases (Aktivera granskning av SQL-databaser)** visas följande information:

* en lista med SQL-databaser
* vilken server de ligger på
* information om huruvida inställningen har ärvts från servern eller om den är unik för databasen
* aktuell status
* problemets allvarlighetsgrad

Om du klickar på databasen för att utföra rekommendationen öppnas bladet **Granskning och hotidentifiering** som du ser på följande skärmbild.

![Bladet Granskning och hotidentifiering](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Du aktiverar granskning genom att välja **PÅ** under alternativet **Granskning**.

### <a name="monitor-applications"></a>Övervakning av program
Om din arbetsbelastning i Azure innehåller program som ligger på [virtuella datorer (skapade via Azure Resource Manager)](../azure-resource-manager/resource-manager-deployment-model.md) med exponerade webbportar (TCP-portarna 80 och 443) kan de övervakas i Security Center. Då identifieras potentiella säkerhetsproblem och du får rekommendationer om hur problemen kan lösas. När du klickar på panelen **Program** öppnas bladet **Program** och ett antal rekommendationer visas i avsnittet **Application recommendations** (Programrekommendationer). Här visas även program uppdelade efter värd/virtuell IP-adress såsom visas på följande skärmbild.

![Programsäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig16-ga.png)

Precis som med de andra rekommendationerna kan du klicka på en rekommendation för att få se mer information om problemet och om hur du kan åtgärda det. I exemplet på följande bild visas ett program som identifierats som en oskyddad webbapp. När du klickar på webbappen som klassats som oskyddad öppnas ett nytt blad med följande alternativ:

![Information om en app som inte är säker](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Det här bladet har en lista över alla rekommendationer för det här programmet. När du klickar på rekommendationen **Lägg till en brandvägg för webbaserade program** öppnas bladet **Lägg till en brandvägg för webbaserade program** med alternativ för att installera en brandvägg för webbaserade program (WAF) från en partner såsom visas på följande skärmbild.

![Dialogrutan Lägg till en brandvägg för webbaserade program](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Se även
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.




<!--HONumber=Dec16_HO1-->


