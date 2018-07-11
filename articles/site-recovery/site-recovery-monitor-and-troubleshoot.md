---
title: Övervaka och felsöka Azure Site Recovery | Microsoft Docs
description: Övervaka och felsöka problem med Azure Site Recovery-replikering och åtgärder med hjälp av portalen
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
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 84b5bf3be09083a69216802fc7f557de1a7f0ee6
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917541"
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Övervaka och felsöka Azure Site Recovery

I den här artikeln får du lära dig hur du använder Azure Site Recovery i inbyggda övervakningsfunktioner för övervakning och felsökning. Lär dig att:
> [!div class="checklist"]
> - Använd instrumentpanelen för Azure Site Recovery (vault översiktssidan)
> - Övervaka och felsöka replikeringsproblem
> - Övervaka Azure Site Recovery-jobb /-åtgärder
> - Prenumerera på e-postaviseringar

## <a name="using-the-azure-site-recovery-dashboard"></a>Med hjälp av Azure Site Recovery-instrumentpanel

Azure Site Recovery-instrumentpanel på översiktssidan för valvet sammanför alla övervakningsinformation för valvet på en enda plats. Starta på instrumentpanelen för valvet och dive djupare för att få mer information genom att gå igenom delarna av instrumentpanelen. De huvudsakliga delarna i instrumentpanelen för Azure Site Recovery är följande:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Växla mellan Azure Backup och Azure Site Recovery instrumentpaneler

Växlingsreglaget överst på översiktssidan kan du växla mellan instrumentpanelssidor för Site Recovery och säkerhetskopiering. Det här alternativet, görs en gång, sparas och standard till nästa gång du öppnar översiktssidan för valvet. Välj alternativet Site Recovery för att se Site Recovery-instrumentpanelen. 

De olika delarna i Azure Site Recovery-instrumentpanelssidan uppdateras var tionde minut, så att instrumentpanelen återspeglar den senaste tillgängliga informationen.

![Övervakningsfunktionerna i Azure Site Recovery-översiktssidan](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Replikerade objekt

Avsnittet replikerade objekt i instrumentpanelen visar en översikt över hälsotillståndet för replikering av skyddade servrar i valvet. 

<table>
<tr>
    <td>Felfri</td>
    <td>Replikeringen körs normalt för dessa servrar och inget fel eller varning symptom har identifierats.</td>
</tr>
<tr>
    <td>Varning </td>
    <td>En eller flera varningar problem som kan påverka replikering eller tyda på att replikeringen inte går normalt har identifierats för dessa servrar.</td>
</tr>
<tr>
    <td>Kritiska</td>
    <td>En eller flera kritisk replikering Fel Symptom har identifierats för dessa servrar. Problemen fel är vanligtvis indikatorer att replikeringen fastnar antingen eller är inte så snabbt som dataändringshastighet för dessa servrar.</td>
</tr>
<tr>
    <td>Inte tillämpligt</td>
    <td>Servrar som inte är för närvarande förväntas vara replikerar, till exempel servrar som har redundansväxlats.</td>
</tr>
</table>

Om du vill se en lista över skyddade servrar som filtreras efter replikeringshälsan, klickar du på replikering hälsotillstånd beskrivning bredvid ringen. Visa alla länkar i avsnittsrubriken är en genväg till sidan replikerade objekt för valvet. Använd vyn alla länk för att se en lista över alla servrar i valvet.

### <a name="3-failover-test-success"></a>3. Redundanstestet lyckades

Avsnittet redundans testet lyckades på instrumentpanelen visar en uppdelning av virtuella datorer i valvet baserat på status för testning. 

<table>
<tr>
    <td>Test som rekommenderas</td>
    <td>Virtuella datorer som inte har haft en lyckad redundanstestning sedan de nått ett skyddat läge.</td>
</tr>
<tr>
    <td>Genomfördes</td>
    <td>Virtuella datorer som har haft en eller flera lyckad redundanstestning.</td>
</tr>
<tr>
    <td>Inte tillämpligt</td>
    <td>Virtuella datorer som inte är för närvarande är berättigade till ett redundanstest. Exempel: Det gick inte över servrar, servrar för vilka den inledande replikeringen är i förlopp, servrar för vilka en redundansväxling pågår, servrar för vilka ett redundanstest pågår redan.</td>
</tr>
</table>

Klicka på Testa redundans status bredvid ringen, så se listan över skyddade servrar baserat på status för test av redundans.
 
> [!IMPORTANT]
> Som bästa praxis rekommenderar vi att du utför ett redundanstest på dina skyddade servrar minst en gång var sjätte månad. Utför ett redundanstest är ett icke störande sätt att testa redundans för dina servrar och program i en isolerad miljö och hjälper dig att utvärdera din nya beredskapsfunktioner kontinuitet för företag.

 En redundanstestet på en server eller en återställningsplan anses lyckas förrän både redundanstestet och rensa redundanstestet har slutförts.

### <a name="4-configuration-issues"></a>4. Konfigurationsproblem

Problem med konfigurationsavsnittet visar en lista över problem som kan påverka din möjlighet att har redundansväxling för virtuella datorer. Typer av problem som beskrivs i det här avsnittet är:
 - **Konfigurationer som saknas:** skyddade servrar som saknar nödvändiga konfigurationer, till exempel återställningsnätverk eller en resursgrupp för återställning.
 - **Resurser som saknas:** konfigurerade mål/återställning resurser hittades inte eller inte tillgängliga i prenumerationen. Resursen har tagits bort eller har migrerats till en annan prenumeration eller resursgrupp. Följande konfigurationer för mål-/ återställning övervakas för tillgänglighet: målresursgruppen, virtuella nätverk och undernät, logg-/ mållagringskontot storage-konto, mål måltillgänglighetsuppsättning, mål-IP.
 - **Prenumerationskvoten:** tillgänglig prenumeration kvot resursbalansen jämförs balans som krävs för att kunna redundansväxla alla virtuella datorer i valvet. Otillräcklig kvot saldo rapporteras om saldot hittas inte tillräckligt med. Kvoter för följande Azure-resurser övervakas: antal för VM-kärnor, antal för VM-familjen kärnor, antal nätverkskort (NIC)-gränssnittet.
 - **Programuppdateringar:** tillgängligheten för nya programuppdateringar upphör att gälla programvaruversioner.

Konfigurationsproblem (andra än tillgänglighet av programuppdateringar) identifieras av en regelbunden verifieraren åtgärd som körs var 12: e timme som standard. Du kan tvinga verifieraren åtgärden att köra omedelbart genom att klicka på Uppdatera-ikonen bredvid den *konfigurationsproblem* avsnittsrubriken.

Klicka på länkarna för att få mer information om de listade problem och virtuella datorer som påverkas av dem. För problem som påverkar specifika virtuella datorer, kan du få mer information genom att klicka på den **behöver åtgärdas** länken under kolumnen mål konfigurationer för den virtuella datorn. Informationen innehåller rekommendationer om hur du kan åtgärda identifierade problem.

### <a name="5-error-summary"></a>5. Sammanfattning av fel vid

Sammanfattning av felavsnittet visas för tillfället aktiv replikering fel problemen som kan påverka replikering av servrar i valvet, tillsammans med antalet berörda entiteter på grund av varje fel.

Replikering fel symptomen för servrar i ett kritiskt eller varningshälsotillstånd replikering hälsotillstånd kan ses i sammanfattningen av fel. 

- Fel som påverkar den lokala infrastrukturkomponenter, t.ex icke-mottagandet av pulsslag från Azure Site Recovery-providern som körs på den lokala konfigurationsservern, VMM-servern eller Hyper-V-värden visas i början av felsammanfattning avsnittet
- Replikering Fel Symptom påverka skyddade servrar visas härnäst. Sammanfattningstabell felposter sorteras i fallande ordning efter allvarlighetsgrad för fel och sedan i fallande ordning efter antalet berörda servrar.
 

> [!NOTE]
> 
>  Flera replikering fel symptom kan observeras på en enskild server. Om det finns flera fel symptom på en enskild server räknas varje fel symtom servern i listan över de berörda servrarna. När det underliggande problemet, vilket resulterar i ett fel symtom är fast, förbättra replikeringsparametrarna och felet har raderats från den virtuella datorn.
>
> > [!TIP]
> Antalet berörda servrar är ett bra sätt att förstå om ett enda underliggande problem kan påverka flera servrar. Till exempel kan ett nätverk glapp påverka alla servrar som replikeras från en lokal plats till Azure. Den här vyn ger snabbt att åtgärda ett underliggande problem korrigerar replikering för flera servrar.
>

### <a name="6-infrastructure-view"></a>6. Infrastruktur-vy

Infrastruktur-vy innehåller en scenariot klokt visuell representation av infrastrukturen komponenterna som ingår i replikeringen. Den visar även visuellt hälsotillståndet för anslutningen mellan de olika servrarna och mellan servrar och de Azure-tjänsterna som ingår i replikeringen. 

En grön linje visar att anslutningen är felfri, medan en röd linje med putsade felikonen anger förekomsten av en eller flera fel inträffa som påverkar anslutningen mellan komponenterna som ingår. Hovra muspekaren över felikonen på raden visar felet och antal berörda entiteter. 

Att klicka på felikonen visar en filtrerad lista över berörda entiteter för felmeddelandena.

![Site Recovery infrastruktur-vy (valv)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Se till att den lokala infrastrukturkomponenter (konfigurationsserver, ytterligare processervrar replikera virtuella VMware-datorer, Hyper-V-värdar, VMM-servrar) kör den senaste versionen av Azure Site Recovery-programvaran. Om du vill kunna använda alla funktioner i infrastrukturvyn du måste köra [samlad 22 uppdatering](https://support.microsoft.com/help/4072852) eller senare för Azure Site Recovery

Om du vill använda infrastruktur-vy, Välj lämplig replikeringsscenariot (Azure-datorer, virtuella datorer/fysiska VMware-server eller Hyper-V) beroende på din källmiljö. Infrastruktur-vy som visas på översiktssidan för valvet är en sammansatt vy för valvet. Du kan öka detaljnivån ned vidare till enskilda komponenter genom att klicka på rutorna.

En infrastruktur-vy som är begränsade till sammanhanget på en replikerande dator är tillgänglig på översiktssidan för replikerade objektet. Gå till översiktssidan för en replikerande server genom att gå till replikerade objekt valvmenyn och välj servern för att se information om.

### <a name="infrastructure-view---faq"></a>Infrastruktur-vy – vanliga frågor och svar

**F.** Varför ser jag infrastruktur-vy för den virtuella datorn inte? </br>
**S.** Infrastruktur visa funktionen är endast tillgänglig för virtuella datorer som replikerar till Azure. Funktionen är för närvarande inte tillgänglig för virtuella datorer som replikerar mellan lokala platser.

**F.** Varför visas antalet virtuella datorer i infrastrukturvyn valv skiljer sig från det totala antalet i ringdiagrammet replikerade objekt?</br>
**S.** Infrastruktur-vy valvet är begränsad av replikeringsscenarier. Endast virtuella datorer som deltar i det markerade replikeringsscenariot ingår i antalet virtuella datorer visas i infrastrukturvyn. För det valda scenariot ingår dessutom endast virtuella datorer som är konfigurerade för att replikera till Azure i antal virtuella datorer som visas i infrastrukturvyn (Fo exempel: redundansväxlade virtuella datorer, virtuella datorer som replikeras tillbaka till en lokal plats ingår inte i infrastrukturvyn.)

**F.** Varför är antalet replikerade objekt som visas i lådan essentials på översiktssidan skiljer sig från det totala antalet replikerade objekt som visas i ringdiagrammet på instrumentpanelen?</br>
**S.** Endast de virtuella datorerna för vilka den inledande replikeringen har slutförts som ingår i antalet som visas i essentials-lådan. Den totala ringen för replikerade objekt som innehåller alla virtuella datorer i valvet inklusive servrar för vilka den inledande replikeringen pågår.

**F.** Vilka replikeringsscenarier är tillgänglig för infrastruktur-vy? </br>
**S.**
>[!div class="mx-tdBreakAll"]
>|Replikeringsscenario  | Tillstånd för virtuell dator  | Infrastruktur-vy som är tillgängliga  |
>|---------|---------|---------|
>|Virtuella datorer som replikeras mellan två lokala platser     | -        | Nej      |
>|Alla     | Redundansväxlats         |  Nej       |
>|Virtuella datorer som replikeras mellan två Azure-regioner     | Den inledande replikeringen pågår eller skyddade         | Ja         |
>|VMware virtuella datorer som replikeras till Azure     | Den inledande replikeringen pågår eller skyddade        | Ja        |
>|VMware virtuella datorer som replikeras till Azure     | Redundansväxlade virtuella datorer som replikeras tillbaka till en lokal VMware-webbplats         | Nej        |
>|Hyper-V virtuella datorer som replikeras till Azure     | Den inledande replikeringen pågår eller skyddade        | Ja       |
>|Hyper-V virtuella datorer som replikeras till Azure     | Redundans / återställning efter fel pågår        |  Nej       |


### <a name="7-recovery-plans"></a>7. Återställningsplaner

Avsnittet planer visar antalet av återställningsplaner i valvet. Klicka på siffran för att se en lista över återställningsplaner, skapa nya återställningsplaner eller redigera befintliga. 

### <a name="8-jobs"></a>8. Jobb

Azure Site Recovery-jobb spåra statusen för Azure Site Recovery-åtgärder. De flesta åtgärder i Azure Site Recovery utförs asynkront, med ett spårnings-jobb som används för att följa förloppet för åtgärden.  Läs hur du övervakar statusen för en åtgärd i den [övervaka Azure Site Recovery-jobb/åtgärder](#monitor-azure-site-recovery-jobsoperations) avsnittet.

Det här avsnittet för jobb i instrumentpanelen ger följande information:

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
    <td>Azure Site Recovery-jobb som för närvarande har pausats väntar på indata från användaren.</td>
</tr>
</table>

Visa alla länken bredvid avsnittsrubriken är en genväg för att gå till sidan med listan över jobb.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Övervaka och felsöka replikeringsproblem

Du kan få ytterligare information och felsökningsinformation i sidan med listan över virtuella datorer och sidan virtuell dator utöver informationen i valvet instrumentpanelssidan. Du kan visa listan över skyddade virtuella datorer i valvet genom att välja den **replikerade objekt** alternativet valvmenyn. Alternativt kan får du en filtrerad lista över de skyddade objekten genom att klicka på någon av begränsade genvägar som är tillgänglig på sidan för instrumentpanelen för valvet.

![Site Recovery replikerade objekt listvy](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

Filteralternativet på sidan med replikerade objekt kan du använda olika filter, till exempel replikeringshälsa och replikeringsprincip. 

Alternativet kolumn Väljaren kan du ange ytterligare kolumner som ska visas som RPO, mål konfigurationsproblem och replikeringsfel. Du kan initiera åtgärder på en virtuell dator eller visa fel som påverkar den virtuella datorn genom att högerklicka på en viss rad i listan över datorer.

Om du vill öka detaljnivån ytterligare, väljer du en virtuell dator genom att klicka på den. Då öppnas sidan virtuell dator. Översiktssidan översikt under information om den virtuella datorn innehåller en instrumentpanel där du hittar ytterligare information som hör till datorn. 

På översiktssidan för replikerande dator hittar du:
- RPO (mål för återställningspunkt): aktuell RPO för den virtuella datorn och den tid då beräknades senast rpo-MÅLET.
- Senaste tillgängliga återställningspunkter för datorn
- Konfigurationsproblem om någon som kan påverka redundansberedskap för datorn. Klicka på länken om du vill ha mer information.
- Felinformation: lista över replikering fel symtomen för närvarande observerats på datorn tillsammans med möjliga orsaker och rekommenderade reparationer
- Händelser: En kronologisk lista över de senaste händelserna som påverkar datorn. Felinformation visar för närvarande synliga fel symptom på datorn, händelser är en historisk post av olika händelser som kan påverka den datorn inklusive fel inträffa som kanske tidigare har såg för datorn.
- Infrastruktur-vy för datorer som replikeras till Azure

![Site Recovery replikerade objekt information/overview](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

Åtgärd-menyn överst på sidan ger alternativ för att utföra olika åtgärder som redundanstest på den virtuella datorn. Knappen fel information på Åtgärd-menyn kan du se alla aktiva fel inklusive replikeringsfel, konfigurationsproblem och bästa praxis baserat konfigurationsvarningar för den virtuella datorn.

> [!TIP]
> Hur skiljer sig rpo-mål eller recovery point objective från den senaste tillgängliga återställningspunkten?
> 
>Azure Site Recovery använder en asynkron process i flera steg för att replikera virtuella datorer till Azure. I det näst sista steget av replikering kopieras de senaste ändringarna på den virtuella datorn tillsammans med metadata till en logg-/ cachelagringskontot. När dessa ändringar tillsammans med taggen för att identifiera en återställningsbara har skrivits till lagringskonto i målregionen, har Azure Site Recovery informationen som behövs för att generera en återställningsbara tidpunkt för den virtuella datorn. Återställningspunktmålet har nu är uppfyllda för att ändringarna överförs till storage-konto än så länge. Återställningspunktmålet för den virtuella datorn nu uttrycks i tidsenheter, med andra ord är lika lång tid det tar från tidsstämpel som motsvarar den återställningsbara punkten.
>
>Azure Site Recovery-tjänsten, i bakgrunden, hämtar överförda data från storage-kontot och använder dem till replikeringsdiskar som har skapats för den virtuella datorn. Den genererar en återställningspunkt och gör den här punkten tillgängliga för återställning vid en redundansväxling. Den senaste tillgängliga återställningspunkten anger tidsstämpeln som motsvarar den senaste återställningspunkten som redan har bearbetats och tillämpas på repliken diskarna.
>> [!WARNING]
> En skeva klockan eller felaktig systemklockan på den replikerande källdatorn eller infrastrukturservrar lokala kommer ge skeva beräknade värde för Återställningspunktmål. För att säkerställa att korrekt rapportering av RPO Kontrollera värden att systemklockan på servrar som ingår i replikeringen är korrekt. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Övervaka Azure Site Recovery-jobb /-åtgärder

Azure Site Recovery utför de åtgärder som du anger asynkront. Exempel på åtgärder du kan utföra är Aktivera replikering, skapa en återställningsplan, testa redundans, uppdatera replikeringsinställningarna osv. Varje sådan åtgärd har ett motsvarande jobb som skapas för att spåra och granska igen. Objektet har all nödvändig information som krävs att spåra status och förlopp för åtgärden. Du kan spåra statusen för de olika Site Recovery-åtgärderna för valvet från sidan jobb. 

Att se en lista över Site Recovery-jobb för valvet går den **övervakning och rapporter** avsnittet i menyn och välj jobb > Site Recovery-jobb. Välj ett jobb i listan över jobb på sidan genom att klicka på den för att få mer information om det angivna jobbet. Om ett jobb inte har slutförts eller innehåller fel kan se du mer information på fel och möjliga åtgärder genom att klicka på knappen fel information högst upp på jobbet informationssidan (även tillgänglig från sidan jobb genom att högerklicka på den misslyckade jobbet.) Du kan använda filteralternativet Åtgärd-menyn överst på sidan för jobb-listan för att filtrera listan baserat på ett visst villkor Använd knappen exportera för att exportera information om de valda jobben till en excel. Du kan även använda listvyn jobb från genvägen på instrumentpanelssidan Site Recovery. 

 För åtgärder som du utför från Azure-portalen, kan skapade jobbet och aktuell status också spåras från meddelandeavsnittet (klockikonen längst upp till höger) i Azure-portalen.

## <a name="subscribe-to-email-notifications"></a>Prenumerera på e-postaviseringar

Inbyggd e-postaviseringsfunktionen kan du prenumerera på e-postmeddelanden för kritiska händelser. Om du prenumererar på, skickas e-postmeddelanden för följande händelser:
- Tillståndet för en replikdator försämra till kritiskt replikeringen.
- Ingen anslutning mellan lokala infrastrukturkomponenter och Azure Site Recovery-tjänsten. Anslutning till Site Recovery-tjänsten från en lokal infrastrukturkomponenter, t.ex konfigurationsservern (VMware) eller System Center Virtual Machine Manager(Hyper-V) registrerad för valvet har identifierats med hjälp av en mekanism för pulsslag.
- Redundans åtgärden fel om några.

Om du vill prenumerera på e-postmeddelanden för Azure Site Recovery, går den **övervakning och rapporter** på menyn för valvet och:
1. Välj aviseringar och händelser > Site Recovery-händelser.
2. Välj ”e-postmeddelanden” på menyn på sidan för evenemang som öppnas.
3. Använd guiden e-postavisering att aktivera eller inaktivera e-postmeddelanden och Välj mottagare av meddelanden. Du kan ange att alla prenumerationsadministratörer skickas meddelanden, och/eller ange en lista över e-postadresser du skickar meddelanden till. 
