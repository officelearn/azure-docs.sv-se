---
title: "Övervakning av resurser i Operations Management Suite säkerhet och granska lösningen | Microsoft Docs"
description: "Det här dokumentet hjälper dig att använda OMS säkerhet och granska funktioner för att övervaka dina resurser och identifiera säkerhetsproblem."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 2f73266b65a4eda6c8751a2d56bc3f11bf4e6a57
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Övervaka resurser i Operations Management Suite säkerhet och granska lösning
Det här dokumentet hjälper dig att granska funktionerna för OMS säkerhet och övervaka dina resurser och identifiera säkerhetsproblem.

## <a name="what-is-oms"></a>Vad är OMS?
Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur. Mer information om OMS finns i artikeln [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Övervaka resurser
När det är möjligt, kommer du vilja förhindra att säkerhetsincidenter händer i första hand. Dock är det omöjligt att förhindra alla säkerhetsincidenter. När en säkerhetsincident sker, behöver du kontrollera att dess konsekvenser minimeras.  Det finns tre viktiga rekommendationer som kan användas för att minimera antalet och konsekvenserna av säkerhet:

* Regelbundet bedöma säkerhetsrisker i din miljö.
* Kontrollera regelbundet alla datorer och nätverksenheter så att de har alla de senaste korrigeringarna installerad.
* Kontrollera regelbundet alla loggar och mekanismer för loggning, inklusive händelseloggar för operativsystem, specifika programloggarna och intrångsidentifiering systemloggar för identifiering.

OMS säkerhets- och granska lösningen möjliggör för IT att aktivt övervaka alla resurser som hjälper dig att minimera effekten av säkerhetsincidenter. OMS säkerhet och granska har säkerhetsdomäner som kan användas för att övervaka resurser. Säkerhetsdomäner ger snabb åtkomst till en alternativ för säkerhetsövervakning följande domäner beskrivs mer detaljerat:

* kontroll av skadlig kod
* Kontroll av uppdateringar
* Identitet och åtkomst

> [!NOTE]
> en översikt över alla dessa alternativ, läsa [komma igång med Operations Management Suite säkerhet och granska lösningen](oms-security-getting-started.md).
> 
> 

### <a name="monitoring-system-protection"></a>Övervakning av Systemskydd
Varje skyddslager är viktigt för övergripande säkerhetstillståndet hos din tillgång i en skydd på djupet metod. Datorer med identifierade hot och datorer med otillräckligt skydd visas i panelen kontroll av skadlig kod under säkerhetsdomäner. Du kan identifiera en plan för att använda skyddet för de servrar som behöver den med hjälp av informationen om bedömning skadlig kod. För att komma åt det här alternativet Följ stegen nedan:

1. Klicka på panelen **Säkerhet och granskning** på huvudinstrumentpanelen för **Microsoft Operations Management Suite**.
   
    ![Säkerhet och granskning](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. I den **säkerhet och granska** instrumentpanelen, klickar du på **program mot skadlig kod Assessment** under **säkerhetsdomäner**. Den **program mot skadlig kod Assessment** instrumentpanelen visas enligt nedan:

![kontroll av skadlig kod](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Du kan använda den **kontroll av skadlig kod** instrumentpanelen för att identifiera följande säkerhetsproblem:

* **Aktiva hot**: datorer som har komprometterats och ha active hot i systemet.
* **Åtgärdad hot**: datorer som har drabbats men hoten har reparerats.
* **Inaktuell signatur**: datorer som har skadlig kod skydd är aktiverat men signaturen är inaktuell.
* **Inget realtidsskydd**: datorer som inte har installerat program mot skadlig kod.

### <a name="monitoring-updates"></a>övervakning av uppdateringar
Tillämpa de senaste säkerhetsuppdateringarna är en bra säkerhetsrutin och bör tas upp i din strategi för uppdateringen. Microsoft Monitoring Agent-tjänsten (HealthService.exe) läser information om uppdateringar från övervakade datorer och skickar sedan den uppdaterade informationen till OMS-tjänst i molnet för bearbetning. Microsoft Monitoring Agent-tjänsten har konfigurerats som en automatisk tjänst och den alltid ska köras på måldatorn.

![övervakning av uppdateringar](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Logik tillämpas på data för uppdateringen och Molntjänsten innehåller data. Om uppdateringar som saknas påträffas, visas de på det **uppdateringar** instrumentpanelen. Du kan använda den **uppdateringar** instrumentpanelen för att arbeta med uppdateringar som saknas och utveckla en plan för att använda dem på de servrar som behövs. Följ stegen nedan för att komma åt den **uppdateringar** instrumentpanelen:

1. Klicka på panelen **Säkerhet och granskning** på huvudinstrumentpanelen för **Microsoft Operations Management Suite**.
2. I den **säkerhet och granska** instrumentpanelen, klickar du på **uppdatering Assessment** under **säkerhetsdomäner**. Uppdatera instrumentpanelen visas enligt nedan:

![Utvärdering av uppdateringar](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Du kan utföra en utvärdering av uppdateringar för att förstå det aktuella tillståndet för dina datorer och åtgärda de mest kritiska hot i den här instrumentpanelen. Med hjälp av den **kritiska uppdateringar eller säkerhetsuppdateringar** panelen måste IT-administratörer kommer att kunna komma åt detaljerad information om vilka uppdateringar som saknas enligt nedan:

![sökresultat](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Den här rapporten innehåller viktig information som kan användas för att identifiera vilken typ av hot som det här systemet är sårbar för som ingår i Microsoft KB-artiklar som associeras med säkerhetsuppdateringen och MS-Bulletin som innehåller mer information om problemet.

### <a name="monitoring-identity-and-access"></a>Övervakning av identiteter och åtkomst
Med användare som arbetar var som helst, med olika enheter och komma åt mängder med molnet och lokala appar, är det viktigt att skydda sina autentiseringsuppgifter. Attacker för stöld av autentiseringsuppgifter är sådana där en angripare först får åtkomst till en vanlig användare autentiseringsuppgifter för åtkomst till ett system i nätverket. Många gånger angreppet inledande är bara ett sätt att få åtkomst till nätverket, slutmålet är att identifiera privilegium konton. 

Angripare kvar i nätverket, med gratis tooling för att hämta autentiseringsuppgifter från sessioner av andra konton. Beroende på systemkonfiguration, kan dessa autentiseringsuppgifter extraheras i form av hashvärden, biljetter och även textlösenord.  

> [!NOTE]
> datorer som exponeras direkt till Internet visas många misslyckade inloggningsförsök som försöker logga in med alla slags välkända användarnamn (t.ex. administratör). I de flesta fall är det OK om välkända användarnamn inte används och lösenordet är starkt nog.
> 
> 

Det är möjligt att identifiera dessa användare innan de angripa ett konto med privilegier. Du kan utnyttja **OMS säkerhets- och granska lösningen** övervakaren identitet och åtkomst. Följ stegen nedan för att komma åt den **identitets- och** instrumentpanelen:

1. I den **Microsoft Operations Management Suite** huvudinstrumentpanelen klickar du på säkerhet och granska panelen.
2. I den **säkerhet och granska** instrumentpanelen, klickar du på **identitets- och** under **säkerhetsdomäner**. Den **identitets- och** instrumentpanelen visas enligt nedan:

![identitet och åtkomst](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Som en del av din strategi för vanlig övervakning, måste du inkludera identitet övervakning. IT-administratören bör se ut om det finns ett specifikt användarnamn som har många försök. Detta kan indikera antingen angripare som införskaffade verkliga användarnamnet och försök att brute force eller ett automatiskt verktyg som använder hårdkodade lösenord som upphört att gälla.

Den här instrumentpanelen göra det möjligt att snabbt kunna identifiera potentiella hot som rör identitet och åtkomst till företagets resurser. Det är särskilt viktigt att också identifiera eventuella trender, till exempel i panelen inloggningar över tid du kan se över tidsperiod hur många gånger ett misslyckat inloggningsförsök har utförts. I det här fallet datorn **filserver** emot 35 inloggningsförsök. Du kan utforska mer information om den här datorn genom att klicka på den. 

![Mer information](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

Rapporten genereras för den här datorn ger värdefull information om det här mönstret. Upptäckt som den **konto** kolumnen innehåller det användarkonto som användes att komma åt systemet, den **TIMEGENERATED** kolumn får du tidsintervallet i som försöket gjordes och **LOGONTYPENAME** kolumn får du den plats där det här försöket har utförts. Om dessa försök utfördes lokalt i systemet av ett program i **processen** kolumn skulle visar processens namn. Processens namn finns redan i scenarier där inloggningsförsöket kommer från ett program, och nu kan du utföra ytterligare undersökning i målsystemet.

## <a name="see-also"></a>Se även
I det här dokumentet du har lärt dig hur du använder OMS säkerhet och granska lösningen för att övervaka dina resurser. Mer information om säkerheten i OMS finns i följande artiklar:

* [Översikt över Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Komma igång med Operations Management Suite säkerhet och granska lösningen](oms-security-getting-started.md)
* [Övervaka och svara på säkerhetsaviseringar i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-responding-alerts.md)

