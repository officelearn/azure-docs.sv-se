---
title: "Replikera virtuella Hyper-V-datorer till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär VMM-plats med hjälp av Azure portal."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b33a1922-aed6-4916-9209-0e257620fded
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: 777bddea6b1cb325a6f8ede00196b18e1746d80c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Replikera Hyper-V virtuella datorer i VMM-moln till en sekundär VMM-plats med hjälp av Azure portal
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-vmm.md)
> * [Klassisk portal](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Den här artikeln beskriver hur du replikera lokala Hyper-V virtuella datorer som hanteras i System Center Virtual Machine Manager (VMM)-moln, till en sekundär plats med hjälp av [Azure Site Recovery](site-recovery-overview.md) i Azure-portalen. Mer information om detta [scenariots arkitektur](site-recovery-components.md).

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Krav

**Krav** | **Detaljer**
--- | ---
**Azure** | Du behöver ett [Microsoft Azure](http://azure.microsoft.com/)-konto. Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om priserna för Site Recovery.
**Lokala VMM** | Vi rekommenderar att du har två VMM-servrar, en på den primära platsen och en i sekundärt.<br/><br/> Du kan replikera mellan moln på en VMM-server.<br/><br/> VMM-servrar måste köra minst System Center 2012 SP1 med de senaste uppdateringarna.<br/><br/> VMM-servrar måste internet-åtkomst.
**VMM-moln** | Varje VMM-servern måste ha ett eller flera moln och alla moln måste ha Hyper-V kapacitetsprofilen anges. <br/><br/>Molnen måste innehålla en eller flera VMM-värdgrupper.<br/><br/> Om du bara har en VMM-servern måste ha minst två moln fungerar som primär och sekundär.
**Hyper-V** | Hyper-V-servrar måste köra minst Windows Server 2012 med Hyper-V-rollen och har installerat de senaste uppdateringarna.<br/><br/> En Hyper-V-server måste innehålla en eller flera virtuella datorer.<br/><br/>  Hyper-V-värdservrar ska finnas i värdgrupper i de primära och sekundära VMM-molnen.<br/><br/> Om du kör Hyper-V i ett kluster på Windows Server 2012 R2, installera [uppdatera 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Om du kör Hyper-V i ett kluster på Windows Server 2012 skapas inte klusterutjämning automatiskt om du har en statisk IP-adress-kluster. Konfigurera klusterutjämningen manuellt. [Läs mer](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Hyper-V-servrar måste internet-åtkomst.
**URL: er** | VMM-servrar och Hyper-V-värdar ska kunna nå dessa webbadresser:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

## <a name="deployment-steps"></a>Distributionssteg

Här är vad du göra:

1. Kontrollera krav är uppfyllda.
2. Förbereda VMM-servern och Hyper-V-värdar.
3. Skapa ett Recovery Services-valv. Valvet innehåller konfigurationsinställningar och styr replikeringen.
4. Ange källa och mål replikering inställningar.
5. Distribuera mobilitetstjänsten på virtuella datorer som du vill replikera.
6. Förbereda för replikering och aktivera replikering för virtuella Hyper-V-datorer.
7. Kör ett redundanstest för att kontrollera att allt fungerar som förväntat.

## <a name="prepare-vmm-servers-and-hyper-v-hosts"></a>Förbereda VMM-servrar och Hyper-V-värdar

Att förbereda för distribution:

1. Kontrollera att VMM-servern och Hyper-V-värdar uppfyller kraven som beskrivs ovan och kan nå URL: er som krävs.
2. Konfigurera VMM-nätverk så att du kan konfigurera [nätverksmappning](#network-mapping-overview).

    - Se till att de virtuella datorerna på Hyper-V-källvärdservern är anslutna till ett VM-nätverk i VMM. Nätverket ska kopplas till ett logiskt nätverk som är associerat med molnet.
    Kontrollera att det sekundära moln som du använder för återställning har ett motsvarande Virtuellt datornätverk. Det Virtuella datornätverket ska kopplas till ett logiskt nätverk som är associerad med det sekundära molnet.

3. Förbereda för en [enkel serverdistributionen](#single-vmm-server-deployment), om du vill replikera virtuella datorer mellan moln på samma VMM-servern.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Nytt** > **Hantering** > **Recovery Services**.
3. I **Namn** anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du en av dem.
4. [Skapa en resursgrupp](../azure-resource-manager/resource-group-template-deploy-portal.md) eller välj en befintlig. Ange en Azure-region. Datorer replikeras till den här regionen. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/)
5. Du kan snabbt komma åt valvet från instrumentpanelen genom att klicka på **Fäst på instrumentpanelen** > **Skapa valv**.

    ![Nytt valv](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

Det nya valvet visas på den **instrumentpanelen**i **alla resurser**, och på primära **Recovery Services-valv** bladet.


## <a name="choose-a-protection-goal"></a>Välj en skyddsmål

Välj vad och vart du vill replikera.

2. Klicka på **Site Recovery** > **steg 1: Förbered infrastrukturen** > **skyddsmål**.
3. Välj **till återställningsplatsen**, och välj **Ja, med Hyper-V**.
4. Välj **Ja** att indikera att du använder VMM för att hantera Hyper-V-värdar.
5. Välj **Ja** om du har en sekundär VMM-server. Om du distribuerar replikering mellan moln på en VMM-server, klickar du på **nr**. Klicka sedan på **OK**.

    ![Välja mål](./media/site-recovery-vmm-to-vmm/choose-goals.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Installera Azure Site Recovery-providern på VMM-servrar och identifiera och registrera servrar i valvet.

1. Klicka på **steg 1: förbereda infrastrukturen** > **källa**.

    ![Konfigurera källan](./media/site-recovery-vmm-to-vmm/goals-source.png)
2. I **Förbered källa** klickar du på **+ VMM** för att lägga till en VMM-server.

    ![Konfigurera källan](./media/site-recovery-vmm-to-vmm/set-source1.png)
3. I **Lägg till Server**, kontrollera att **System Center VMM-servern** visas i **servertyp** och att VMM-servern uppfyller det [krav](#prerequisites).
4. Ladda ned installationsfilen för Azure Site Recovery-providern.
5. Ladda ned registreringsnyckeln. Du behöver den när du kör installationsprogrammet. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/site-recovery-vmm-to-vmm/set-source3.png)
6. Installera Azure Site Recovery-providern på VMM-servern. Du behöver inte installera något uttryckligen på Hyper-V-värdservrar.


### <a name="install-the-azure-site-recovery-provider"></a>Installera Azure Site Recovery-providern

1. Kör installationsfilen på varje VMM-servern för providern. Om VMM distribueras i ett kluster, gör du följande första gången du installerar:
    -  Installera providern på en aktiv nod och slutför installationen för att registrera VMM-servern i valvet.
    - Installera sedan providern på de andra noderna. Klusternoder bör köra samma version av providern.
2. Installationsprogrammet körs några kontroller av förutsättningar och kräver behörighet att stoppa VMM-tjänsten. VMM-tjänsten kommer att startas om automatiskt när installationen är klar. Om du installerar på en VMM-klustret, uppmanas du att stoppa klusterrollen.
3. I **Microsoft Update**, kan du välja för att ange att providern uppdateringar är installerade i enlighet med Microsoft Update-princip.
4. I **Installation**acceptera eller ändra standardplatsen för installation och klicka på **installera**.

    ![Installationsplats](./media/site-recovery-vmm-to-vmm/provider-location.png)
5. När installationen är klar klickar du på **registrera** att registrera servern i valvet.

    ![Installationsplats](./media/site-recovery-vmm-to-vmm/provider-register.png)
6. I **Valvnamn** kontrollerar du namnet på valvet som servern ska registreras i. Klicka på *Nästa*.

    ![Serverregistrering](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)
7. I **Internetanslutning**, ange hur providern som körs på VMM-servern ansluter till Azure.

    ![Internetinställningar](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

   - Du kan ange att providern ska ansluta direkt till internet eller via en proxyserver.
   - Ange proxy-inställningar om det behövs.
   - Om du använder en proxyserver skapas VMM RunAs-konto (DRAProxyAccount) automatiskt med de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Inställningarna för RunAs-konto kan ändras i VMM-konsolen > **inställningar** > **säkerhet** > **kör som-konton**. Starta om VMM-tjänsten för att uppdatera ändringar.
8. I **Registreringsnyckel** väljer du den nyckel som du laddade ned från Azure Site Recovery och kopierade till VMM-servern.
9. Krypteringsinställningen används bara när du replikerar virtuella Hyper-V-datorer i VMM-moln till Azure. Om du replikerar till en sekundär plats används den inte.
10. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. I en klusterkonfiguration anger du namnet på VMM-klusterrollen.
11. I **synkronisera molnmetadata**väljer du om du vill synkronisera metadata för alla moln på VMM-servern med valvet. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln kan du lämna den här inställningen avmarkerad och synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen.
12. Slutför processen genom att klicka på **Nästa**. Efter registreringen hämtas metadata från VMM-servern av Azure Site Recovery. Servern visas på fliken **VMM-servrar** på sidan **Servrar** i valvet.

    ![Server](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)
13. När servern är tillgänglig i Site Recovery-konsolen i **källa** > **Förbered källa** Välj VMM-servern och välj det moln som Hyper-V-värden finns. Klicka sedan på **OK**.

Du kan också installera providern från kommandoraden:

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj VMM-målservern och molnet.

1. Klicka på **Förbered infrastruktur** > **mål**, och välj mål-VMM-servern som du vill använda.
2. Moln på servern som är synkroniserade med Site Recovery visas. Välj målmolnet.

   ![mål](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="set-up-replication-settings"></a>Konfigurera replikeringsinställningar

- När du skapar en replikeringsprincip måste alla värden med hjälp av principen ha samma operativsystem. VMM-molnet kan innehålla Hyper-V-värdar som kör olika versioner av Windows Server, men i detta fall måste flera replikeringsprinciper.
- Du kan utföra den första replikeringen offline. [Läs mer](#prepare-for-initial-offline-replication)

1. Skapa en ny replikeringsprincip genom att klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.

    ![Nätverk](./media/site-recovery-vmm-to-vmm/gs-replication.png)
2. I **Princip för att skapa och koppla** anger du ett principnamn. Typen för källan och målet ska vara **Hyper-V**.
3. I **Hyper-V-värdversion**, Välj vilket operativsystem som körs på värden.
4. I **autentiseringstyp** och **autentiseringsport**, ange hur trafik autentiseras mellan de primära platsen och återställningsplatsen Hyper-V-värdservrarna. Välj **certifikat** om du inte har en fungerande Kerberos-miljö. Azure Site Recovery kommer automatiskt att konfigurera certifikat för HTTPS-autentisering. Du behöver inte göra något manuellt. Som standard öppnas port 8083 och 8084 (för certifikat) i Windows-brandväggen på Hyper-V-värdservrar. Om du väljer **Kerberos**, en Kerberos-biljett används för ömsesidig autentisering av värdservrar. Observera att den här inställningen gäller endast för Hyper-V-värdservrar som körs på Windows Server 2012 R2.
5. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).
6. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden i antal timmar för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en period.
7. I **frekvens av programkonsekventa ögonblicksbilder**, ange hur ofta (1 – 12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder skapas. Hyper-V använder två typer av ögonblicksbilder: en standardögonblicksbild som tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn och en programkonsekvent ögonblicksbild som tar en ögonblicksbild vid en viss tidpunkt av programdata på den virtuella datorn. Programkonsekventa ögonblicksbilder använda VSS (Volume Shadow Copy Service) för att säkerställa att programmen är i ett konsekvent tillstånd när ögonblicksbilden tas. Om du aktiverar programkonsekventa ögonblicksbilder så påverkar prestanda för program som körs på virtuella källdatorer. Kontrollera att värdet som du anger är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
8. I **dataöverföring komprimering**, ange om replikerade data som överförs ska komprimeras.
9. Välj **ta bort replikering VM**, för att ange att den replikerade virtuella datorn ska tas bort om du inaktiverar skydd för den Virtuella källdatorn. Om du aktiverar den här inställningen när du inaktiverar skyddet för källan VM det tas bort från Site Recovery-konsolen, Site Recovery-inställningarna för VMM tas bort från VMM-konsolen och repliken tas bort.
10. I **inledande Replikeringsmetod**, om du replikerar över nätverket, ange om du vill starta den inledande replikeringen eller schemalägga. Om du vill spara bandbredd i nätverket, kanske du vill schemalägga den utanför kontorstid. Klicka sedan på **OK**.

     ![Replikeringsprincip](./media/site-recovery-vmm-to-vmm/gs-replication2.png)
11. När du skapar en ny princip associeras den automatiskt med VMM-molnet. I **replikeringsprincipen**, klickar du på **OK**. Du kan associera ytterligare VMM-moln (och de virtuella datorerna i dem) med den här replikeringsprincipen i **replikering** > principnamn > **associera VMM-moln**.

     ![Replikeringsprincip](./media/site-recovery-vmm-to-vmm/policy-associate.png)


### <a name="configure-network-mapping"></a>Konfigurera nätverksmappning

- Lär dig mer om [nätverksmappning](#prepare-for-network-mapping) innan du börjar.
- Kontrollera att virtuella datorer på VMM-servrar är anslutna till ett Virtuellt datornätverk.


1. I **Site Recovery-infrastruktur** > **nätverksmappning** > **nätverksmappningar**, klickar du på **+ nätverksmappning**.

    ![Nätverksmappning](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. I **Lägg till nätverksmappning** fliken, Välj källa och mål VMM-servrar. De Virtuella datornätverk kopplade till VMM-servrarna har hämtats.
3. I **Källnätverk**, väljer nätverket du vill använda från listan över Virtuella datornätverk kopplade till den primära VMM-servern.
4. I **målnätverket**, väljer nätverket du vill använda på den sekundära VMM-servern. Klicka sedan på **OK**.

    ![Nätverksmappning](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Det här händer när nätverksmappningen börjar:

* Alla befintliga virtuella replikdatorer som motsvarar VM-källnätverket ansluts till målnätverket VM.
* Nya virtuella datorer som är anslutna till källnätverket ansluts till målnätverket mappas efter replikeringen.
* Om du ändrar en befintlig mappning med ett nytt nätverk ansluts virtuella replikdatorer med de nya inställningarna.
* Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.

### <a name="configure-storage-mapping"></a>Konfigurera lagring mappning.

[Mappning av lagring](#prepare-for-storage-mapping) stöds inte i den nya Azure-portalen. Dock kan aktiveras med hjälp av Powershell. [Läs mer](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-7-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Steg 5: Kapacitetsplanering

Nu när du har konfigurerat den grundläggande infrastrukturen är det dags att planera för kapaciteten och tänka igenom om du behöver ytterligare resurser.

- Hämta och kör den [Azure Site Recovery Capacity planner](site-recovery-capacity-planner.md), för att samla in information om replikeringsmiljön, inklusive virtuella datorer, diskar per virtuell dator och lagringsutrymme per disk.
- När du har lagrat realtid replikeringsinformation, kan du ändra NetQos principen för att kontrollera bandbredden för replikering för virtuella datorer. Läs mer om [begränsning trafik för Hyper-V-replikering](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/), på bloggen Thomas Maurer. Hämta mer information den [cmdlet New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx.).

## <a name="enable-replication"></a>Aktivera replikering

1. Klicka på **Steg 2: Replikera program** > **Källa**. När du har aktiverat replikering för första gången kan du klicka på **+ replikera** i valvet för att aktivera replikering för ytterligare datorer.

    ![Aktivera replikering](./media/site-recovery-vmm-to-vmm/enable-replication1.png)
2. I **källa**, Välj VMM-servern och det moln där Hyper-V-värdar som du vill replikera finns. Klicka sedan på **OK**.

    ![Aktivera replikering](./media/site-recovery-vmm-to-vmm/enable-replication2.png)
3. I **mål**, kontrollera sekundär VMM-servern och molnet.
4. I **virtuella datorer**, Välj de virtuella datorerna som du vill skydda från listan.

    ![Aktivera skydd för en virtuell dator](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Du kan följa förloppet för den **Aktivera skydd** åtgärden i **jobb** > **Site Recovery-jobb**. Efter den **Slutför skydd** jobbet är slutfört, den virtuella datorn är redo för redundans.

Tänk på följande:

- Du kan också aktivera skydd för virtuella datorer i VMM-konsolen. Klicka på **Aktivera skydd** i verktygsfältet i egenskaperna för virtuella datorn > **Azure Site Recovery** fliken.
- När du har aktiverat replikering, du kan visa egenskaperna för den virtuella datorn i **replikerade objekt**. På den **Essentials** instrumentpanel, hittar du information om replikeringsprincipen för den virtuella datorn och dess status. Klicka på **egenskaper** för mer information.

### <a name="onboard-existing-virtual-machines"></a>Publicera befintliga virtuella datorer
Om du har befintliga virtuella datorer i VMM som replikerar med Hyper-V-replikering, kan du publicera dem för Azure Site Recovery replikering på följande sätt:

1. Se till att Hyper-V-server som värd för den befintliga virtuella datorn finns i det primära molnet och att Hyper-V-server som värd för den virtuella replikdatorn finns i sekundära molnet.
2. Kontrollera att en replikeringsprincip har konfigurerats för det primära VMM-molnet.
3. Aktivera replikering för den primära virtuella datorn. Azure Site Recovery och VMM Kontrollera att samma replik värden och den virtuella datorn har identifierats och Azure Site Recovery att återanvända och återupprätta replikering med de angivna inställningarna.

## <a name="test-your-deployment"></a>Testa distributionen

Om du vill testa distributionen kan du köra en [redundanstestningen](site-recovery-test-failover-vmm-to-vmm.md) för en enskild virtuell dator eller [skapa en återställningsplan](site-recovery-create-recovery-plans.md) som innehåller en eller flera virtuella datorer.



## <a name="prepare-for-offline-initial-replication"></a>Förbereda för inledande offlinereplikering

Du kan göra offlinereplikering för första Datakopieringen. Du kan förbereda på följande sätt:

* På källservern, kan du ange en sökväg där dataexporten sker. Tilldela fullständig behörighet för NTFS och dela behörigheter till VMM-tjänsten på exportsökvägen. På målservern, kan du ange en sökväg där dataimporten sker. Tilldela samma behörigheter på den här importsökvägen.
* Om import- eller sökvägen är delad tilldela gruppmedlemskap för administratörer, Privilegierade användare, Skrivaransvariga eller serveransvarig för VMM-tjänstkontot på den fjärrdator där den delade finns.
* Om du använder en Kör som-konton för att lägga till värdar i import- och exportsökvägar tilldela Läs- och skrivbehörighet till Kör som-konton i VMM.
* Importera och exportera resurser bör inte finnas på en dator som används som en Hyper-V-värdserver eftersom loopback konfigurationen inte stöds av Hyper-V.
* I Active Directory på varje Hyper-V begränsad-värdserver som innehåller virtuella datorer som du vill skydda, aktivera och konfigurera delegering ska lita på fjärranslutna datorer där import- och exportsökvägar finns, enligt följande:
  1. Öppna på domänkontrollanten, **Active Directory-användare och datorer**.
  2. I konsolträdet klickar du på **DomainName** > **datorer**.
  3. Högerklicka på servernamnet för Hyper-V-värden > **egenskaper**.
  4. På den **delegering** klickar du på **förtroende för den här datorn för delegering till angivna tjänster**.
  5. Klicka på **Använd valfritt autentiseringsprotokoll**.
  6. Klicka på **lägga till** > **användare och datorer**.
  7. Skriv namnet på den dator som är värd för exportsökvägen > **OK**. Från listan över tillgängliga tjänster, håll ned CTRL och klicka på **cifs** > **OK**. Upprepa för namnet på den dator som är värd för importsökvägen. Upprepa efter behov för ytterligare Hyper-V-värdservrar.



## <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning
Mappar nätverksmappningen mellan VMM VM-nätverk på de primära och sekundära VMM-servrarna till:

* Placera optimalt Replikdatorerna på sekundära Hyper-V-värdar efter växling vid fel.
* Anslut Replikdatorerna till lämpliga VM-nätverk efter redundansväxling.

Tänk på följande:
- Du kan konfigurera nätverksmappning mellan Virtuella nätverk på två VMM-servrar eller på en VMM-server om två platser hanteras av samma server.
- När mappningen är korrekt konfigurerad och replikering har aktiverats, en virtuell dator på den primära platsen ska anslutas till ett nätverk och dess replik på målplatsen ansluts till dess mappade nätverksenheter.
- Om nätverk har konfigurerats korrekt i VMM när du väljer ett mål Virtuellt datornätverk under nätverksmappning, visas källa VMM-moln som använder källnätverket, tillsammans med tillgängliga målservrar Virtuella datornätverk på mål-moln som används för skydd.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, sedan ansluts den replikerade virtuella datorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.


Här är ett exempel som illustrerar den här mekanismen. Låt oss ta en organisation med två platser i New York och Chicago.

| **Plats** | **VMM-server** | **Virtuella datornätverk** | **Mappas till** |
| --- | --- | --- | --- |
| New York |VMM-NewYork |VMNetwork1 NewYork |Mappas till VMNetwork1 Chicago |
| VMNetwork2 NewYork |Inte mappad | | |
| Chicago |VMM-Chicago |VMNetwork1 Chicago |Mappas till VMNetwork1 NewYork |
| VMNetwork2 Chicago |Inte mappad | | |

Med det här exemplet:

* När en replikerad virtuell dator skapas för en virtuell dator som är ansluten till VMNetwork1 NewYork ansluts till VMNetwork1 Chicago.
* När en replikerad virtuell dator har skapats för VMNetwork2 NewYork eller VMNetwork2 Chicago kan ansluten den inte till något nätverk.

Här är hur VMM-moln ställs in i vårt exempelorganisation och logiska nätverk som är associerade med molnen.

### <a name="cloud-protection-settings"></a>Skyddsinställningarna för molnet
| **Skyddade moln** | **Skydda molnet** | **Logiskt nätverk (New York)** |
| --- | --- | --- |
| GoldCloud1 |GoldCloud2 | |
| SilverCloud1 |SilverCloud2 | |
| GoldCloud2 |<p>Ej tillämpligt</p><p></p> |<p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p> |
| SilverCloud2 |<p>Ej tillämpligt</p><p></p> |<p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p> |

### <a name="logical-and-vm-network-settings"></a>Inställningar för logiska och Virtuella nätverk
| **Plats** | **Logiskt nätverk** | **Associerat VM-nätverk** |
| --- | --- | --- |
| New York |LogicalNetwork1 NewYork |VMNetwork1 NewYork |
| Chicago |LogicalNetwork1 Chicago |VMNetwork1 Chicago |
| LogicalNetwork2Chicago |VMNetwork2 Chicago | |

### <a name="target-networks"></a>Målnätverk
I följande tabell visas baserat på dessa inställningar när du väljer VM målnätverket, alternativen som är tillgängliga.

| **Välj** | **Skyddade moln** | **Skydda molnet** | **Målnätverket som är tillgängliga** |
| --- | --- | --- | --- |
| VMNetwork1 Chicago |SilverCloud1 |SilverCloud2 |Tillgänglig |
| GoldCloud1 |GoldCloud2 |Tillgänglig | |
| VMNetwork2 Chicago |SilverCloud1 |SilverCloud2 |Inte tillgänglig |
| GoldCloud1 |GoldCloud2 |Tillgänglig | |


### <a name="failback"></a>Återställning efter fel
Om du vill se vad som händer vid återställning (omvänd replikering), anta att VMNetwork1 NewYork är mappad till VMNetwork1 Chicago med följande inställningar.

| **Virtuell dator** | **Ansluten till nätverket** |
| --- | --- |
| VM1 |VMNetwork1 nätverk |
| VM2 (replik av VM1) |VMNetwork1 Chicago |

Med dessa inställningar nu ska vi se vad som händer på några möjliga scenarier.

| **Scenario** | **Resultatet** |
| --- | --- |
| Ingen ändring i Nätverksegenskaper för VM-2 efter växling vid fel. |VM-1 fortfarande är ansluten till nätverket källa. |
| Nätverksegenskaper för VM-2 har ändrats efter växling vid fel och har kopplats från. |VM-1 är frånkopplad. |
| Nätverksegenskaper för VM-2 har ändrats efter växling vid fel och är ansluten till VMNetwork2 Chicago. |Om VMNetwork2 Chicago har inte mappats, kopplas VM-1. |
| Nätverksmappningen för VMNetwork1 Chicago ändras. |VM-1 ansluts till nätverket nu mappat till VMNetwork1 Chicago. |


## <a name="prepare-for-single-server-deployment"></a>Förbereda för distribution på enskild server


Om du bara har en enda VMM-server kan du replikera virtuella datorer i Hyper-V-värdar i VMM-moln till [Azure](site-recovery-vmm-to-azure.md) eller till en sekundär VMM-moln. Vi rekommenderar det första alternativet eftersom replikerar mellan moln inte sömlös. Om du vill replikera mellan moln, kan du replikera med en enda fristående VMM-server eller med en enda VMM-server som distribueras i ett sträckta Windows-kluster

### <a name="standalone-vmm-server"></a>Standalone VMM-servern

I det här scenariot distribuera enda VMM-servern som en virtuell dator i den primära platsen och replikera den här virtuella datorn till en sekundär plats med hjälp av Site Recovery och Hyper-V-replikering.

1. **Konfigurera VMM på en Hyper-V VM**. Vi rekommenderar att du samordna SQL Server-instans som används av VMM på samma virtuella dator. Detta sparar tid som en enda VM som ska skapas. Om du vill använda fjärrinstansen av SQL Server och ett avbrott inträffar måste du återställa den instansen innan du kan återställa VMM.
2. **Se till att VMM-servern har minst två moln som konfigurerats**. Ett moln innehåller de virtuella datorerna som du vill replikera och det andra molnet fungerar som den sekundära platsen. Molnet som innehåller de virtuella datorerna som du vill skydda måste uppfylla [krav](#prerequisites).
3. Ställa in Site Recovery som beskrivs i den här artikeln. Skapa och registrera VMM-servern i ett valv, ställa in en replikeringsprincip och aktivera replikering. VMM-namnen för källan och målet ska vara samma. Ange den inledande replikeringen sker över nätverket.
4. När du konfigurerar nätverksmappning mappa det Virtuella datornätverket för det primära molnet till VM-nätverk för det sekundära molnet.
5. Aktivera Hyper-V-replikering på Hyper-V-värden som innehåller VMM VM i Hyper-V Manager-konsolen och aktivera replikering på den virtuella datorn. Kontrollera att du inte lägger till den virtuella datorn i VMM till moln som skyddas av Site Recovery för att se till att Hyper-V-replikeringsinställningar inte åsidosättas av Site Recovery.
6. Om du skapar återställningsplaner för redundans kan du använda samma VMM-server för källan och målet.
7. I en fullständig avbrott växla över och återställa på följande sätt:

   1. Kör en oplanerad redundans i Hyper-V Manager-konsolen på den sekundära platsen att växla över primärt VMM VM till den sekundära platsen.
   2. Kontrollera att VMM VM är igång och körs och i valvet, kör en oplanerad redundans för att växla över de virtuella datorerna från primär till sekundär moln. Genomför en redundansväxling och välj en annan återställningspunkt om det behövs.
   3. När den oplanerade redundansväxlingen är klar, kan alla resurser nås från den primära platsen igen.
   4. När den primära platsen är tillgänglig igen i Hyper-V Manager-konsolen på den sekundära platsen kan du aktivera omvänd replikering för VMM-VM. Detta startar replikeringen för den virtuella datorn från sekundär primär.
   5. Kör en planerad redundans i Hyper-V Manager-konsolen på den sekundära platsen att växla över VMM VM till den primära platsen. Godkänn växling vid fel. Aktivera sedan omvänd replikering så att VMM VM replikeras igen från primär till sekundär.
   6. Aktivera omvänd replikering för virtuella datorer, att starta replikering av dem från sekundära för primära arbetsbelastning i Recovery Services-valvet.
   7. Kör en planerad redundans för att återställa de virtuella datorerna i arbetsbelastningen till den primära platsen i Recovery Services-valvet. Genomföra redundans för att slutföra den. Aktivera sedan omvänd replikering att starta replikering av virtuella datorerna i arbetsbelastningen från primär till sekundär.

### <a name="stretched-vmm-cluster"></a>Ambitiöst VMM-klustret

I stället för att distribuera en fristående VMM-server som en virtuell dator som replikerar till en sekundär plats, kan du VMM hög tillgänglighet genom att distribuera den som en virtuell dator i ett redundanskluster i Windows. Det ger arbetsbelastningsåterhämtning och skydd mot maskinvarufel. Om du vill distribuera med Site Recovery ska VMM VM distribueras i ett kluster för stretch på olika platser. Gör så här:

1. Installera VMM på en virtuell dator i ett redundanskluster i Windows och väljer alternativet för att köra server med hög tillgänglighet under installationen.
2. SQL Server-instansen som används av VMM ska replikeras med SQL Server AlwaysOn-Tillgänglighetsgrupper, så att det finns en replik av databasen på den sekundära platsen.
3. Följ instruktionerna i den här artikeln för att skapa ett valv, registrera servern och konfigurera skydd. Du måste registrera varje VMM-server i klustret på Recovery Services-valvet. Om du vill göra detta måste du installera providern på en aktiv nod och registrera VMM-servern. Du kan installera providern på andra noder.
4. När ett avbrott inträffar är VMM-servern och dess motsvarande SQL Server-databas redundansväxling och nås från den sekundära platsen.



## <a name="prepare-for-storage-mapping"></a>Förbered för lagringsmappning


Du ställer in lagring mappning av mappning lagringsklassificeringar på en källa och mål VMM-servrar för att göra följande:

  * **Identifiera mål lagring för virtuella replikdatorer**– en källa Virtuella hårddisk replikeras till den lagringsenhet som du angett (SMB-resurs eller kluster delade klustervolymer (CSV)) på målplatsen.
  * **Placering av virtuella datorer för replikering**– lagring mappning används för att placera optimalt replikerade virtuella datorerna på Hyper-V-värdservrar. Virtuella replikdatorer placeras på värdar som kan komma åt mappade lagringsklassificeringen.
  * **Ingen mappning har lagring**– om du inte konfigurerar mappning av lagring, virtuella datorer replikeras till standardplatsen för lagring som anges på Hyper-V-värdservern som är kopplade till den replikerade virtuella datorn.

Tänk på följande:
- Du kan konfigurera mappning mellan två VMM-moln på en enskild server.
- Lagringsklassificeringar måste vara tillgängliga för de värdgrupper som finns i källan och målet moln.
- Klassificeringar behöver inte ha samma typ av lagring. Du kan till exempel mappa en klassificering för datakälla som innehåller SMB-resurser till en mål-klassificering som innehåller CSV: er.

### <a name="example"></a>Exempel
Käll- och klassificeringar visas om klassificeringar är korrekt konfigurerade i VMM när du väljer käll- och VMM-servern under lagring mappningen. Här är ett exempel på filresurser för lagring och klassificeringar för en organisation med två platser i New York och Chicago.

| **Plats** | **VMM-server** | **Filresurs (källa)** | **Klassificering (källa)** | **Mappas till** | **Filresurs (mål)** |
| --- | --- | --- | --- | --- | --- |
| New York |VMM_Source |SourceShare1 |GULD |GOLD_TARGET |TargetShare1 |
| SourceShare2 |SILVER |SILVER_TARGET |TargetShare2 | | |
| SourceShare3 |BRONS |BRONZE_TARGET |TargetShare3 | | |
| Chicago |VMM_Target | |GOLD_TARGET |Inte mappad | |
|  |SILVER_TARGET |Inte mappad | | | |
|  |BRONZE_TARGET |Inte mappad | | | |

Med det här exemplet:

* När en replikerad virtuell dator har skapats för en virtuell dator på guld lagring (SourceShare1), kommer att replikeras till GOLD_TARGET-lagring (TargetShare1).
* När en replikerad virtuell dator har skapats för en virtuell dator på SILVER lagring (SourceShare2), kommer att replikeras till en SILVER_TARGET (TargetShare2) lagring och så vidare.

### <a name="multiple-storage-locations"></a>Flera lagringsplatser
Om mål-klassificering tilldelas till flera SMB-resurser eller CSV: er, väljs automatiskt alla optimala lagringsplatsen när den virtuella datorn är skyddad. Om det finns ingen lämplig mållagring med angiven klassificering används standardplatsen för lagring som anges i Hyper-V-värden att placera de virtuella hårddiskarna replik.

Följande tabell visar hur klassificering för lagring och klusterdelade volymer ställs in i vårt exempel.

| **Plats** | **Klassificering** | **Associerade lagringsmedia** |
| --- | --- | --- |
| New York |GULD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> |
| SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p> | |
| Chicago |GOLD_TARGET |<p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p> |
| SILVER_TARGET |<p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p> | |

Den här tabellen sammanfattas beteendet när du aktiverar skydd för virtuella datorer (VM1 - VM5) i det här exemplet-miljö.

| **Virtuell dator** | **Källservern. lagring** | **Källan klassificering** | **Mappade mål-lagringskontot** |
| --- | --- | --- | --- |
| VM1 |C:\ClusterStorage\SourceVolume1 |GULD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Båda GOLD_TARGET</p> |
| VM2 |\\FileServer\SourceShare1 |GULD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Båda GOLD_TARGET</p> |
| VM3 |C:\ClusterStorage\SourceVolume2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p> |
| VM4 |\FileServer\SourceShare2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Båda SILVER_TARGET</p> |
| VM5 |C:\ClusterStorage\SourceVolume3 |Saknas |Ingen mappning så Standardlagringsplats för Hyper-V-värden används |



### <a name="data-privacy-overview"></a>Översikt över sekretess

Den här tabellen sammanfattar hur data lagras i det här scenariot:

- - -
| Åtgärd | **Detaljer** | **Insamlade data** | **Använd** | **Krävs** |
| --- | --- | --- | --- | --- |
| **Registrering** | Du kan registrera en VMM-server i ett Recovery Services-valv. Om du vill avregistrera en server kan göra du det genom att ta bort serverinformation från Azure-portalen. | När en VMM-server är registrerad Site Recovery samlar in processer, och överför metadata om VMM-servern och namnen på VMM-moln som identifieras av Site Recovery. | Data används för att identifiera och kommunicera med lämpliga VMM-servern och konfigurera inställningar för lämpliga VMM-moln. | Den här funktionen krävs. Om du inte vill skicka den här informationen till Site Recovery kan du inte använda Site Recovery-tjänsten. |
| **Aktivera replikering** | Azure Site Recovery-providern är installerad på VMM-servern och är Överföringskanal för kommunikation med Site Recovery-tjänsten. Providern är en dynamiska länkbiblioteket (DLL) finns i VMM-processen. När du har installerat providern hämtar ”Datacenteråterställning”-funktionen har aktiverats i VMM-administratörskonsolen. Nya och befintliga virtuella datorer kan du aktivera funktionen att aktivera skyddet för en virtuell dator. |Med denna egenskap angiven skickar providern namn och ID för den virtuella datorn till Site Recovery.  Replikering har aktiverats av Windows Server 2012 eller Windows Server 2012 R2 Hyper-V-replikering. Virtuella datordata replikeras från en Hyper-V-värd till en annan (som vanligtvis finns i ett datacenter för olika ”återställning”). |Site Recovery använder metadata för att fylla i VM-information i Azure-portalen. | Den här funktionen är en viktig del av tjänsten och kan inte stängas av. Om du inte vill skicka den här informationen kan inte aktivera Site Recovery-skydd för virtuella datorer. Observera att alla data som skickas av providern till Site Recovery skickas via HTTPS. |
| **Återställningsplan** | Återställningsplaner hjälpa dig att skapa en orchestration-plan för återställning datacenter. Du kan definiera ordningen som virtuella datorer eller en grupp med virtuella datorer ska startas på återställningsplatsen. Du kan också ange eventuella automatiserade skript ska köras eller alla manuella åtgärder vidtas vid tidpunkten för återställning för varje virtuell dator. Redundans initieras vanligtvis på nivån recovery plan för samordnade återställning. | Site Recovery samlar in, bearbetas och överför metadata för återställningsplanen, inklusive virtuella metadata och metadata för alla automatiserade skript och anteckningar för manuella åtgärden. |Metadata som används för att skapa återställningsplanen i Azure-portalen. |Den här funktionen är en viktig del av tjänsten och kan inte stängas av. Om du inte vill skicka den här informationen till Site Recovery inte skapa återställningsplaner. |
| **Nätverksmappning** | Mappar nätverksinformation från den primära datacentralen till datacentret återställning. När virtuella datorer har återställts på återställningsplatsen nätverksmappning sätt kan upprätta nätverksanslutning. |Site Recovery samlar in, bearbetas och överför metadata för logiska nätverk för varje plats (primära och datacenter). |Metadata för att fylla nätverksinställningar så att du kan mappa nätverksinformationen. | Den här funktionen är en viktig del av tjänsten och kan inte stängas av. Om du inte vill skicka den här informationen till Site Recovery inte använda nätverksmappning. |
| **Växling vid fel (planerad och oplanerad/testning)** | Failover flyttas över virtuella datorer från en VMM-hanterade Datacenter till en annan. Redundansåtgärden initieras manuellt i Azure-portalen. |Providern på VMM-servern får ett meddelande om redundansväxlingen av Site Recovery och kör Redundansåtgärden på Hyper-V-värd via VMM-gränssnitt. Det är faktiska redundans för en virtuell dator från en Hyper-V-värd till en annan och hanterats av Windows Server 2012 eller Windows Server 2012 R2 Hyper-V-replikering. Aktre Site Recovery använder informationen som skickas till fylla status för växling vid fel åtgärdsinformation i Azure-portalen. | Den här funktionen är en viktig del av tjänsten och kan inte stängas av. Använd inte växling vid fel om du inte vill skicka den här informationen till Site Recovery. |

## <a name="next-steps"></a>Nästa steg

När du har testat distributionen, mer information om andra typer av [växling vid fel](site-recovery-failover.md)
