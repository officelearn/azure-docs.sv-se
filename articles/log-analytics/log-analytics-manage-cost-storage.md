---
title: Hantera kostnaden för data i Azure Log Analytics | Microsoft Docs
description: Lär dig hur du ändrar prisplanen och hanterar data volym och kvarhållning för Log Analytics-arbetsytan i Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 179d5c71a309fd79c23519544d1da6bcc07cda0c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045008"
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>Hantera kostnader genom att kontrollera datavolymer och kvarhållning i Log Analytics

> [!NOTE]
> Den här artikeln beskriver hur du kan kontrollera dina kostnader i Log Analytics genom att ange kvarhållningsperioden för data.  Finns i följande artiklar för relaterad information.
> - [Analysera dataanvändning i Log Analytics](log-analytics-manage-cost-storage.md) beskriver hur du analyserar och Avisera om din dataanvändning.
> - [Övervaka användning och uppskattade kostnader](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) beskriver hur du visar användning och beräknade kostnader för flera Azure övervakningsfunktioner för olika prissättningsmodeller. Det beskriver också hur du ändrar din prissättningsmodell.

Log Analytics har utformats för skalning och stöd för insamling av, indexering och lagra stora mängder data per dag från vilken källa som helst i ditt företag eller distribueras i Azure.  Detta kan vara en primära drivande faktorn för din organisation, är kostnadseffektivitet i slutändan underliggande drivrutinen. Det är också beroende av den valda planen därför det är viktigt att förstå att kostnaden för en Log Analytics-arbetsytan inte är bara baserat på mängden data som samlas in, och hur länge du har valt att lagra data som genereras från dina anslutna källor.  

I den här artikeln granskar vi hur du proaktivt övervakar tillväxt för volymen och lagring av data, och definiera gränser för att kontrollera de associerade kostnaderna. 

Kostnaden för data kan vara betydande beroende på följande faktorer: 

- Antal system, infrastruktur, molnresurser, etc. som du samlar in från 
- Typ av data som skapats av källa, till exempel meddelandeköer, loggar, händelser, säkerhetsrelaterade data eller prestandamått 
- Mängden data som genereras av dessa källor och matas in till arbetsytan 
- Period data finns kvar på arbetsytan  
- Antal hanteringslösningarna aktiverat, datakälla och insamlingsfrekvens 

> [!NOTE]
> I dokumentationen för varje lösning eftersom det ger dig en uppfattning om hur mycket data som samlas in.   

Kunder med ett Enterprise-avtal som signerats före den 1 juli 2018 eller som redan har skapat en Log Analytics-arbetsyta i en prenumeration kan du fortfarande har åtkomst till den *kostnadsfri* plan. Om din prenumeration inte är kopplat till en befintlig EA-registrering i *kostnadsfri* nivån är inte tillgänglig när du skapar en arbetsyta i en ny prenumeration efter 2 April 2018.  Data är begränsad till 7 dagars kvarhållning för den *kostnadsfri* nivå.  För den *fristående* eller *betald* nivån, data som samlas in är tillgänglig under de senaste 31 dagarna. Den *kostnadsfri* nivån har en gräns på 500 MB dagliga datainmatning och om du upptäcker att konsekvent överstiger de mängder som tillåts volym, kan du ändra din arbetsyta till en betald plan för att samla in data utöver den här gränsen. 

> [!NOTE]
> Avgifter tillkommer om du vill välja en längre period för den kostnadsfria nivån. Du kan ändra din Plantyp när som helst och mer information om priser, se [prisinformation](https://azure.microsoft.com/pricing/details/log-analytics/). 

Det finns två sätt mängden data kan vara begränsad och hur kontrollerar dina kostnader, det här är dagliga gräns och datalagring.  

## <a name="review-estimated-cost"></a>Granska uppskattad kostnad
Log Analytics gör det lätt att förstå vad kostnaderna förmodligen baseras på de senaste användningsmönster.  Utför följande steg om du vill göra detta.  

1. Logga in på [Azure-portalen](http://portal.azure.com). 
2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. Välj din arbetsyta i fönstret Log Analytics-prenumerationer och klicka sedan på **användning och uppskattade kostnader** från det vänstra fönstret.<br><br> ![Användning och uppskattade kostnader](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

Härifrån kan du granska din datavolym för månaden. Detta omfattar alla data tas emot och bevaras i Log Analytics-arbetsytan.  Klicka på **användningsinformation** högst upp på sidan för att visa instrumentpanelen för användning med information på datatrender volym av källa, datorer och erbjudande. Visa och ange en daglig högsta gräns eller ändra kvarhållningsperioden klickar du på **Datavolymhantering**.
 
Log Analytics avgifter läggs till i Azure-fakturan. Du kan se information om din Azure fakturerar under avsnittet fakturering i Azure Portal eller i den [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Dagligt tak
När du skapar en Log Analytics-arbetsyta från Azure-portalen och du väljer den *kostnadsfri* plan, den är inställd på en 500 MB per dag gränsen. Det finns ingen gräns för andra priser planer. Du kan konfigurera en daglig högsta gräns och begränsa den dagliga datainmatningen för arbetsytan, men var försiktig eftersom målet inte får vara att träffa den dagliga gränsen.  Annars kan förlora du data under resten av den dagen, vilket kan påverka andra Azure-tjänster och lösningar som vars funktioner kan vara beroende uppdaterad information är tillgänglig i arbetsytan.  Därför kan aviseringar din förmåga att Observera och ta emot när hälsovillkoren av resurser som stödjer IT-tjänster som påverkas.  Den dagliga gränsen är avsedd att användas som ett sätt att hantera den oväntat ökningen av datavolymen från dina hanterade resurser och hålla dig inom dina gränser, eller när du vill att begränsa oplanerade avgifter för arbetsytan.  

När den dagliga gränsen har uppnåtts, stoppar insamlingen av fakturerbara datatyper för resten av dagen. En varning banderoll överst på sidan för den valda Log Analytics-arbetsytan och en åtgärd händelse skickas till den *åtgärden* tabellen **LogManagement** kategori. Insamling av data återupptar när återställningstiden definierats *dagliga gränsen ställs in på*. Vi rekommenderar att definiera en aviseringsregel baserat på den här åtgärden-händelser som konfigurerats för att meddela när den dagliga data gränsen har uppnåtts. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifiera vilka dagliga datagräns definiera 
Granska [Log Analytics-användning och uppskattade kostnader](log-analytics-usage.md) att förstå till trenden för inmatning av data och vad är den dagliga Volymgränsen definiera. Det bör ses med försiktighet, eftersom du inte längre att övervaka dina resurser när gränsen har nåtts. 

### <a name="manage-the-maximum-daily-data-volume"></a>Hantera maximal daglig datavolym 
Följande steg beskriver hur du konfigurerar en gräns för att hantera mängden data som Log Analytics att mata in per dag.  

1. Från arbetsytan, Välj **användning och uppskattade kostnader** i den vänstra rutan.
2. På den **användning och uppskattade kostnader** för den valda arbetsytan och klicka på **Datavolymhantering** högst upp på sidan. 
5. Dagligt tak är **OFF** som standard – klickar du på **på** att aktivera den och ange sedan datavolymen i GB/dag.<br><br> ![Log Analytics konfigurera datagräns](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Avisera när gränsen har nåtts
Medan Vi presenterar en visuell ledtråd i Azure-portalen när tröskeln för ditt data gränsen är uppfyllt, justera det här beteendet inte nödvändigtvis som du hanterar operativa problem som kräver omedelbar uppmärksamhet.  För att få en avisering, kan du skapa en ny aviseringsregel i Azure Monitor.  Mer information finns i [hur du skapar, visa och hantera aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).      

Här följer de rekommenderade inställningarna för aviseringen för att komma igång:

* Mål: Välj din Log Analytics-resurs
* Villkor: 
   * Signalnamn: anpassad loggsökning
   * Sökfråga: åtgärden | där detalj har ”se”
   * Baserat på: antal resultat
   * Villkor: Är större än
   * Tröskelvärde: 0
   * Period: 5 (minuter)
   * Frekvens: 5 (minuter)
* Namn på aviseringsregel: dagliga data nådd
* Allvarlighetsgrad: Varning (Sev 1)

När aviseringen har definierats och gränsen har nåtts kan en avisering har utlösts och utför svaret som definierats i åtgärdsgruppen. Det kan meddela ditt team via e-post och textmeddelanden eller automatisera åtgärder med webhooks, Automation-runbooks eller [integrera med en extern ITSM-lösning](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data 
Följande steg beskriver hur du konfigurerar hur länge log data bevaras av i din arbetsyta.
 
1. Från arbetsytan, Välj **användning och uppskattade kostnader** i den vänstra rutan.
2. På den **användning och uppskattade kostnader** klickar du på **Datavolymhantering** högst upp på sidan.
5. Flytta skjutreglaget för att öka eller minska antalet dagar och klickar sedan på i fönstret **OK**.  Om du använder den *kostnadsfria* nivå, du kommer inte att kunna ändra kvarhållningsperioden för data och du måste uppgradera till betald nivå om du vill styra den här inställningen.<br><br> ![Ändra inställningen för kvarhållning av arbetsyta data](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>Felsökning
**Fråga**: Hur felsöker jag om Log Analytics inte längre att samla in data? 
**Svar**: Om du på den kostnadsfria prisnivån och skicka fler än 500 MB data under en dag, datainsamling stoppas under resten av dagen. Når den dagliga gränsen är en vanlig orsak som Log Analytics slutar att samla in data eller data verkar sakna.  
Log Analytics skapar en händelse av typen igen när datainsamlingen startar och stoppar.  
Kör följande fråga i sökningen för att kontrollera om du når den dagliga gränsen och data som saknas: åtgärden | där OperationCategory == 'Datainsamlingsstatus'   
När datainsamlingen slutar OperationStatus är en varning. När datainsamlingen startar är OperationStatus slutfört.  
I följande tabell beskrivs skäl som stoppar insamling av data och en rekommenderad åtgärd för att återuppta insamling av data:  

|Stoppar orsak samling| Lösning| 
|-----------------------|---------|
|Daglig gräns för kostnadsfria data nått<sup>1</sup>|Vänta tills nästa dag för samlingen att starta om automatiskt eller ändra till en betald prisnivå.|
|Daglig gräns som du definierade i datavolymhanteringen har nåtts|Vänta tills nästa dag för samlingen att starta om automatiskt eller öka den dagliga datavolymen som beskrivs i [hantera maximal daglig datavolym](#manage-the-maximum-daily-volume)|
|Azure-prenumerationen är i ett pausat tillstånd på grund av:<br> Kostnadsfri utvärderingsversion avslutades<br> Azure-pass har upphört att gälla<br> Varje månad utgiftsgränsen har nåtts (till exempel på en MSDN eller Visual Studio-prenumeration)|Konvertera till en betald prenumeration<br> Ta bort gränsen, eller vänta tills begränsningen återställs|

<sup>1</sup> om din arbetsyta finns på den kostnadsfria prisnivån, du är begränsad till att skicka 500 MB data per dag till tjänsten. När du når den dagliga gränsen stoppas insamling av data till nästa dag. Data som skickas när datainsamling har stoppats har indexerats inte och är inte tillgängliga för sökning. När datainsamling återgår sker endast för nya skickade data. 

Log Analytics använder UTC-tid. Återställningstiden varierar mellan arbetsytor att förhindra alla begränsad överskottsavgift arbetsytor start mata in data på samma gång. Om arbetsytan når den dagliga gränsen, bearbetningen återupptas när återställningstiden som definierats i **dagliga gränsen ställs in på**.<br><br> ![Log Analytics begränsa UTC-tidszonen](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Fråga**: hur kan jag bli meddelad när datainsamlingen slutar? 
**Svar**: Använd stegen som beskrivs i *skapa dagliga data gräns* avisering du vill meddelas när datainsamlingen slutar och följ stegen använder stegen som beskrivs i Lägg till åtgärder i Varningsregler konfigurera ett e-post, webhook eller runbook åtgärd för regeln. 

## <a name="next-steps"></a>Nästa steg  

För att avgöra hur mycket data som samlas in, vilka källor skickar och vilka typer av data som skickas för att hantera förbrukning och kostnader, se [analysera dataanvändning i Log Analytics](log-analytics-usage.md).