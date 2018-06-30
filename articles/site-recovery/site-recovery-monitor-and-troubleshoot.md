---
title: Övervaka och felsöka Azure Site Recovery | Microsoft Docs
description: Övervaka och felsöka problem med Azure Site Recovery replikering och åtgärder med hjälp av portalen
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: 9a979cc940e2133e3dff3bb7dd4c5f2ec53fea7a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109509"
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Övervakning och felsökning av Azure Site Recovery

I den här artikeln får du lära dig hur du använder Azure Site Recovery i inbyggda övervakningsfunktioner för övervakning och felsökning. Lär dig att:
> [!div class="checklist"]
> - Använd Azure Site Recovery-instrumentpanelen (valvet översiktssidan)
> - Övervaka och felsöka replikeringsproblem
> - Övervaka Azure Site Recovery jobb /-åtgärder
> - Prenumerera på e-postaviseringar

## <a name="using-the-azure-site-recovery-dashboard"></a>Med hjälp av Azure Site Recovery-instrumentpanelen

Azure Site Recovery-instrumentpanelen på översiktssidan valvet konsoliderar alla övervakningsinformation för valvet på en enda plats. Börja med valvet instrumentpanelen och gå på djupet att få mer information genom att navigera genom delar av instrumentpanelen. De huvudsakliga delarna i Azure Site Recovery-instrumentpanelen är följande:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Växla mellan Azure Backup och Azure Site Recovery instrumentpaneler

Växeln växla överst på översiktssidan kan du växla mellan instrumentpanelssidor för Site Recovery och säkerhetskopiering. Det här urvalet när gjort sparas och nästa gång du öppnar översiktssidan för valvet som standard. Välj alternativet Site Recovery för att se Site Recovery-instrumentpanelen. 

De olika delarna i Azure Site Recovery instrumentpanelssida uppdateras var 10: e minut så att instrumentpanelen visar den senaste tillgängliga informationen.

![Övervakningsfunktioner på översiktssidan för Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Replikerade objekt

Avsnittet replikerade objekt i instrumentpanelen visar en översikt över hälsotillståndet för replikering av skyddade servrar i valvet. 

<table>
<tr>
    <td>Felfri</td>
    <td>Replikering pågår normalt för servrarna och inga fel eller varning problem har upptäckts.</td>
</tr>
<tr>
    <td>Varning </td>
    <td>En eller flera varning problem som kan påverka replikering eller tyda på att replikering inte är klar normalt har upptäckts för dessa servrar.</td>
</tr>
<tr>
    <td>Kritiska</td>
    <td>En eller flera fel inträffa kritisk replikering har identifierats för dessa servrar. Problemen fel är vanligtvis indikatorer att replikeringen är antingen fastnat eller inte fortlöper snabb data ändra frekvensen för dessa servrar.</td>
</tr>
<tr>
    <td>Inte tillämpligt</td>
    <td>Servrar som inte är för närvarande förväntas replikeras till exempel servrar som inte över.</td>
</tr>
</table>

Om du vill se en lista över skyddade servrar filtreras efter replikeringshälsa klickar du på replikering hälsa beskrivning bredvid ringen. Visa alla länka nära avsnittsrubriken är en genväg till sidan replikerade objekt för valvet. Använd vyn alla länkar om du vill se en lista över alla servrar i valvet.

### <a name="3-failover-test-success"></a>3. Redundans testet lyckades

Avsnittet redundans testet lyckas i instrumentpanelen visar en uppdelning av virtuella datorer i valvet baserat på status för testa redundans. 

<table>
<tr>
    <td>Test som rekommenderas</td>
    <td>Virtuella datorer som inte har lyckats testa redundans sedan de uppnått ett skyddat läge.</td>
</tr>
<tr>
    <td>Genomfördes</td>
    <td>Virtuella datorer som har fått en eller flera lyckade redundanstestning.</td>
</tr>
<tr>
    <td>Inte tillämpligt</td>
    <td>Virtuella datorer som inte är för närvarande är berättigad till ett redundanstest. Exempel: Det gick inte över servrar, servrar för vilka den inledande replikeringen är i pågår, servrar som en växling pågår, servrar för vilka ett redundanstest pågår redan.</td>
</tr>
</table>

Klicka på Testa redundans status bredvid ringen, så se en lista över skyddade servrar baserat på deras testa redundans status.
 
> [!IMPORTANT]
> Som bästa praxis rekommenderas det att du utför ett redundanstest på dina skyddade servrar minst en gång var sjätte månad. Utför ett redundanstest är ett icke störande sätt att testa redundans för dina servrar och program till en isolerad miljö och hjälper dig att utvärdera dina business continuity beredskap.

 En redundanstestet på en server eller en återställningsplan anses lyckade förrän både redundanstestet och rensa redundanstestet har slutförts.

### <a name="4-configuration-issues"></a>4. Konfigurationsproblem

Konfigurationsavsnittet problem visar en lista över problem som kan påverka möjligheten att har redundans virtuella datorer. De problem som anges i det här avsnittet är:
 - **Konfigurationer som saknas:** skyddade servrar som saknar nödvändiga konfigurationer, till exempel ett nätverk för återställning eller en resursgrupp för återställning.
 - **Resurser saknas:** konfigurerade mål/återställning resurser hittas inte eller är inte tillgängliga i prenumerationen. Resursen har tagits bort eller migrerats till en annan prenumeration eller resursgrupp. Följande konfigurationer för mål/återställning övervakas för tillgänglighet: målresursgruppen, virtuella nätverk och undernät, loggen/mål-lagringskontot mål tillgänglighet måluppsättningen, mål-IP-adress.
 - **Prenumerationens kvoter:** tillgänglig prenumeration kvoten resursbalansen jämförs saldo som krävs för att kunna redundansväxla alla virtuella datorer i valvet. Om det finns inte tillräckligt saldot, rapporteras kvoten för otillräckligt saldo. Kvoter för följande resurser i Azure övervakas: kärnor för antal virtuella datorer, antal virtuella datorer i familjen kärnor, antal nätverkskort (NIC)-gränssnittet.
 - **Programuppdateringar:** tillgängligheten för nya programuppdateringar upphör att gälla programvaruversioner.

Konfigurationsproblem (andra än tillgänglighet av programuppdateringar) identifieras av en återkommande verifieraren åtgärd som körs var 12: e timme som standard. Du kan tvinga verifieraren åtgärden att köra omedelbart genom att klicka på ikonen bredvid den *konfigurationsproblem* avsnittsrubriken.

Klicka på länkarna för att få mer information om listan problem och virtuella datorer som påverkas av dem. Om problem som påverkar specifika virtuella datorer, kan du få mer information genom att klicka på den **behöver åtgärdas** länken under kolumnen mål konfigurationer för den virtuella datorn. Informationen omfattar rekommendationer om hur du kan åtgärda identifierade problem.

### <a name="5-error-summary"></a>5. Sammanfattning av fel vid

Översikt över felavsnittet visas för närvarande är aktiv replikering fel problem som kan påverka replikering av servrar i valvet, tillsammans med antalet berörda enheter på grund av varje fel.

Replikering fel inträffa för servrar i ett kritiskt eller varningshälsotillstånd replikering hälsotillstånd kan ses i sammanfattningen av fel. 

- Fel som påverkar lokalt infrastrukturkomponenter, till exempel inte emot något pulsslag från Azure Site Recovery-providern körs på den lokala konfigurationsservern, VMM-servern eller Hyper-V-värden visas i början av fel sammanfattning avsnittet
- Replikering fel inträffa påverka skyddade servrar visas bredvid. Sammanfattningstabell felposter sorteras i fallande ordning efter allvarlighetsgrad fel och sedan i fallande ordning efter antalet servrar som påverkas.
 

> [!NOTE]
> 
>  Flera replikering fel problem kan observeras på en enskild server. Om det finns flera fel inträffa på en enskild server räknas varje fel symtom servern i listan över de berörda servrarna. När det underliggande problemet, vilket resulterar i ett fel symtom är fast, förbättra replikeringsparametrarna och felet rensas från den virtuella datorn.
>
> > [!TIP]
> Antal berörda servrarna är ett bra sätt att förstå om en enda underliggande problem kan påverka flera servrar. Till exempel kan ett nätverk tekniskt problem påverka alla servrar som replikerar från en lokal plats till Azure. Den här vyn ger snabbt att åtgärda en underliggande problem korrigerar replikering för flera servrar.
>

### <a name="6-infrastructure-view"></a>6. Visa infrastruktur

Vyn infrastruktur innehåller en scenariot klokt visuell representation av de infrastrukturella komponenterna som ingår i replikeringen. Den visar även visuellt hälsotillståndet för anslutningen mellan de olika servrarna och mellan servrarna och Azure-tjänster ingår i replikeringen. 

En grön linje anger att anslutningen är felfri, medan en röd linje med putsade felikonen anger förekomsten av en eller flera fel inträffa som påverkar anslutningen mellan komponenter som ingår. Hovra muspekaren över felikonen på raden visar felet och antalet enheter som påverkas. 

Klicka på felikonen visas en filtrerad lista över påverkade entiteter för felmeddelandena.

![Site Recovery infrastruktur vyn (valvet)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Se till att lokalt infrastrukturkomponenter (konfigurationsservern och ytterligare processer servrar replikera virtuella VMware-datorer, Hyper-V-värdar, VMM-servrar) kör den senaste versionen av Azure Site Recovery-programvara. Om du vill kunna använda alla funktioner i vyn infrastruktur du behöver köra [Update rollup 22](https://support.microsoft.com/help/4072852) eller senare för Azure Site Recovery

Om du vill använda vyn infrastruktur, Välj lämplig replikering scenario (virtuella Azure-datorer, VMware-datorer/fysiska server eller Hyper-V) beroende på din källmiljö. Vyn infrastruktur som visas på översiktssidan för valvet är en samlad vy för valvet. Du kan gå vidare nedåt till de enskilda komponenterna genom att klicka på rutorna.

Vyn infrastruktur omfattar kontexten för en enskild dator för replikering är tillgänglig på översiktssidan replikerade objekt. Gå till översiktssidan för en replikeringsservrarna går du till replikerade objekt från valvet-menyn och Välj server och visa information för.

### <a name="infrastructure-view---faq"></a>Visa infrastruktur - vanliga frågor och svar

**F.** Varför ser jag vyn infrastruktur för den virtuella datorn inte? </br>
**S.** Infrastruktur visa funktionen är endast tillgänglig för virtuella datorer som replikerar till Azure. Funktionen är för närvarande inte tillgängligt för virtuella datorer som replikerar mellan lokala platser.

**F.** Varför visas antalet virtuella datorer i valvet infrastruktur vyn skiljer sig från det totala antalet i ringen för replikerade objekt?</br>
**S.** Vyn valvet infrastruktur omfattas av replikeringarna. Endast virtuella datorer som ingår i det markerade replikering scenariot tas med i antalet virtuella datorer visas i vyn infrastruktur. För det valda scenariot ingår också endast virtuella datorer som är konfigurerade för att replikera till Azure i antal virtuella datorer visas i vyn infrastruktur (för exempel: misslyckades för virtuella datorer, virtuella datorer som replikeras tillbaka på en lokal plats ingår inte i vyn infrastruktur.)

**F.** Varför är antalet replikerade objekt visas i essentials lådan på översiktssidan skiljer sig från det totala antalet replikerade objekt visas i ringdiagram på instrumentpanelen?</br>
**S.** Endast de virtuella datorerna för vilka den inledande replikeringen har slutförts tas med i antalet som visas i lådan essentials. Den totala replikerade objekt ringen innehåller alla virtuella datorer i valvet inklusive servrar för vilka den inledande replikeringen pågår.

**F.** Vilka replikeringarna finns vyn infrastruktur för? </br>
**S.**
>[!div class="mx-tdBreakAll"]
>|Scenario för replikering  | Tillstånd för virtuell dator  | Infrastruktur visa tillgängliga  |
>|---------|---------|---------|
>|Virtuella datorer som replikeras mellan två lokala platser     | -        | Nej      |
>|Alla     | Det gick inte över         |  Nej       |
>|Virtuella datorer som replikeras mellan två Azure-regioner     | Inledande replikering pågår eller skyddade         | Ja         |
>|Replikera till Azure virtuella VMware-datorer     | Inledande replikering pågår eller skyddade        | Ja        |
>|Replikera till Azure virtuella VMware-datorer     | Det gick inte över virtuella datorer som replikeras tillbaka till en lokal VMware-plats         | Nej        |
>|Hyper-V virtuella datorer som replikeras till Azure     | Inledande replikering pågår eller skyddade        | Ja       |
>|Hyper-V virtuella datorer som replikeras till Azure     | Det gick inte via / återställning efter fel pågår        |  Nej       |


### <a name="7-recovery-plans"></a>7. Återställningsplaner

Avsnittet planer visar antalet återställningsplaner i valvet. Klicka på siffran om du vill se en lista över återställningsplaner, skapa återställningsplaner för nya eller redigera befintliga. 

### <a name="8-jobs"></a>8. Jobb

Azure Site Recovery-jobb spåra statusen för Azure Site Recovery-åtgärder. De flesta åtgärder i Azure Site Recovery körs asynkront, med ett jobb för spårning som används för att spåra förloppet för åtgärden.  Information om hur du övervakar statusen för en åtgärd, finns det [övervakaren Azure Site Recovery-jobb/åtgärder](#monitor-azure-site-recovery-jobsoperations) avsnitt.

Det här avsnittet för jobb på instrumentpanelen innehåller följande information:

<table>
<tr>
    <td>Misslyckad</td>
    <td>Misslyckade jobb för Azure Site Recovery under de senaste 24 timmarna</td>
</tr>
<tr>
    <td>Pågår</td>
    <td>Azure Site Recovery-jobb som för närvarande pågår</td>
</tr>
<tr>
    <td>Väntar på indata</td>
    <td>Azure Site Recovery-jobb som är pausad för tillfället väntar på indata från användaren.</td>
</tr>
</table>

Visa alla länken bredvid avsnittsrubriken är en genväg för att gå till sidan jobb.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Övervaka och felsöka replikeringsproblem

Utöver informationen i valvet instrumentpanelens sida, kan du få ytterligare information och information om felsökning i sidan virtuella datorer och informationssidan för virtuell dator. Du kan visa listan över skyddade virtuella datorer i valvet genom att välja den **replikerade objekt** alternativ på menyn valvet. Alternativt kan du till en filtrerad lista över skyddade objekt genom att klicka på någon av begränsade genvägar som är tillgänglig på sidan valvet instrumentpanelen.

![Site Recovery replikerade objekt listvy](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

Filteralternativet på sidan replikerade objekt kan du använda olika filter, till exempel replikeringshälsa och replikeringsprincipen. 

Kolumnen selector-alternativet kan du ange ytterligare kolumner som ska visas som Återställningspunktsmål, mål konfigurationsproblem och replikeringsfel. Du kan initiera åtgärder på en virtuell dator eller visa fel som påverkar den virtuella datorn genom att högerklicka på en viss rad i listan med datorer.

Om du vill öka detaljnivån ytterligare, väljer du en virtuell dator genom att klicka på den. Informationssidan om virtuell dator öppnas. På översiktssidan under information om den virtuella datorn innehåller en instrumentpanel där du hittar ytterligare information som rör datorn. 

På översiktssidan för replikering datorn hittar du:
- RPO (mål för återställningspunkt): aktuella Återställningspunktmålet för den virtuella datorn och den tid då Återställningspunktmålet senast beräknad.
- Senaste tillgängliga återställningspunkter för datorn
- Konfigurationsproblem om eventuella som kan påverka beredskap för växling vid fel för datorn. Klicka på länken om du vill ha mer information.
- Information om felet: lista av replikering fel inträffa som för närvarande observerats på datorn tillsammans med möjliga orsaker och rekommenderade reparationer
- Händelser: En kronologisk lista över senaste händelser som påverkar datorn. Felinformation visar för närvarande synliga fel inträffa på datorn, händelser är en tidigare post för olika händelser som kan ha påverkat den datorn inklusive fel inträffa som kanske tidigare har såg för datorn.
- Visa infrastruktur för datorer som replikeras till Azure

![Site Recovery replikerade objekt information/översikt](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

Åtgärd-menyn överst på sidan innehåller alternativ för att utföra olika åtgärder som testa redundans på den virtuella datorn. Knappen fel information på Åtgärd-menyn kan du visa alla aktiva fel replikeringsfel, konfigurationsproblem och bästa praxis baserat konfigurationsvarningar för den virtuella datorn.

> [!TIP]
> Hur skiljer sig mål för Återställningspunktmål eller återställning från den senaste tillgängliga återställningspunkten?
> 
>Azure Site Recovery använder en asynkron process i flera steg för att replikera virtuella datorer till Azure. I det näst sista steget i replikering kopieras ändringar på den virtuella datorn tillsammans med metadata i ett lagringskonto för logg-cache. När ändringarna tillsammans med taggen för att identifiera en återställningsbara har skrivits till lagringskonto i målregionen, har informationen som behövs för att generera en återställningsbara punkt för den virtuella datorn i Azure Site Recovery. Återställningspunktmålet har nu uppfyllts så att ändringarna har överförts till lagringskontot hittills. Återställningspunktmålet för den virtuella datorn nu uttryckt i tidsenheter, med andra ord är lika med tiden förflutit från tidsstämpel som motsvarar den återställningsbara punkten.
>
>Azure Site Recovery-tjänsten som körs i bakgrunden, hämtar överförda data från storage-konto och använder dem till replik-diskar som skapats för den virtuella datorn. Den genererar en återställningspunkt och gör den här punkten tillgängliga för återställning under växling vid fel. Den senaste tillgängliga återställningspunkten anger tidsstämpel som motsvarar den senaste återställningspunkten som redan har bearbetats och tillämpas på repliken diskar.
>> [!WARNING]
> En skeva klockan eller felaktig systemklockan på källdatorn replikering eller infrastrukturservrar lokalt kommer skeva beräknade värde för Återställningspunktsmål. För att säkerställa att korrekt rapportering av Återställningspunktmål Kontrollera värden att systemklockan på servrarna som ingår i replikeringen är korrekt. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Övervaka Azure Site Recovery jobb /-åtgärder

Azure Site Recovery utför de åtgärder som du anger asynkront. Exempel på åtgärder du kan utföra är Aktivera replikering, skapa återställningsplan, testa redundans, uppdatera replikeringsinställningar osv. Varje sådan åtgärd har ett motsvarande jobb som har skapats för att spåra och granska igen. Jobbobjektet har all nödvändig information som krävs att spåra status och förlopp för åtgärden. Du kan spåra statusen för olika Site Recovery-åtgärder för valvet från sidan jobb. 

Lista över Site Recovery-jobb för valvet gå finns i **övervakning och rapporter** avsnitt i valvet-menyn och välj jobb > Site Recovery-jobb. Välj ett jobb i listan över jobb på sidan genom att klicka på den för att få mer information i det angivna jobbet. Om ett jobb inte har slutförts eller har fel kan se du mer information om reparationen för fel och möjliga genom att klicka på knappen fel information längst upp i jobbet informationssidan (också tillgänglig från sidan jobb genom att högerklicka på den misslyckade jobb.) Du kan använda filteralternativet Åtgärd-menyn överst sidan jobb för att filtrera listan baserat på ett visst villkor och Använd exportera-knappen för att exportera information om de valda jobben till en excel. Du kan också använda listvyn jobb från genvägen på instrumentpanelssidan Site Recovery. 

 För åtgärder som du utför i Azure Portal kan skapade jobbet och dess aktuella status också spåras från avsnittet meddelanden (klockikonen längst upp till höger) i Azure-portalen.

## <a name="subscribe-to-email-notifications"></a>Prenumerera på e-postaviseringar

Inbyggda e-postaviseringsfunktionen kan du prenumerera på e-postmeddelanden för kritiska händelser. Om du prenumererar på, skickas e-postaviseringar för följande händelser:
- Hälsotillstånd för replikering av en replikering dator försämring till kritiskt.
- Ingen anslutning mellan infrastrukturkomponenter lokalt och Azure Site Recovery-tjänsten. Anslutning till Site Recovery-tjänsten från lokala infrastrukturkomponenter som konfigurationsservern (VMware) eller System Center Virtual Machine Manager(Hyper-V) registrerad för valvet har identifierats med hjälp av en mekanism för pulsslag.
- Redundans åtgärden fel om en sådan.

Om du vill prenumerera på e-postmeddelanden för Azure Site Recovery, går den **övervakning och rapporter** på valvet-menyn och:
1. Välj aviseringar och händelser > Site Recovery-händelser.
2. Välj ”e-postmeddelanden” på menyn ovanpå sidan händelser som har öppnats.
3. Använd guiden e-postavisering för att aktivera eller inaktivera e-postmeddelanden och Välj mottagare av meddelanden. Du kan ange att alla prenumerationsadministratörer skickas meddelanden, och/eller ange en lista över e-postadresser du skickar meddelanden till. 
