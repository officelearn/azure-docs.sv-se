---
title: Övervakning av säkerhetshälsa i Azure Security Center | Microsoft Docs
description: I det här dokumentet får du hjälp att komma igång med övervakningsfunktionerna i Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2016
ms.author: yurid

---
# Övervakning av säkerhetshälsa i Azure Security Center
I det här dokumentet berättar vi hur du använder övervakningsfunktionerna i Azure Security Center för att övervaka att fastställda principer efterlevs.

## Vad är övervakning av säkerhetshälsa?
Ofta tänker vi oss att övervakning är att sitta och titta och vänta på att något ska hända som vi sedan ska åtgärda. Säkerhetsövervakning handlar om att ha en proaktiv strategi där resurserna hela tiden kontrolleras så att system som inte uppfyller organisationens normer och principer hittas.

## Övervakning av säkerhetshälsa
När du har aktiverat [säkerhetsprinciper](security-center-policies.md) för resurser i en prenumeration analyseras resursernas säkerhet för upptäckt av eventuella säkerhetsrisker.  Information om nätverkskonfigurationen får du direkt, medan det kan ta upp till en timme innan du får konfigurationsinformation om virtuella datorer, som status för säkerhetsuppdateringar och operativsystemkonfigurationer. Du kan se säkerhetsstatus för dina resurser, samt om det finns några problem, i bladet **Resource Security Health (Resurssäkerhetshälsa)**. Problemen visas även i en lista på bladet **Recommendations (Rekommendationer)**.

Mer information om hur du utför rekommendationerna finns i artikeln om att [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

I rutan **Resources security health (Resurssäkerhetshälsa)** kan du övervaka dina resursers säkerhetstillstånd. I exemplet nedan kan du se att det finns ett antal problem med hög eller medelhög allvarlighetsgrad som behöver åtgärdas. Vilka säkerhetsprinciper som är aktiverade påverkar vad det är som övervakas.

![Resursernas hälsa](./media/security-center-monitoring/security-center-monitoring-fig1-new4.png)

Om en säkerhetsrisk som måste åtgärdas upptäcks, till exempel en virtuell dator där det saknas säkerhetsuppdateringar eller ett undernät som inte har någon [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md), ser du det här.

### Övervakning av virtuella datorer
Om du klickar på **Virtual machines (Virtuella datorer)** i rutan **Resources security health (Resurssäkerhetshälsa)** öppnas bladet **Virtual machines (Virtuella datorer)** med mer information om förberedande åtgärder och skyddsåtgärder, liksom en lista med alla virtuella datorer som övervakas via Security Center, så som visas på bilden nedan.

![Systemuppdatering ej gjord på virtuell dator](./media/security-center-monitoring/security-center-monitoring-fig2-ga.png)

* Förberedande åtgärder
* Rekommendationer för virtuella datorer
* Virtuella datorer

Om du klickar på de olika alternativen i de olika delarna visas mer information om de åtgärder som rekommenderas för att åtgärda problemet. I avsnitten nedan ges en utförligare förklaring av de här funktionerna.

#### Rekommendationer för övervakning
I den här delen visas hur många virtuella datorer som initierats för datainsamling och deras aktuella status. När datainsamling har initierats för alla virtuella datorer kan de börja ta emot säkerhetsprinciper från Security Center. Om du klickar på det här alternativet öppnas bladet **Data collection installation status** (Status för installation av datainsamling), där du kan se namnen på alla virtuella datorer och aktuell status för datainsamlingen i kolumnen **INSTALLATION STATUS** (INSTALLATIONSSTATUS) enligt bilden nedan.

![Initieringsstatus](./media/security-center-monitoring/security-center-monitoring-fig3-ga.png)

#### Rekommendationer för virtuella datorer
I den här delen finns ett antal [rekommendationer för de virtuella datorer](security-center-virtual-machine-recommendations.md) som övervakas via Azure Security Center. I första kolumnen visas rekommendationerna, i den andra antalet virtuella datorer som påverkas av aktuell rekommendation, och i den tredje visas problemets allvarlighetsgrad som på bilden nedan.

![Rekommendationer för virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig4-ga.png)

> [!NOTE]
> Endast virtuella datorer med minst en offentlig slutpunkt visas i bladet Networking Health (Hälsa för nätverk) i listan över nätverkstopologi.
> 
> 

För varje rekommendation finns ett antal åtgärder som du kan utföra genom att klicka på de olika rekommendationerna. Om du till exempel klickar på **Missing system updates (Systemuppdateringar saknas)** öppnas bladet **Missing system updates (Systemuppdateringar saknas)**. Här finns en lista över virtuella datorer där korrigeringar inte installerats och hur allvarligt det klassas som, så som på bilden nedan.

![Systemuppdatering ej gjord](./media/security-center-monitoring/security-center-monitoring-fig5-ga.png)

I bladet **Missing system updates (Systemuppdateringar saknas)** finns en tabell med följande information:

* **VIRTUAL MACHINE (VIRTUELL DATOR)**: Här står namnet på de virtuella datorer som det saknas uppdateringar på.
* **SYSTEM UPDATES (SYSTEMUPPDATERINGAR)**: Här visas antalet systemuppdateringar som saknas.
* **LAST SCAN TIME (SENASTE GENOMSÖKNING)**: Här står den tid då den virtuella datorn senast genomsöktes för kontroll av uppdateringar.
* **STATE (STATUS)**: Här visas det aktuella tillståndet för rekommendationen:
  * **Open (Ej utförd)**: Rekommendationen har inte utförts än.
  * **In Progress (Pågående)**: Rekommendationen håller på att utföras. Ingen åtgärd behövs från din sida.
  * **Resolved (Utförd)**: Rekommendationen har redan genomförts (när problemet har lösts blir raden grå).
* **SEVERITY (ALLVARLIGHETSGRAD)**: Här visas hur viktig rekommendationen är.
  * **High (Hög)**: Det finns en säkerhetsrisk i en viktig resurs (program, virtuell dator eller nätverkssäkerhetsgrupp) som måste åtgärdas.
  * **Medium (Medelhög)**: Det behövs mindre viktiga eller ytterligare åtgärder för att slutföra en process eller åtgärda en säkerhetsrisk.
  * **Low (Låg)**: Det finns en säkerhetsrisk som bör åtgärdas, men det måste inte göras omedelbart. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)

Om du vill se mer information om en rekommendation klickar du på den virtuella datorn i fråga. Då öppnas ett nytt blad med en lista med uppdateringar för den datorn.

![Systemuppdatering ej gjord på viss virtuell dator](./media/security-center-monitoring/security-center-monitoring-fig6-ga.png)

> [!NOTE]
> Säkerhetsrekommendationerna här är desamma som de som visas i bladet Rekommendationer. Se artikeln om att [utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md) för mer information om att utföra rekommendationer. Ovanstående gäller inte bara för virtuella datorer, utan för alla resurser i resurshälsorutan.
> 
> 

#### Delen Virtuella datorer
I delen med virtuella datorer får du en överblick över alla virtuella datorer och rekommendationer. I varje kolumn finns en typ av rekommendationer så som visas på bilden nedan:

![Virtuella datorer](./media/security-center-monitoring/security-center-monitoring-fig7-ga.png)

Genom ikonerna under de olika rekommendationerna kan du snabbt se vilka virtuella datorer som behöver åtgärdas och vilken allvarlighetsgrad det är på rekommendationen.

I exemplet ovan är det en virtuell dator som har en viktig rekommendation angående slutpunktsskydd. Om du vill ha mer information om en virtuell dator klickar du på datorn i fråga. Då öppnas ett nytt blad om just den datorn som på bilden nedan.

![Säkerhetsinformation om virtuell dator](./media/security-center-monitoring/security-center-monitoring-fig8-ga.png)

Här ser du säkerhetsinformation om datorn. Längst ned ser du den rekommenderade åtgärden och allvarlighetsgraden på de olika problemen.

#### Avsnittet Molntjänster (förhandsversion)
Hälsostatus för molntjänster anges i panelen med säkerhetshälsa för virtuella datorer. En rekommendation skapas när OS-versionen är inaktuell enligt nedan: 

![Molntjänster](./media/security-center-monitoring/security-center-monitoring-fig8-new2.png)

Du måste följa anvisningarna i rekommendationen för att uppdatera OS-versionen. Om du till exempel klickar på den röda aviseringen i webbrollen (kör Windows Server med ditt webbprogram automatiskt distribuerat till IIS) eller arbetsrollen (kör Windows Server med ditt webbprogram automatiskt distribuerat till IIS) öppnas ett nytt blad med mer information om den här rekommendationen enligt bilden nedan:

![Molntjänstinformation](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png) 

Om du vill få en mer ingående förklaring av den här rekommendationen klickar du på Uppdatera **OS-version** under kolumnen **BESKRIVNING**. Bladet **Uppdatera OS-version (Förhandsversion)** öppnas med mer information.

![Molntjänstrekommendationer](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### Övervakning av virtuella nätverk
Om du klickar på **Networking (Nätverk)** i rutan **Resources security health (Resurssäkerhetshälsa)** öppnas bladet **Networking (Nätverk)** med mer information som på bilden nedan:

![Nätverk](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### Nätverksrekommendationer
Liksom med resurshälsoinformationen för virtuella datorer visas på det här bladet en sammanfattande lista längst upp och en lista med övervakade nätverk längst ned.

I listan med detaljerad status för nätverk visas potentiella säkerhetsproblem och [rekommendationer](security-center-network-recommendations.md). Följande säkerhetsproblem kan visas:

* Nästa generations brandvägg inte installerad
* Nätverkssäkerhetsgrupper i undernät inte aktiverade
* Nätverkssäkerhetsgrupper på virtuella datorer inte aktiverade
* Begränsa extern åtkomst genom offentlig extern slutpunkt
* Felfria internetuppkopplade slutpunkter

Om du klickar på någon av de här rekommendationerna öppnas ett nytt blad med mer information om den rekommendationen som på bilden nedan:

![Begränsa slutpunkt](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

I det här exemplet ser du bladet **Configure Missing Network Security Groups for Subnets (Konfigurera saknade nätverkssäkerhetsgrupper för undernät)**, där det finns en lista med undernät som saknar skyddet som nätverkssäkerhetsgrupper ger. Om du klickar på det undernät som du vill förse med det här skyddet öppnas ett nytt blad.

I bladet **Choose network security group (Välj nätverkssäkerhetsgrupp)** väljer du den mest lämpade nätverkssäkerhetsgruppen för undernätet eller skapar en ny grupp. 

#### Delen med internetuppkopplade slutpunkter
I delen **Internet facing endpoints (Internetuppkopplade slutpunkter)** ser du de virtuella datorer som är konfigurerade med en internetuppkopplad slutpunkt och aktuell status för dem.

![Internetuppkopplad slutpunkt](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

I den här tabellen visas slutpunktsnamnen, det vill säga de virtuella datorerna, IP-adressen som används på internet och aktuell allvarlighetsgrad för nätverkssäkerhetsgruppen och nästa generations brandvägg. Tabellen är sorterad enligt allvarlighetsgrad enligt följande:

* Röd (högst upp): hög prioritet och bör åtgärdas omedelbart 
* Orange: medelhög prioritet och bör åtgärdas så snart som möjligt
* Grön (längst ned): god status

#### Delen med nätverkstopologi
I delen **Networking topology (Nätverkstopologi)** visas en hierarkisk vy över resurserna som på bilden nedan:

![Nätverkstopologi](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Tabellen är sorterad (virtuella datorer och undernät) enligt allvarlighetsgrad enligt följande:

* Röd (högst upp): hög prioritet och bör åtgärdas omedelbart 
* Orange: medelhög prioritet och bör åtgärdas så snart som möjligt
* Grön (längst ned): god status

I den här topologiska vyn har du [virtuella nätverk](../virtual-network/virtual-networks-overview.md), [virtuella nätverksgateways](../vpn-gateway/vpn-gateway-site-to-site-create.md) och [virtuella nätverk (klassiska)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) på översta nivån. På andra nivån hittar du undernät och på den tredje de virtuella datorer som finns på de undernäten. I den högra kolumnen visas aktuell status för resursernas nätverkssäkerhetsgrupper (NSG), som i följande exempel:

![Nätverksträd](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

Längst ned på bladet hittar du rekommendationerna för den här virtuella datorn, som ser ut ungefär så som beskrivits ovan. Om du klickar på en av rekommendationerna får du mer information och kan utföra den säkerhetskontroll eller säkerhetskonfiguration som behövs.

### Övervaka data
Om du klickar på **Data** i rutan **Resources security health (Resurssäkerhetshälsa)** öppnas bladet **SQL** med rekommendationer för problem med sådant som att funktionerna för granskning och transparent datakryptering inte är aktiverade. Här finns även [rekommendationer](security-center-sql-service-recommendations.md) gällande databasens allmänna hälsoläge.

![SQL-resurshälsa](./media/security-center-monitoring/security-center-monitoring-fig13-ga.png)

Du kan klicka på de olika rekommendationerna och få mer information om vilka åtgärder som bör vidtas för att lösa problemet. I exemplet nedan har vi klickat på rekommendationen **Database Auditing not enabled (Databasgranskning inte aktiverad)**.

![SQL-resurshälsa](./media/security-center-monitoring/security-center-monitoring-fig14-ga.png)

I bladet **Enable Auditing on SQL databases (Aktivera granskning av SQL-databaser)** visas följande information:

* en lista med SQL-databaser
* vilken server de ligger på
* information om huruvida inställningen har ärvts från servern eller om den är unik för databasen
* aktuell status
* problemets allvarlighetsgrad

Om du klickar på databasen för att utföra rekommendationen öppnas bladet **Auditing & Threat detection (Granskning och hotidentifiering)** som du ser på bilden nedan.

![SQL-resurshälsa](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Du aktiverar granskning genom att helt enkelt klicka på **ON** (PÅ) under alternativet **Auditing** (Granskning).

### Övervakning av program
Om din arbetsbelastning i Azure innehåller program som ligger på [Resource Manager-distribuerade virtuella datorer](../resource-manager-deployment-model.md) med exponerade webbportar (TCP-portarna 80 och 443) kan de övervakas i Security Center. Då identifieras potentiella säkerhetsproblem och du får rekommendationer om hur problemen kan lösas Om du klickar på rutan **Applications (Program)** öppnas bladet **Applications (Program)** med olika rekommendationer i den översta delen med skyddsåtgärder. Här visas även program uppdelade efter värd/virtuell IP-adress som på bilden nedan.

![Programsäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig16-ga.png)

Precis som med de andra rekommendationerna kan du klicka på dem för att få se mer information om problemet och om hur du kan åtgärda det. I exemplet på bilden nedan visas ett program som identifierats som ett oskyddat webbprogram. När du klickar på webbprogrammet som klassats som oskyddat öppnas ett nytt blad med följande alternativ:

![Program](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Det här bladet har en lista över alla rekommendationer för det här programmet. När du klickar på rekommendationen **Add a web application firewall ** (Lägg till en brandvägg för webbaserade program) öppnas bladet **Add a Web Application Firewall** (Lägg till en brandvägg för webbaserade program) med alternativ för att installera en brandvägg för webbprogram (WAF) från tredje part så som visas på bilden nedan.

![Lägg till brandvägg för webbaserade program](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## Se även
I det här avsnittet har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure

<!--HONumber=Sep16_HO4-->


