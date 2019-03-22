---
title: Övervaka Azure Site Recovery | Microsoft Docs
description: Övervaka och felsöka problem med Azure Site Recovery-replikering och åtgärder med hjälp av portalen
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: rayne-wiselman
ms.openlocfilehash: 605421196c2ec7f62826e2f6c823d50262af8144
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316651"
---
# <a name="monitor-site-recovery"></a>Övervaka Site Recovery

I den här artikeln får du lära dig hur du använder Azure Site Recovery i inbyggda övervakningsfunktioner för övervakning och felsökning. 

## <a name="use-the-dashboard"></a>Använd instrumentpanelen

1. I valvet, klickar du på **översikt** att öppna instrumentpanelen Site Recovery. Det finns instrumentpanelssidor för säkerhetskopiering och Site Recovery du kan växla mellan dem.

    ![Site Recovery-instrumentpanel](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  Instrumentpanelen konsoliderar alla övervakningsinformation för valvet på en enda plats. Från instrumentpanelen kan du granska nedåt i olika områden. 

    ![Site Recovery-instrumentpanel](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. På **replikerade objekt**, klickar du på **visa alla** att alla servrar i valvet.
4. Öka detaljnivån genom att klicka på statusinformation i varje avsnitt. I **infrastruktur-vy**, du kan sortera informationen av övervakningen på vilken typ av datorer som du replikerar.

## <a name="monitor-replicated-items"></a>Övervaka replikerade objekt

Avsnittet replikerade objekt som visar hälsotillståndet för alla datorer som har replikering är aktiverat i valvet.

**tillstånd** | **Detaljer**
--- | ---
Felfri | Replikeringen körs normalt. Inga fel eller varning symptom identifieras.
Varning | En eller flera varningar problem som kan påverka replikering identifieras.
Kritisk | En eller flera kritisk replikering Fel Symptom har identifierats.<br/><br/> Problemen fel är vanligtvis indikatorer att replikeringen har fastnat eller inte så snabbt som de dataändringshastighet.
Inte tillämpligt | Servrar som inte är för närvarande förväntas vara replikeras. Detta kan innehålla datorer som har redundansväxlats.

## <a name="monitor-test-failovers"></a>Övervaka redundanstestning

Du kan visa status för test-redundans för virtuella datorer i valvet.

- Vi rekommenderar att du kör ett redundanstest på replikerade datorer minst en gång var sjätte månad. Det är ett sätt att kontrollera att redundansväxlingen fungerar som förväntat utan att avbryta din produktionsmiljö. 
- Ett redundanstest anses lyckas förrän rensningen redundans och efter en redundansväxling har slutförts.

**tillstånd** | **Detaljer**
--- | ---
Test som rekommenderas | Datorer som inte har haft ett redundanstest sedan skyddet aktiverades.
Genomfördes | Datorer med eller mer framgångsrika redundanstestningen.
Inte tillämpligt | Datorer som inte är för närvarande är berättigade till ett redundanstest. Datorer som har redundansväxlats, har till exempel inledande replikering/redundanstest/redundansväxling pågår.

## <a name="monitor-configuration-issues"></a>Problem med konfigurationen av Övervakare

Den **konfigurationsproblem** avsnittet visas en lista över problem som kan påverka din möjlighet att växlas över.

- Konfigurationsproblem (förutom för software update tillgänglighet) identifieras av en regelbunden verifieraren åtgärd som körs var 12: e timme som standard. Du kan tvinga verifieraren åtgärden att köra omedelbart genom att klicka på Uppdatera-ikonen bredvid den **konfigurationsproblem** avsnittsrubriken.
- Klicka på länkarna för att få mer information. Problem som påverkar specifika datorer klickar du på den **behöver åtgärdas** i den **rikta konfigurationer** kolumn. Informationen innehåller rekommenderade åtgärder.

**tillstånd** | **Detaljer**
--- | ---
Konfigurationer som saknas | En inställning som behövs saknas, till exempel återställningsnätverk eller en resursgrupp.
Resurser som saknas | En specifik resurs hittas inte eller är inte tillgängliga i prenumerationen. Resursen har tagits bort eller migrerats. Övervakade resurser med målresursgrupp, målets virtuella nätverk/undernät, logg-/ mållagringskontot, måltillgänglighetsuppsättning, mål-IP.
Prenumerationskvot |  Kvoten för tillgänglig prenumeration resursbalansen jämförs balans som behövs för att växla över alla datorer i valvet.<br/><br/> Om det inte finns tillräckligt med resurser, rapporteras en otillräcklig kvot balans.<br/><br/> Kvoter övervakning av antal Virtuella kärnor, antal family core virtuella datorer, antal nätverkskort (NIC)-gränssnittet.
Programuppdateringar | Tillgängligheten för nya programuppdateringar och information om utgående programvaruversioner.


## <a name="monitoring-errors"></a>Övervakning av fel 
Den **chyb** visar aktiva fel problem som kan påverka replikering av servrar i valvet och antalet datorer som påverkas.

- Fel som påverkar den lokala infrastrukturkomponenter som visas i början av avsnittet. Till exempel icke-mottagande av ett pulsslag från Azure Site Recovery-providern som körs på den lokala konfigurationsservern, VMM-servern eller Hyper-V-värd.
- Nästa, replikering Fel Symptom påverkar replikerade servrar visas.
- Posterna i registret sorteras i fallande ordning efter allvarlighetsgrad för fel och sedan efter fallande antal berörda datorer.
- Antalet berörda servrar är ett bra sätt att förstå om ett enda underliggande problem kan påverka flera datorer. Till exempel kan ett nätverk glapp påverka alla datorer som replikeras till Azure. 
- Flera replikeringsfel kan inträffa på en enskild server. I det här fallet räknas varje fel symtom servern i listan över de berörda servrarna. När problemet är löst replikeringsparametrarna förbättra och felet har raderats från datorn.

## <a name="monitor-the-infrastructure"></a>Övervaka infrastrukturen.

Den **infrastruktur-vy** visar infrastrukturkomponenter som ingår i replikeringen och hälsotillståndet för anslutningen mellan servrar och Azure-tjänster.

- En grön linje visar att anslutningen är felfri.
- En röd linje med putsade felikonen anger förekomsten av en eller flera fel inträffa som inverkan-anslutning.
-  Hovra muspekaren över felikonen för att visa felet och antal berörda entiteter. Klicka på ikonen för en filtrerad lista över berörda entiteter.

    ![Site Recovery infrastruktur-vy (valv)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Tips för övervakning av infrastrukturen

- Se till att de senaste versionerna av Site Recovery-providern och/eller agenter körs infrastrukturkomponenter lokala (konfigurationsservern, processervrar, VMM-servrar, Hyper-V-värdar, VMware-datorer).
- Om du vill använda alla funktioner i infrastrukturvyn du bör köra [samlad 22 uppdatering](https://support.microsoft.com/help/4072852) för dessa komponenter.
- Om du vill använda infrastruktur-vy, Välj lämplig replikeringsscenariot i din miljö. Du kan granska nedåt i vyn för mer information. I följande tabell visas vilka scenarier representeras.

    **Scenario** | **tillstånd**  | **Visa tillgängliga?**
    --- |--- | ---
    **Replikering mellan lokala platser** | Alla tillstånd | Nej 
    **Azure VM-replikering mellan Azure-regioner**  | Replikering aktiverad/initial replikering pågår | Ja
    **Azure VM-replikering mellan Azure-regioner** | Redundans / återställning | Nej   
    **VMware-replikering till Azure** | Replikering aktiverad/initial replikering pågår | Ja     
    **VMware-replikering till Azure** | Återställda over/misslyckades | Nej      
    **Hyper-V-replikering till Azure** | Återställda over/misslyckades | Nej

- Om du vill se infrastruktur-vy för en enskild replikerande dator i valvet-menyn klickar du på **replikerade objekt**, och välj en server.  

### <a name="common-questions"></a>Vanliga frågor


**Varför visas antalet virtuella datorer i infrastrukturvyn valv skiljer sig från det totala antalet i replikerade objekt?**

Infrastruktur-vy valvet är begränsad av replikeringsscenarier. I replikeringsscenariot för markerade kan endast datorer som ingår i antalet för vyn. Dessutom kan räknar vi bara virtuella datorer som är konfigurerade för att replikera till Azure. Redundansväxlats datorer eller datorer som replikeras tillbaka till en lokal plats räknas inte i vyn.

**Varför är antalet replikerade objekt som visas i lådan Essentials skiljer sig från det totala antalet replikerade objekt på instrumentpanelen?**

Endast datorer för vilka den inledande replikeringen har slutförts som ingår i antalet som visas i Essentials-lådan. På de replikerade objekt som innehåller det totala antalet alla datorer i valvet, inklusive de för vilka den inledande replikeringen pågår.


## <a name="monitor-recovery-plans"></a>Övervaka återställningsplaner

I den **återställningsplaner avsnittet** du kan granska antalet planer, skapa nya planer och ändra befintliga.  

## <a name="monitor-jobs"></a>Övervaka jobb

Den **jobb** visas statusen för Site Recovery-åtgärder.

- De flesta åtgärder i Azure Site Recovery utförs asynkront, med ett jobb för spårning och skapas och används för att spåra förloppet för åtgärden. 
- Objektet har all information du behöver att spåra status och förlopp för åtgärden. 

Övervaka jobb på följande sätt:

1. I instrumentpanelen > **jobb** avsnittet visas en sammanfattning över jobb som har slutförts, är pågår eller väntar på indata, under de senaste 24 timmarna. Du kan klicka på alla tillstånd för att få mer information om relevanta projekt.
2. Klicka på **visa alla** att se alla jobb under de senaste 24 timmarna.

    > [!NOTE]
    > Du kan också komma åt jobbinformation valvmenyn > **Site Recovery-jobb**. 

2. I den **Site Recovery-jobb** lista, en lista över jobb visas. På den översta menyn som du kan hämta felinformationen för specifika jobb, filtrera listan baserat på ett specifikt villkor och exportera markerade jobbinformation till Excel.
3. Du kan se ett jobb genom att klicka på den. 

## <a name="monitor-virtual-machines"></a>Övervakning av virtuella datorer

Du kan övervaka datorer i sidan virtuella datorer i instrumentpanelen för tillägg. 

1. I valvet, klickar du på **replikerade objekt** att hämta en lista över replikerade datorer.  Alternativt kan får du en filtrerad lista över de skyddade objekten genom att klicka på någon av de begränsade genvägarna på den instrumentpanelen.

    ![Site Recovery replikerade objekt listvy](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. På den **replikerade objekt** kan du visa och filtrera information. Du kan utföra åtgärder för en viss dator, inklusive som kör ett redundanstest eller genom att visa specifika fel på Åtgärd-menyn längst upp.
3. Klicka på **kolumner** för att visa ytterligare kolumner, till exempel för att visa RPO riktar sig mot konfigurationsproblem och replikeringsfel.
4. Klicka på **Filter** att visa information baserat på specifika parametrar, till exempel replikeringshälsa eller en viss replikeringsprincip.
5. Högerklicka på en dator att initiera åtgärder som att testa redundans för den eller visa felinformationen kopplade till den.
6. Klicka på en dator för att visa mer detaljer för den. Informationen innehåller:
   - **Replikeringsinformation**: Aktuell status och hälsotillståndet för datorn.
   - **RPO** (mål för återställningspunkt): Aktuell RPO för den virtuella datorn och den tid då beräknades senast rpo-MÅLET.
   - **Återställningspunkter**: Senaste tillgängliga återställningspunkter för datorn.
   - **Redundansberedskap**: Anger om ett redundanstest kördes för datorn agent-version som körs på datorn (för datorer som kör mobilitetstjänsten) och eventuella konfigurationsproblem.
   - **Fel**: Lista över replikering fel symtomen för närvarande observerats på datorn och möjliga orsaker/åtgärder.
   - **Händelser**: En kronologisk lista över de senaste händelserna som påverkar datorn. Felinformation visar för närvarande synliga Fel-Symptom när händelser är en historisk post av problem som har påverkat datorn.
   - **Infrastruktur-vy**: Visar tillståndet för infrastruktur för scenariot när datorer replikeras till Azure.

     ![Site Recovery replikerade objekt information/overview](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Vanliga frågor

**Hur skiljer sig RPO från den senaste tillgängliga återställningspunkten?**


- Site Recovery använder en asynkron process i flera steg för att replikera datorer till Azure.
- I det näst sista steget av replikering kopieras ändringar på datorn, tillsammans med metadata, till en logg-/ cachelagringskontot.
- De här ändringarna, tillsammans med taggen för att identifiera en återställningsbara, skrivs till lagringskontot i målregionen.
-  Site Recovery kan nu skapa en återställningsbara tidpunkt för den virtuella datorn.
- Återställningspunktmålet har nu är uppfyllda för att ändringarna överförs till storage-konto än så länge. Med andra ord datorn RPO är nu lika lång tid det tar från tidsstämpel som motsvarar den återställningsbara punkten.
- Nu kan Site Recovery hämtar överförda data från storage-kontot och tillämpar den på replikeringsdiskar som har skapats för datorn.
- Site Recovery genererar en återställningspunkt och gör den här punkten tillgängliga för återställning vid en redundansväxling. Den senaste tillgängliga återställningspunkten anger därför tidsstämpeln som motsvarar den senaste återställningspunkten som redan har bearbetats och tillämpas på repliken diskarna.

> [!NOTE]
> En felaktig systemtiden på den replikerande källdatorn eller på den lokala infrastrukturservrar kommer ge skeva beräknade värde för Återställningspunktmål. För exakta RPO-rapportering, se till att systemklockan stämmer på alla servrar och datorer. 

## <a name="subscribe-to-email-notifications"></a>Prenumerera på e-postaviseringar

Du kan prenumerera på e-postmeddelanden för de här kritiska händelser:
 
- Kritiskt tillstånd för den replikerade datorn.
- Ingen anslutning mellan lokala infrastrukturkomponenter och Site Recovery-tjänsten. Anslutningen mellan Site Recovery och lokala servrar som registrerats i ett valv har identifierats med hjälp av en mekanism för pulsslag.
- Fel för redundans.

Prenumerera på följande sätt:

I valvet > **övervakning och rapporter** klickar du på **Site Recovery-händelser**.
1. Klicka på **e-postmeddelanden**.
1. I **e-postavisering**, slå på meddelanden och ange vem som ska skickas till. Du kan skicka till alla prenumerationsadministratörer skickas meddelanden och du kan också specifika e-postadresser.

    ![E-postmeddelanden](./media/site-recovery-monitor-and-troubleshoot/email.png)
