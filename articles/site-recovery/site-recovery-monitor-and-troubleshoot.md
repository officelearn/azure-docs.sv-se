---
title: Övervaka Återställning av Azure-webbplatser | Microsoft-dokument
description: Övervaka och felsöka problem med replikering och åtgärder för Azure Site Recovery med hjälp av portalen
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68717357"
---
# <a name="monitor-site-recovery"></a>Övervaka Site Recovery

I den här artikeln får du lära dig hur du övervakar Azure [Site Recovery](site-recovery-overview.md)med hjälp av inbyggd övervakning av webbplatsåterställning.  Du kan övervaka:

- Hälsa och status för datorer som replikeras av Site Recovery
- Testa felrapporteringsstatus för datorer.
- Problem och fel som påverkar konfiguration och replikering.
- Infrastrukturkomponenter som lokala servrar.


## <a name="before-you-start"></a>Innan du börjar

Du kanske vill granska [vanliga övervakningsfrågor](monitoring-common-questions.md) innan du börjar.

## <a name="monitor-in-the-dashboard"></a>Övervaka i instrumentpanelen

1. Klicka på **Översikt**i valvet . Instrumentpanelen för Återställningstjänster konsoliderar all övervakningsinformation för valvet på en enda plats. Det finns sidor för både Site Recovery och Azure Backup-tjänsten, och du kan växla mellan dem.

    ![Instrumentpanel för webbplatsåterställning](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Från instrumentpanelen kan du öka detaljnivån i olika områden. 

    ![Instrumentpanel för webbplatsåterställning](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. I **Replikerade objekt**klickar du på **Visa alla** om du vill visa alla servrar i valvet.
4. Klicka på statusinformationen i varje avsnitt om du vill öka detaljnivån.
5. Sortera övervakningsinformation efter den typ av datorer som du replikerar i **infrastrukturvyn.**

## <a name="monitor-replicated-items"></a>Övervaka replikerade objekt

I **Replikerade objekt**övervakar du hälsotillståndet för alla datorer i valvet som har replikering aktiverat.

**Status** | **Detaljer**
--- | ---
Felfri | Replikeringen fortskrider normalt. Inga fel- eller varningssymptom upptäcks.
Varning | Ett eller flera varningssymptom som kan påverka replikering upptäcks.
Kritisk | Ett eller flera kritiska replikeringsfelssymptom har upptäckts.<br/><br/> Dessa felsymptom är vanligtvis indikatorer som replikering fastnat, eller inte fortskrider så snabbt som dataändringshastigheten.
Inte tillämpligt | Servrar som för närvarande inte förväntas replikeras. Detta kan inkludera datorer som har misslyckats över.

## <a name="monitor-test-failovers"></a>Övervakare av testundans

I **Redundanstest lyckas**övervaka redundansstatus för datorer i valvet.

- Vi rekommenderar att du kör en test redundans på replikerade datorer minst en gång var sjätte månad. Det är ett sätt att kontrollera att redundans fungerar som förväntat, utan att störa din produktionsmiljö. 
- En testväxling anses lyckas först efter att redundans- och rensningen efter redundansen har slutförts.

**Status** | **Detaljer**
--- | ---
Test rekommenderas | Maskiner som inte har haft en test redundans sedan skyddet aktiverades.
Utfört framgångsrikt | Maskiner med eller fler lyckade test redundans.
Inte tillämpligt | Datorer som för närvarande inte är berättigade till en test redundans. Datorer som har fel på grund av det har till exempel inledande replikering/test redundans/redundans pågående.

## <a name="monitor-configuration-issues"></a>Problem med bildskärmskonfiguration

I **Konfigurationsproblem**övervakar du alla problem som kan påverka din förmåga att växla över.

- Konfigurationsproblem (förutom tillgänglighet för programuppdatering) identifieras av en periodisk validerare som körs var 12:e timme som standard. Du kan tvinga valideraren att köras omedelbart genom att klicka på uppdateringsikonen **bredvid avsnittsrubriken Konfigurationsproblem.**
- Klicka på länkarna för att få mer information. För problem som påverkar specifika datorer **måste du uppmärksammas** i kolumnen **Målkonfigurationer.** Detaljerna inkluderar reparationsrekommendationer.

**Status** | **Detaljer**
--- | ---
Konfigurationer som saknas | En nödvändig inställning saknas, till exempel ett återställningsnätverk eller en resursgrupp.
Resurser som saknas | Det går inte att hitta en angiven resurs eller är inte tillgänglig i prenumerationen. Resursen togs till exempel bort eller migrerades. Övervakade resurser inkluderade målgruppen målresurs, mål-VNet/undernät, logg-/mållagringskonto, inställd måltillgänglighet, mål-IP-adress.
Prenumerationskvot |  Det tillgängliga kvotsaldot för prenumerationsresurser jämförs med det saldo som krävs för att växla över alla datorer i valvet.<br/><br/> Om det inte finns tillräckligt med resurser rapporteras ett otillräckligt kvotsaldo.<br/><br/> Kvoter övervakar för vm-kärnantal, vm-familjens kärnantal, nätverkskort (NIC).
Programuppdateringar | Tillgängligheten för nya programuppdateringar och information om programvaruversioner som upphör att gälla.


## <a name="monitor-errors"></a>Övervaka fel

I **felsammanfattning**övervakar du för närvarande aktiva felsymptom som kan påverka replikering av servrar i valvet och övervaka antalet påverkade datorer.

- Fel som påverkar lokala infrastrukturkomponenter visas är början på avsnittet. Till exempel inte inleverans av ett pulsslag från Azure Site Recovery Provider på den lokala konfigurationsservern eller Hyper-V-värden.
- Därefter visas replikeringsfelssymptom som påverkar replikerade servrar.
- Tabellposterna sorteras genom att minska ordningen på fel allvarlighetsgraden och sedan genom att minska räkneordningen för de påverkade datorerna.
- Antalet påverkade servrar är ett användbart sätt att förstå om ett enda underliggande problem kan påverka flera datorer. Ett nätverksfel kan till exempel påverka alla datorer som replikeras till Azure. 
- Flera replikeringsfel kan uppstå på en enda server. I det här fallet räknar varje felsymptom den servern i listan över dess påverkade servrar. När problemet har åtgärdats förbättras replikeringsparametrarna och felet rensas från datorn.

## <a name="monitor-the-infrastructure"></a>Övervaka infrastrukturen.

I **infrastrukturvyn**övervakar du infrastrukturkomponenterna som är involverade i replikering och anslutningshälsa mellan servrar och Azure-tjänster.

- En grön linje indikerar att anslutningen är felfri.
- En röd linje med den överlagrade felikonen indikerar att det finns ett eller flera felsymptom som påverkar anslutningen.
-  Håll muspekaren över felikonen för att visa felet och antalet påverkade entiteter. Klicka på ikonen för en filtrerad lista över påverkade entiteter.

    ![Infrastrukturvy för platsåterställning (valv)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tips för övervakning av infrastrukturen

- Kontrollera att de lokala infrastrukturkomponenterna (konfigurationsserver, processservrar, VMM-servrar, Hyper-V-värdar, VMware-datorer) kör de senaste versionerna av site recovery provider och/eller agenter.
- Om du vill använda alla funktioner i infrastrukturvyn bör du köra [samlad uppdatering 22](https://support.microsoft.com/help/4072852) för dessa komponenter.
- Om du vill använda infrastrukturvyn väljer du lämpligt replikeringsscenario i din miljö. Du kan öka detaljnivån i vyn för mer information. I följande tabell visas vilka scenarier som representeras.

    **Scenario** | **Status**  | **Visa tillgänglig?**
    --- |--- | ---
    **Replikering mellan lokala platser** | Alla delstater | Inga 
    **Azure VM-replikering mellan Azure-regioner**  | Replikering aktiverad/inledande replikering pågår | Ja
    **Azure VM-replikering mellan Azure-regioner** | Misslyckades över/växlade tillbaka | Inga   
    **Replikering av VMware till Azure** | Replikering aktiverad/inledande replikering pågår | Ja     
    **Replikering av VMware till Azure** | Det gick inte att komma över/misslyckades | Inga      
    **Hyper-V-replikering till Azure** | Det gick inte att komma över/misslyckades | Inga

- Om du vill visa infrastrukturvyn för en enda replikeringsdator klickar du på **Replikerade objekt**på arkivmenyn och väljer en server.  




## <a name="monitor-recovery-plans"></a>Övervaka återställningsplaner

I **återställningsplaner**övervakar du antalet planer, skapar nya planer och ändrar befintliga planer.  

## <a name="monitor-jobs"></a>Övervaka jobb

I **Jobb**övervakar du status för site recovery-åtgärder.

- De flesta åtgärder i Azure Site Recovery körs asynkront, med ett spårningsjobb som skapas och används för att spåra förloppet för åtgärden. 
- Jobbobjektet har all information du behöver för att spåra tillståndet och förloppet för operationen. 

Övervaka jobb enligt följande:

1. I avsnittet instrumentpanel > **jobb** kan du se en sammanfattning av jobb som har slutförts, pågår eller väntar på indata under de senaste 24 timmarna. Du kan klicka på valfri stat för att få mer information om relevanta jobb.
2. Klicka på **Visa alla** om du vill visa alla jobb under de senaste 24 timmarna.

    > [!NOTE]
    > Du kan också komma åt jobbinformation från arkivmenyn > **Jobb för återställning av webbplats**. 

2. I listan **Jobb för platsåterställning** visas en lista över jobb. På den översta menyn kan du få felinformation för ett visst jobb, filtrera jobblistan baserat på specifika kriterier och exportera vald jobbinformation till Excel.
3. Du kan öka detaljnivån i ett jobb genom att klicka på det. 

## <a name="monitor-virtual-machines"></a>Övervakning av virtuella datorer

I **Replikerade objekt**får du en lista över replikerade datorer. 
    ![Lista vy över replikerade objekt för platsåterställning](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Du kan visa och filtrera information. På åtgärdsmenyn högst upp kan du utföra åtgärder för en viss dator, inklusive att köra en testväxling eller visa specifika fel.
3. Klicka på **Kolumner** om du vill visa ytterligare kolumner, till exempel om du till exempel vill visa RPO,målkonfigurationsproblem och replikeringsfel.
4. Klicka på **Filter** om du vill visa information baserat på specifika parametrar, till exempel replikeringshälsa eller en viss replikeringsprincip.
5. Högerklicka på en dator för att initiera åtgärder som test redundans för den, eller för att visa specifik felinformation som är associerad med den.
6. Klicka på en maskin för att öka detaljnivån för den. Detaljer inkluderar:
   - **Replikeringsinformation**: Maskinens aktuella status och hälsotillstånd.
   - **RPO** (återställningspunkt mål): Aktuell RPO för den virtuella datorn och den tidpunkt då RPO senast beräknades.
   - **Återställningspunkter:** Senast tillgängliga återställningspunkter för maskinen.
   - **Redundansberedskap**: Anger om en test redundans kördes för datorn, agentversionen som körs på datorn (för datorer som kör mobilitetstjänsten) och eventuella konfigurationsproblem.
   - **Fel**: Lista över replikeringsfelssymptom som för närvarande observeras på datorn och möjliga orsaker/åtgärder.
   - **Händelser**: En kronologisk lista över de senaste händelserna som påverkar maskinen. Felinformation visar de för närvarande observerbara felsymptomen, medan händelser är en historisk post med problem som har påverkat datorn.
   - **Infrastrukturvy**: Visar tillståndet för infrastruktur för scenariot när datorer replikerar till Azure.

     ![Information om replikerade objektuppgifter/översikt för webbplatsåterställning](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Prenumerera på e-postmeddelanden

Du kan prenumerera för att få e-postmeddelanden för dessa kritiska händelser:
 
- Kritiskt tillstånd för replikerad dator.
- Ingen anslutning mellan lokala infrastrukturkomponenter och tjänsten Site Recovery. Anslutningen mellan platsåterställning och lokala servrar som är registrerade i ett valv identifieras med hjälp av en pulsslagsmekanism.
- Redundansfel.

Prenumerera enligt följande:

Klicka på **Platsåterställningshändelser**i avsnittet > **övervakning.**
1. Klicka på **E-postmeddelanden**.
1. I **E-postavisering**aktiverar du aviseringar och anger vem som ska skickas till. Du kan skicka meddelanden till alla prenumerationsadministratörer och eventuellt specifika e-postadresser.

    ![E-postmeddelanden](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Nästa steg

[Läs mer om](monitor-log-analytics.md) hur du övervakar webbplatsåterställning med Azure Monitor.
