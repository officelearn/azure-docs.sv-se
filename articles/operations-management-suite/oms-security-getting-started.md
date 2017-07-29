---
title: "Komma igång med säkerhets- och granskningslösningen i Operations Management Suite | Microsoft Docs"
description: "Det här dokumentet beskriver hur du kommer igång med funktionerna i säkerhets- och granskningslösningen i Operations Management Suite för att övervaka ditt hybridmoln."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 754796ef-a43e-468a-86c9-04a2eda55b5b
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: get-started-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 5753511d26c06f385fd4ff717d8592c321338172
ms.contentlocale: sv-se
ms.lasthandoff: 06/17/2017

---
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Komma igång med säkerhets- och granskningslösningen i Operations Management Suite
Det här dokumentet hjälper dig att snabbt komma igång med funktionerna i säkerhets- och granskningslösningen i Operations Management Suite (OMS) genom att vägleda dig igenom de olika alternativen.

## <a name="what-is-oms"></a>Vad är OMS?
Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala och molnbaserade infrastruktur. Mer information om OMS finns i artikeln [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>Instrumentpanelen för Säkerhet och granskning i OMS
Säkerhets- och granskningslösningen i OMS ger en heltäckande översikt över din organisations IT-säkerhetsstatus med inbyggda sökfrågor för viktiga problem som kräver din uppmärksamhet. Instrumentpanelen för **Säkerhet och granskning** är startsidan för allt som rör säkerheten i OMS. Den innehåller en översikt över säkerhetsstatusen för dina datorer. Här kan du också visa alla händelser från de senaste 24 timmarna, sju dagarna eller ett annat anpassat tidsintervall. Du öppnar instrumentpanelen för **Säkerhet och granskning** genom att följa dessa steg:

1. Klicka på panelen **Inställningar** till vänster på huvudinstrumentpanelen för **Microsoft Operations Management Suite**.
2. Klicka på alternativet **Säkerhet och granskning** under **Lösningar** på bladet **Inställningar**.
3. Instrumentpanelen för **Säkerhet och granskning** visas:
   
    ![Instrumentpanelen för Säkerhet och granskning i OMS](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Om du använder den här instrumentpanelen för första gången och du inte har några enheter som övervakas av OMS så är inte panelerna ifyllda med data som hämtas från agenten. När du har installerat agenten kan det ta lite tid innan panelerna fylls i och det kan därför se ut som vissa data saknas eftersom de fortfarande laddas upp till molnet.  I detta fall är det normalt att vissa paneler visas utan någon användbar information. Läs [Ansluta Windows-datorer direkt till OMS](https://technet.microsoft.com/library/mt484108.aspx) om du vill ha mer information om hur du installerar OMS-agenten på en Windows-dator och [Ansluta Linux-datorer till OMS](https://technet.microsoft.com/library/mt622052.aspx) om du vill ha mer information om hur du gör på en Linux-dator.

> [!NOTE]
> Agenten samlar in information baserat på de aktuella händelser som är aktiverade, till exempel datornamn, IP-adress och användarnamn. Inga dokument/filer, databasnamn eller privata data samlas dock in.   
> 
> 

Lösningar är en samling logik, visualisering och datahämtningsregler som hjälper till att hantera viktiga kundutmaningar. Säkerhet och granskning är en lösning, andra kan läggas till separat. Artikeln [Lägga till lösningar](https://technet.microsoft.com/library/mt674635.aspx) innehåller mer information om hur du lägger till en ny lösning.

Instrumentpanelen för Säkerhet och granskning i OMS är ordnad i fyra huvudkategorier:

* **Säkerhetsdomäner**: I det här området kan du fortsätta att utforska säkerhetsposter över tid, komma åt utvärderingar av program mot skadlig kod, kontrollera uppdateringar, nätverkssäkerhet, identitets- och åtkomstinformation, datorer med säkerhetshändelser och snabbt komma åt instrumentpanelen för Azure Security Center.
* **Anmärkningsvärda problem**: Med det här alternativet kan du snabbt identifiera antalet aktiva problem och problemens allvarlighetsgrad.
* **Identifieringar (förhandsversion)**: Med det här alternativet kan du identifiera angreppsmönster genom att visualisera säkerhetsvarningar som skapas för dina resurser.
* **Hotinformation**: Med det här alternativet kan du identifiera angreppsmönster genom att visualisera det totala antalet servrar med utgående skadlig IP-trafik, typen av hot och en karta som visar var dessa IP-adresser kommer från. 
* **Vanliga säkerhetsfrågor**: Det här alternativet visar en lista över de vanligaste säkerhetsfrågorna som du kan använda för att övervaka din miljö. När du klickar på någon av dessa frågor öppnas bladet **Sök** med resultatet från frågan.

> [!NOTE]
> Mer information om hur OMS skyddar dina data finns i Så här skyddas dina data med OMS.
> 
> 

## <a name="security-domains"></a>Säkerhetsdomäner
När du övervakar resurser är det viktigt att snabbt kunna komma åt miljöns aktuella tillstånd. Det är också viktigt att kunna spåra händelser som har inträffat tidigare och som kan göra det lättare att förstå vad som händer i miljön vid en viss tidpunkt. 

> [!NOTE]
> Datakvarhållningen baseras på OMS-prisplanen. Mer information finns på prissidan för [Microsoft Operations Management Suite](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx).
> 
> 

Incidenthanterings- och datautredningsscenarier kan dra nytta av resultaten som är tillgängliga på panelen **Säkerhetsposter över tid**.

![Säkerhetsposter över tid](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

När du klickar på den här panelen öppnas bladet **Sök** och visar ett frågeresultat för **Säkerhetshändelser** (Type=SecurityEvents) med data baserat på de senaste sju dagarna, som du ser här:

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Säkerhetsposter över tid](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

Sökresultatet är uppdelat på två paneler: den vänstra panelen innehåller en detaljerad analys av antalet säkerhetshändelser som hittades, de datorer som händelserna hittades på, antalet konton som identifierades på datorerna samt typen av aktivitet. Den högra panelen innehåller det sammanlagda resultatet och en kronologisk vy över säkerhetshändelserna med datorns namn och händelseaktivitet. Du kan också klicka på **Visa mer** om du vill visa mer information om händelsen, till exempel händelsedata, händelse-ID och händelsekälla.

> [!NOTE]
> Mer information om OMS-sökfrågor finns i [sökreferensen för OMS](https://technet.microsoft.com/library/mt450427.aspx).
> 
> 

### <a name="antimalware-assessment"></a>Utvärdering av program mot skadlig kod
Med det här alternativet kan du snabbt identifiera datorer med otillräckligt skydd och datorer som har komprometterats av skadlig kod. Statusen för en utvärdering av skadlig kod och identifierade hot på de övervakade servrarna läses och informationen skickas till OMS-tjänsten i molnet för bearbetning. Servrar med identifierade hot och servrar med otillräckligt skydd visas på instrumentpanelen för utredning av skadlig kod, som du kommer till när du klickar på panelen **Utvärdering av program mot skadlig kod**. 

![utvärdering av skadlig kod](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Precis som med alla andra levande paneler på OMS-instrumentpanelen öppnas bladet **Sök** med frågeresultatet när du klickar på panelen. Om du klickar på **Rapporterar inte** under **Skyddsstatus** för det här alternativet visas frågeresultatet som innehåller den här enstaka posten med datorns namn och dess rankning, som du ser här:

![sökresultat](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [!NOTE]
> *Rankning* är en grad som tilldelas för att återspegla skyddets status (på, av, uppdaterat osv.) och hot som hittats. Med det här numret blir det lättare att göra aggregeringar.
> 
> 

Om du klickar på datorns namn visas den kronologiska vyn över skyddsstatusen för datorn. Detta är väldigt användbart för scenarier då du behöver veta om programmet mot skadlig kod har tagits bort sedan det installerades.   

### <a name="update-assessment"></a>Kontroll av uppdateringar
Med det här alternativet kan du snabbt bedöma exponeringen för potentiella säkerhetsproblem, och se om och i så fall hur viktiga dessa uppdateringar är för din miljö. Säkerhets- och granskningslösningen i OMS visar bara visualiseringen av dessa uppdateringar. Den verkliga informationen kommer från [lösningar för uppdateringshantering](oms-solution-update-management.md), som är en annan modul i OMS. Här ett exempel på uppdateringarna:

![systemuppdateringar](./media/oms-security-getting-started/oms-getting-started-fig6-new.png)

> [!NOTE]
> Mer information om lösningar för uppdateringshantering finns i [Uppdateringshanteringslösning i OMS](oms-solution-update-management.md).
> 
> 

### <a name="identity-and-access"></a>Identitet och åtkomst
Identiteten bör vara kontrollplanet för ditt företag, och din högsta prioritet bör vara att skydda din identitet. Tidigare fanns det gränser runt organisationerna och dessa utgjorde ett av de viktigaste skydden. I dag, då alltmer data och fler appar migreras till molnet, är identiteten dess nya motsvarighet. 

> [!NOTE]
> För närvarande baseras informationen endast på inloggningsdata för säkerhetshändelser (händelse-ID 4624). Längre fram kommer även Office365-inloggningar och Azure AD-data att ingå.
> 
> 

Genom att övervaka identitetsaktiviteter kan du vidta proaktiva åtgärder innan en incident inträffar eller reaktiva åtgärder för att stoppa ett angreppsförsök. Instrumentpanelen för **Identitet och åtkomst** innehåller en översikt över din identitetsstatus, inklusive antalet misslyckade inloggningsförsök, det användarkonto som användes vid dessa försök, konton som har låsts, konton med ändrade eller återställda lösenord och antalet konton som är inloggade för tillfället. 

När du klickar på panelen **Identitet och åtkomst** visas följande instrumentpanel:

![identitet och åtkomst](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

Med informationen på den här instrumentpanelen kan du snabbt identifiera en potentiell misstänkt aktivitet. Anta till exempel att 338 inloggningsförsök som **administratör** har gjorts och att samtliga av dessa försök misslyckades. Detta kan bero på en råstyrkeattack mot kontot. Om du klickar på det här kontot visas mer information som kan hjälpa dig att fastställa målresursen för det potentiella angreppet:

![sökresultat](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

Den detaljerade rapporten innehåller viktig information om den här händelsen, bland annat: måldatorn, typen av inloggning (i det här fallet nätverksinloggning), aktiviteten (i det här fallet händelse 4625) samt en detaljerad tidslinje för varje försök. 

### <a name="computers"></a>Datorer
Den här panelen kan användas för att få åtkomst till alla datorer som aktivt har säkerhetshändelser. När du klickar på den här panelen visas en lista över datorer med säkerhetshändelser och antalet händelser på varje dator:

![Datorer](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

Du kan fortsätta din undersökning genom att klicka på varje dator och granska säkerhetshändelserna som flaggats.

### <a name="threat-intelligence"></a>Hotinformation

När IT-administratörer använder alternativet Hotinformation som är tillgängligt i OMS Säkerhet och granskning kan de identifiera säkerhetshot mot miljön, till exempel identifiera om en specifik dator är en del av ett botnät. Datorer kan bli noder i ett botnät när angripare installerar otillåten skadlig kod som i hemlighet ansluter datorn till kommando och kontroll. Hotinformation kan också identifiera potentiella hot från kommunikationskanaler under marken, som darknet. Läs mer om hotinformation genom att läsa artikeln [Övervaka och svara på säkerhetsaviseringar i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-responding-alerts.md).

I vissa situationer kan finnas det en potentiellt skadlig IP-adress som öppnas från en övervakad dator:

![hotinformationkarta](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.png)

Den här aviseringen och andra inom samma kategori, skapas via OMS Security genom att använda [Microsoft Threat Intelligence](https://youtu.be/O4WtxgUrDc8). Hotinformationsdata samlas in av Microsoft samt köpts från ledande säkerhetsexperter. Dessa data uppdateras ofta och anpassade till snabbföränderliga hot. På grund av sin natur, bör det kombineras med andra säkerhetinformationskällor när du [undersöker](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) en säkerhetsavisering. 

### <a name="baseline-assessment"></a>Utvärdering av baslinje

Microsoft definierar, i samarbete med branschspecifika och offentliga organisationer i hela världen, en Windows-konfiguration för mycket säkra serverinstallationer. Den här konfigurationen består av en uppsättning registernycklar, granskningsprincipinställningar och säkerhetsprincipinställningar tillsammans med Microsofts rekommenderade värden för dessa inställningar. Den här uppsättningen regler kallas för en säkerhetsbaslinje. Läs [bedömning av baslinjen i Operations Management Suite säkerhet och granska lösningen](oms-security-baseline.md) för mer information om det här alternativet.

### <a name="azure-security-center"></a>Azure Security Center
Den här panelen är i grunden en genväg till instrumentpanelen för Azure Security Center. Mer information om den här lösningen finns i [Komma igång med Azure Security Center](../security-center/security-center-get-started.md).

## <a name="notable-issues"></a>Anmärkningsvärda problem
Det huvudsakliga syftet med den här gruppen alternativ är att ge en snabb överblick över problemen i din miljö genom att dela in dem i kategorierna Kritiskt, Varning och Information. Panelen Aktiva problemtyper är en visualisering av dessa problem, men ger dig inte möjlighet att utforskar mer information om dem. För att göra det måste du använda den nedre delen av den här panelen som visar namnet på problemet (NAMN), hur många objekt som haft problemet (ANTAL) och hur kritiskt det är (ALLVARLIGHETSGRAD).

![Anmärkningsvärda problem](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Du kan se att de här problemen redan finns med i olika områden i gruppen **Säkerhetsdomäner**, vilket är i linje med syftet med den här vyn: att visualisera de viktigaste problemen i miljön från en enda plats.

## <a name="detections-preview"></a>Identifieringar (förhandsversion)
Det huvudsakliga syftet med det här alternativet är att IT-personalen snabbt ska kunna identifiera potentiella hot i miljön utifrån hotets allvarlighetsgrad.

![Hotinformation](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Det här alternativet kan också användas vid en [incidenthanteringsundersökning](https://blogs.msdn.microsoft.com/azuresecurity/2016/11/30/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) för att utvärdera och få mer information om attacken.

> [!NOTE]
> Mer information om hur du använder OMS för incidenthantering finns i den här videon: [Utnyttja Azure Security Center och Microsoft Operations Management Suite för incidenthantering](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703).
> 
> 

## <a name="threat-intelligence"></a>Hotinformation
Det nya hotinformationsavsnittet i säkerhets- och granskningslösningen visualiserar möjliga attackmönster på flera sätt: det totala antalet servrar med utgående skadlig IP-trafik, typen av skadligt hot samt en karta som visar var dessa IP-adresser kommer från. Du kan interagera med kartan och klicka på IP-adresser om du vill ha mer information.

Gula kartnålar på kartan anger inkommande trafik från skadliga IP-adresser. Det är inte ovanligt att servrar som exponeras för Internet får skadlig inkommande trafik, men vi rekommenderar att du granskar dessa försök för att kontrollera att inget av dem lyckades. Dessa indikatorer baseras på IIS-loggar, WireData och loggar för Windows-brandväggen.  

![Hotinformation](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Vanliga säkerhetsfrågor
Du kan använda listan över vanliga säkerhetsfrågor för att snabbt komma åt resursens information och anpassa den baserat på kraven i din miljö. Dessa vanliga frågor är:

* Alla säkerhetsaktiviteter
* Säkerhetsaktiviteter på datorn ”computer01.contoso.com” (ersätt namnet med ditt datornamn)
* Säkerhetsaktiviteter på datorn ”computer01.contoso.com” för kontot ”Administratör” (ersätt namnen med dina dator- och kontonamn)
* Inloggningsaktivitet per dator
* Konton som avslutat Microsoft-program mot skadlig kod på en dator
* Datorer där processer för Microsoft-program mot skadlig kod har avslutats
* Datorer där ”hash.exe” har körts (ersätt namnet med processnamnet)
* Alla processnamn som körts
* Inloggningsaktivitet per konto
* Konton som har loggat in på datorn ”computer01.contoso.com” via en fjärranslutning (ersätt namnet med ditt datornamn)

## <a name="see-also"></a>Se även
I det här dokumentet introducerades säkerhets- och granskningslösningen i OMS. Mer information om säkerheten i OMS finns i följande artiklar:

* [Översikt över Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Övervaka och svara på säkerhetsaviseringar i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-responding-alerts.md)
* [Övervaka resurser i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-monitoring-resources.md)


