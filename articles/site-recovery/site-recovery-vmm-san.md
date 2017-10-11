---
title: "Replikera virtuella Hyper-V-datorer i VMM med SAN-nätverk med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du replikerar virtuella Hyper-V-datorer mellan två platser med Azure Site Recovery med SAN-replikering."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: 3df38802fcdc86e4553253d38c49faff455f873e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-by-using-san"></a>Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär plats med Azure Site Recovery med hjälp av SAN


Använd den här artikeln om du vill distribuera [Azure Site Recovery](site-recovery-overview.md) att hantera replikering av Hyper-V virtuella datorer (hanteras i System Center Virtual Machine Manager-moln) till en sekundär VMM-plats med hjälp av Azure Site Recovery på den klassiska portalen. Det här scenariot är inte tillgänglig i den nya Azure-portalen.



Skicka kommentarer i slutet av den här artikeln. Få svar på tekniska frågor i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="why-replicate-with-san-and-site-recovery"></a>Varför replikera med SAN-nätverk och Site Recovery?

* SAN-nätverk är en replikeringslösning på företagsnivå, skalbara så att en primär plats som innehåller Hyper-V med SAN-nätverk kan replikera LUN till en sekundär plats med SAN-nätverk. Lagring hanteras av VMM och replikering och redundans är samordnade med Site Recovery.
* Site Recovery har arbetat med flera [SAN-lagring partners](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) att tillhandahålla replikering över Fibre Channel och iSCSI-lagring.  
* Använda din befintliga SAN-infrastruktur för att skydda kritiska program som distribueras i Hyper-V-kluster. Virtuella datorer kan replikeras som en grupp så att N-nivås appar kan flyttas över konsekvent.
* SAN-replikering ger replikeringskonsekvens i olika nivåer av ett program med synkroniserade replikering för låga RTO och Återställningspunktmål och asynkron replikering för hög flexibilitet (beroende på din lagringsmatrisfunktionerna).  
* Du kan hantera SAN-lagring i VMM-infrastrukturen och Använd SMI-S i VMM för att identifiera befintlig lagring.  

Tänk på följande:
- Plats-till-plats-replikering med SAN-nätverk är inte tillgänglig i Azure-portalen. Det är bara tillgängliga i den klassiska portalen. Nytt valv kan inte skapas i den klassiska portalen. Befintliga valv kan upprätthållas.
- Replikering från SAN-nätverket till Azure stöds inte.
- Du kan replikera delade vhdx-diskar eller logiska enheter (LUN) som är direkt anslutna till virtuella datorer via iSCSI eller Fibre Channel. Gästkluster kan replikeras.


## <a name="architecture"></a>Arkitektur

![SAN-arkitektur](./media/site-recovery-vmm-san/architecture.png)

- **Azure**: konfigurera ett Site Recovery-valv i Azure-portalen.
- **SAN-lagring**: SAN-lagring hanteras i VMM-infrastrukturresurser. Du lägger till lagringsprovidern, skapar du lagringsklassificeringar och konfigurera lagringspooler.
- **VMM och Hyper-V**: Vi rekommenderar en VMM-server på varje plats. Ställ in privata VMM-moln och placera Hyper-V-kluster i dessa moln. Azure Site Recovery-providern är installerad på varje VMM-servern under distributionen och servern är registrerad i valvet. Providern kommunicerar med Site Recovery-tjänsten för att hantera replikering, redundans och återställning efter fel.
- **Replikering**: när du konfigurerar lagring i VMM och konfigurera replikering i Site Recovery replikering mellan den primära och sekundära SAN-lagringen. Inga replikeringsdata skickas till Site Recovery.
- **Redundans**: aktivera redundans i Site Recovery-portalen. Det finns ingen dataförlust under växling vid fel eftersom replikeringen är synkron.
- **Återställning efter fel**: för att växla tillbaka du aktivera omvänd replikering att överföra deltaändringar från den sekundära platsen till den primära platsen. När den omvända replikeringen är klar kör en planerad redundans från sekundär primär. Den här planerad redundans stoppar replikering VMs på den sekundära platsen och startar dem på den primära platsen.


## <a name="before-you-start"></a>Innan du börjar


**Förutsättningar** | **Detaljer**
--- | ---
**Azure** |Du behöver ett [Microsoft Azure](https://azure.microsoft.com/)-konto. Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om priserna för Site Recovery. Skapa en Azure Site Recovery-valvet för att konfigurera och hantera replikering och redundans.
**VMM** | Du kan använda en enda VMM-server och replikera mellan olika moln, men vi rekommenderar en VMM på den primära platsen och en på den sekundära platsen. En VMM kan distribueras som en fristående fysisk eller virtuell server eller ett kluster. <br/><br/>VMM-servern måste köra System Center 2012 R2 eller senare med de senaste kumulativa uppdateringarna.<br/><br/> Du behöver minst ett moln som konfigurerats på den primära VMM-servern som du vill skydda och ett moln som konfigurerats på den sekundära VMM-servern som du vill använda för redundans.<br/><br/> Källmolnet måste innehålla en eller flera VMM-värdgrupper.<br/><br/> Alla VMM-moln måste ha Hyper-V kapacitetsprofilen anges.<br/><br/> Mer information om hur du konfigurerar VMM-moln finns i [distribuera ett privat moln för Virtuella](https://technet.microsoft.com/en-us/system-center-docs/vmm/scenario/cloud-overview).
**Hyper-V** | Du behöver en eller flera Hyper-V-kluster i primära och sekundära VMM-moln.<br/><br/> Hyper-V-kluster källan måste innehålla en eller flera virtuella datorer.<br/><br/> VMM-värdgrupper i primära och sekundära platser måste innehålla minst en av Hyper-V-kluster.<br/><br/>Värd- och Hyper-V servrar måste köra Windows Server 2012 eller senare med Hyper-V-rollen och de senaste uppdateringarna installerade.<br/><br/> Om du kör Hyper-V i ett kluster och har en statisk IP-adressbaserat kluster skapas klusterutjämning inte automatiskt. Du måste konfigurera den manuellt. Mer information finns i [förbereder värdar för Hyper-V-replikering](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters).
**SAN-lagring** | Du kan replikera gäst-klustrade virtuella datorer med iSCSI eller channel-lagring eller genom att använda delade virtuella hårddiskar (vhdx).<br/><br/> Du behöver två SAN-matriser: en på den primära platsen och en på den sekundära platsen.<br/><br/> En nätverksinfrastruktur ska ställas in mellan matriserna. Peering och replikering ska konfigureras. Replikering licenser ska ställas in i enlighet med lagringskraven för matrisen.<br/><br/>Konfigurera nätverk mellan Hyper-V-värdservrar och lagringsmatrisen så att värdar kan kommunicera med LUN-lagring med hjälp av iSCSI eller Fibre Channel.<br/><br/> Kontrollera [lagringsmatriser som stöds](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> SMI-S-providrar från matrisen lagringstillverkare ska installeras och SAN-matriser ska hanteras av providern. Ställ in providern enligt tillverkarens instruktioner.<br/><br/>Se till att matrisens SMI-S-providern är på en server som VMM-servern kan komma åt via nätverket med en IP-adress eller fullständigt domännamn.<br/><br/> Varje SAN-matris ska ha en eller flera tillgängliga lagringspooler.<br/><br/> Den primära VMM-servern ska hantera den primära matrisen och sekundära VMM-servern ska hantera sekundär matrisen.
**Nätverksmappning** | Konfigurera nätverksmappning så att den replikerade virtuella datorer placeras optimalt på sekundära Hyper-V-värdservrar efter växling vid fel och så att de är anslutna till lämpliga VM-nätverk. Om du inte konfigurerar nätverksmappning ska replikering VMs inte anslutas till något nätverk efter redundansväxling.<br/><br/> Kontrollera att VMM-nätverk är korrekt konfigurerade så att du kan konfigurera nätverksmappning under distributionen av Site Recovery. De virtuella datorerna på källvärden för Hyper-V måste vara ansluten till ett VMM VM-nätverk i VMM. Nätverket ska kopplas till ett logiskt nätverk som är associerat med molnet.<br/><br/> Målmolnet bör ha ett motsvarande VM-nätverk och det i sin tur ska kopplas till ett motsvarande logiskt nätverk som är associerad med målmolnet.<br/><br/>.

## <a name="step-1-prepare-the-vmm-infrastructure"></a>Steg 1: Förbered infrastrukturen i VMM
För att förbereda infrastrukturen VMM, måste du:

1. Kontrollera att VMM-moln.
2. Integrera och klassificera SAN-lagring i VMM.
3. Skapa LUN och allokera lagringsutrymme.
4. Skapa replikeringsgrupper.
5. Konfigurera VM-nätverk.

### <a name="verify-vmm-clouds"></a>Kontrollera att VMM-moln

Kontrollera att VMM-moln har ställts in korrekt innan du börjar distributionen av Site Recovery.

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>Integrera och klassificera SAN-lagring i VMM-infrastrukturresurser

1. I VMM-konsolen går du till **Infrastrukturresurser** > **lagring** > **Lägg till resurser** > **lagringsenheter**.
2. I den **lägga till lagringsenheter** väljer **Välj en lagringsprovidertyp** och välj **SAN och NAS-enheter som upptäckts och hanterats av en SMI-S-provider**.

    ![Providertyp](./media/site-recovery-vmm-san/provider-type.png)

3. På den **Ange protokoll och adress för SMI-S-Lagrinsprovidern** väljer **SMI-S CIMXML** och ange inställningar för att ansluta till providern.
4. I **providerns IP-adress eller FQDN** och **TCP/IP-port**, ange inställningar för att ansluta till providern. Du kan bara använda en SSL-anslutning för SMI-S CIMXML.

    ![Providern ansluta](./media/site-recovery-vmm-san/connect-settings.png)
5. I **kör som-konto**, ange det kör som-konto som kan komma åt providern eller skapa ett konto.
6. På den **samla in Information** sidan VMM försöker automatiskt identifiera och importera enhetsinformation för lagring. Om du vill försöka identifiering, klickar du på **Genomsök Provider**. Om identifieringen lyckas identifierade lagringsmatriser, lagringspooler, visas tillverkare och modell kapacitet på sidan.

    ![Identifiera lagring](./media/site-recovery-vmm-san/discover.png)
7. I **Välj lagringspooler placeras under hantering och tilldela en klassificering**, Välj de lagringspooler som VMM ska hantera och tilldela dem en klassificering. LUN-information importeras från lagringspoolerna. Skapa LUN baserat på de program som du behöver skydda sina kapacitetsbehov och dina krav på vad som ska replikeras tillsammans.

    ![Klassificera lagring](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>Skapa LUN och allokera lagringsutrymme

Skapa LUN baserat på de program som du behöver skydda, kapacitetsbehov och dina krav på vad som ska replikeras tillsammans.

1. När lagringsutrymmet visas i VMM-infrastrukturen, kan du [etablera LUN](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm).

     > [!NOTE]
     > Lägg inte till virtuella hårddiskar för den virtuella datorn som har aktiverats för replikering till LUN. Om dessa LUN som inte är i en replikeringsgrupp för Site Recovery kan identifieras inte de av Site Recovery.
     >

2. Allokera lagringskapacitet i Hyper-V-värdkluster så att det kan distribuera data för virtuell dator till etablerade lagring:

   * Innan allokera lagring till klustret, måste du tilldela värdgruppen VMM som finns i klustret. Mer information finns i [så här allokerar du logiska lagringsenheter till en värdgrupp i VMM](https://technet.microsoft.com/library/gg610686.aspx) och [så här allokerar du lagringspooler till en värdgrupp i VMM](https://technet.microsoft.com/library/gg610635.aspx).
   * Allokera lagringskapacitet till klustret enligt beskrivningen i [så här konfigurerar du lagring på en Hyper-V-värdkluster i VMM](https://technet.microsoft.com/library/gg610692.aspx).

    ![Providertyp](./media/site-recovery-vmm-san/provider-type.png)
3. I **Ange protokoll och adress för SMI-S-Lagrinsprovidern**väljer **SMI-S CIMXML**. Ange inställningarna för att ansluta till providern. Du kan använda en SSL-anslutning för SMI-S CIMXML.

    ![Providern ansluta](./media/site-recovery-vmm-san/connect-settings.png)
4. I **kör som-konto**, ange det kör som-konto som kan komma åt providern eller skapa ett konto.
5. I **samla in Information**, VMM försöker automatiskt identifiera och importera enhetsinformation för lagring. Om du behöver försök klickar du på **Genomsök Provider**. När identifieringen lyckas lagringsmatriser, lagringspooler, visas tillverkare och modell kapacitet på sidan.

    ![Identifiera lagring](./media/site-recovery-vmm-san/discover.png)
7. I **Välj lagringspooler placeras under hantering och tilldela en klassificering**, Välj de lagringspooler som VMM ska hantera och tilldela dem en klassificering. LUN-information importeras från lagringspoolerna.

    ![Klassificera lagring](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>Skapa replikgrupper

Skapa en replikeringsgrupp som innehåller alla LUN som måste replikeras tillsammans.

1. I VMM-konsolen, öppna den **replikeringsgrupper** storage matris egenskaper och klicka sedan på fliken **ny**.
2. Skapa gruppen.

    ![SAN replikeringsgrupp](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>Konfigurera nätverk

Om du vill konfigurera nätverksmappning gör du följande:

1. Se Site Recovery nätverksmappning.
2. Förbereda virtuella datornätverk i VMM:

   * [Konfigurera logiska nätverk](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks).
   * [Konfigurera virtuella datornätverk](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks).


## <a name="step-2-create-a-vault"></a>Steg 2: Skapa ett valv

1. Logga in på den [Azure-portalen](https://portal.azure.com) från VMM-servern som du vill registrera i valvet.
2. Expandera **datatjänster** > **återställningstjänster**, och klicka sedan på **Site Recovery-valv**.
3. Klicka på **Skapa nytt** > **Snabbregistrering**.
4. I **Namn** anger du ett eget namn som identifierar valvet.
5. I **Region** väljer du ett geografiskt område för valvet. Regioner som stöds finns i [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Klicka på **Skapa valv**.

    ![Nytt valv](./media/site-recovery-vmm-san/create-vault.png)

Kontrollera att valvet har skapats genom att titta i statusfältet. Valvet visas som **Active** på primära **återställningstjänster** sidan.

### <a name="register-the-vmm-servers"></a>Registrera VMM-servrar

1. Öppna den **Snabbstart** sidan från den **återställningstjänster** sidan. Snabbstart kan även öppnas när som helst genom att välja ikonen.

    ![Snabbstartsikon](./media/site-recovery-vmm-san/quick-start-icon.png)
2. Välj i listrutan, **mellan Hyper-V lokal plats med matris replikering**.

    ![Registreringsnyckel](./media/site-recovery-vmm-san/select-san.png)
3. I **Förbered VMM-servrar**, ladda ned den senaste versionen av installationsfilen för Azure Site Recovery-providern.
4. Kör den här filen på VMM-källservern. Om VMM distribueras i ett kluster och du installerar providern för första gången, installera providern på en aktiv nod och slutför installationen för att registrera VMM-servern i valvet. Installera sedan providern på de andra noderna. Om du uppgraderar providern måste du uppgradera på alla noder så att de har samma providerversion.
5. Installationsprogrammet kontrollerar kraven och begäranden behörighet att stoppa VMM-tjänsten för att starta installationen av providern. Tjänsten kommer att startas om automatiskt när installationen är klar. I ett kluster i VMM uppmanas du att stoppa klusterrollen.
6. I **Microsoft Update**, kan du välja uppdateringar och uppdateringar av providern installeras enligt Microsoft Update-princip.

    ![Microsoft-uppdateringar](./media/site-recovery-vmm-san/ms-update.png)

7. Installationsplatsen för providern är som standard <SystemDrive>\Program\Microsoft System Center 2012 R2\Virtual Machine Manager\bin. Klicka på **installera** ska börja.

    ![Installationsplats](./media/site-recovery-vmm-san/install-location.png)

8. När providern är installerad klickar du på **registrera** att registrera VMM-servern i valvet.

    ![Installationen slutförd](./media/site-recovery-vmm-san/install-complete.png)

9. I **Internetanslutning**, ange hur providern ska ansluta till Internet. Välj **Använd standardproxyinställningar** om du vill använda standardinställningarna för Internet-anslutning på servern.

    ![Internetinställningar](./media/site-recovery-vmm-san/proxy-details.png)

   * Om du vill använda en anpassad proxyserver, konfigurera den innan du installerar providern. När du konfigurerar anpassade proxyinställningar körs ett test för att kontrollera proxyanslutningen.
   * Om du använder en anpassad proxy eller om din standardproxy kräver autentisering, ska du ange proxyinformationen, inklusive den adress och port.
   * De nödvändiga URL: er ska vara tillgänglig från VMM-servern.
   * Om du använder en anpassad proxyserver skapas ett VMM kör som-konto (DRAProxyAccount) automatiskt med hjälp av de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Du kan ändra inställningarna för kontot kör som i VMM-konsolen (**inställningar** > **säkerhet** > **kör som-konton**  >   **DRAProxyAccount**). Du måste starta om VMM-tjänsten för att ändringarna ska börja gälla.
10. I **registreringsnyckel**, Välj den nyckel som du hämtade från portalen och kopieras till VMM-servern.
11. I **Valvnamn** kontrollerar du namnet på valvet som servern ska registreras i.

    ![Serverregistrering](./media/site-recovery-vmm-san/vault-creds.png)
12. Krypteringsinställningen används bara för VMM till Azure-replikering. Du kan ignorera den.

    ![Serverregistrering](./media/site-recovery-vmm-san/encrypt.png)
13. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. I en klusterkonfiguration anger du namnet på VMM-klusterrollen.
14. I **synkronisera metadata för inledande moln**väljer du om du vill synkronisera metadata för alla moln på VMM-servern. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln kan du lämna den här inställningen avmarkerad och synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen.

    ![Serverregistrering](./media/site-recovery-vmm-san/friendly-name.png)

15. Slutför processen genom att klicka på **Nästa**. Efter registreringen hämtas metadata från VMM-servern av Azure Site Recovery. Servern visas i **servrar** > **VMM-servrar** i valvet.

### <a name="command-line-installation"></a>Kommandoradsinstallation

Azure Site Recovery-providern kan också installeras med hjälp av följande kommandorad. Den här metoden kan användas för att installera providern på Server Core för Windows Server 2012 R2.

1. Ladda ned installationsfilen och registreringsnyckeln för providern till en mapp. Till exempel C:\ASR.
2. Stoppa VMM-tjänsten.
3. Extrahera installationsprogrammet för providern. Köra dessa kommandon som administratör:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Installera providern:

        C:\ASR> setupdr.exe /i
5. Registrera providern:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Parametrar:

* **/ Credentials**: parametern krävs för den plats där registreringsnyckelfilen finns.  
* **/ FriendlyName**: krävs parameter för namnet på Hyper-V-värdservern som visas i Azure Site Recovery-portalen.
* **/ EncryptionEnabled**: valfri parameter som bara används vid replikering från VMM till Azure.
* **/proxyAddress**: Valfri parameter som anger adressen till proxyservern.
* **/proxyport**: Valfri parameter som anger porten för proxyservern.
* **/ proxyusername**: valfri parameter som anger användarnamnet för proxyservern (Om proxyservern kräver autentisering).
* **/ proxypassword**: valfri parameter som anger lösenordet för att autentisera med proxyservern (Om proxyservern kräver autentisering).

## <a name="step-3-map-storage-arrays-and-pools"></a>Steg 3: Mappa lagringsmatriser och pooler

Mappa primära och sekundära matriser för att ange vilka sekundära lagringspoolen tar emot replikeringsdata från den primära poolen. Mappa storage innan du konfigurerar replikering, eftersom mappningsinformationen om används när du aktiverar skydd för replikeringsgrupper.

Innan du börjar kontrollerar du att VMM-moln visas i valvet. Moln identifieras när du synkroniserar alla moln under providerinstallationen eller när du synkroniserar ett visst moln i VMM-konsolen.

1. Klicka på **resurser** > **serverlagring** > **kartan källa och Målmatriser**.
    ![Registrera servern](./media/site-recovery-vmm-san/storage-map.png)

2. Välj lagringsmatriser på den primära platsen och koppla dem till lagringsmatriser på den sekundära platsen. I **lagringspooler**, Välj en källa och mål lagringspoolen för att mappa.

    ![Serverregistrering](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>Steg 4: Konfigurera replikeringsinställningar

När VMM-servrar är registrerade, konfigurera skyddsinställningar för molnet.

1. På den **Snabbstart** klickar du på **Konfigurera skydd för VMM-moln**.
2. På den **skyddade objekt** , Välj molnet **Configuration**.
3. I **mål**väljer **VMM**.
4. I **målplatsen**, Välj VMM-servern som hanterar molntjänster som du vill använda för återställning.
5. I **mål moln**, Välj målmoln som du vill använda för VM-redundans.
   * Vi rekommenderar att du väljer ett målmoln som uppfyller återställning för de virtuella datorerna som du skyddar.
   * Ett moln kan bara tillhöra en enda molnlänkningen--som en primär eller ett målmoln.
6. Site Recovery kontrollerar att moln har åtkomst till SAN-lagring och att lagringsmatriserna mappas.
7. Om verifieringen lyckas i **replikeringstyp**väljer **SAN**.

När du har sparat inställningarna skapas ett jobb som kan övervakas i den **jobb** fliken. Du kan ändra inställningarna på den **konfigurera** fliken. Om du vill ändra målplatsen eller målmoln måste du ta bort konfigurationen för molnet och sedan konfigurera om molnet.

## <a name="step-5-enable-network-mapping"></a>Steg 5: Aktivera nätverksmappning

1. På den **Snabbstart** klickar du på **mappa nätverk**.
2. Välj VMM-källservern och välj sedan VMM-målservern som nätverk som ska mappas. Listan över källnätverken och deras associerade Målnätverk visas. Ett tomt värde visas för nätverk som inte är mappad. Klicka på informationsikonen bredvid nätverksnamn källa och mål för att visa undernät för varje nätverk.
3. Välj ett nätverk i **nätverk på källan**, och klicka sedan på **kartan**. Tjänsten identifierar VM-nätverk på målservern och visas.

    ![SAN-arkitektur](./media/site-recovery-vmm-san/network-map1.png)
4. Välj en av de Virtuella datornätverken från VMM-målservern.

    ![SAN-arkitektur](./media/site-recovery-vmm-san/network-map2.png)

5. När du väljer ett Målnätverk visas de skyddade moln som använder källnätverket. Tillgängliga Målnätverk visas också. Vi rekommenderar att du väljer ett Målnätverk som är tillgänglig för alla moln som du använder för replikering.
6. Slutför mappningen genom att klicka på bockmarkeringen. Ett jobb startar som spårar förloppet. Du kan visa den på den **jobb** fliken.

## <a name="step-6-enable-replication-for-replication-groups"></a>Steg 6: Aktivera replikering för replikeringsgrupper

Du måste aktivera replikering för replikering lagringsgrupper innan du kan aktivera skydd för virtuella datorer.

1. På den **egenskaper** sidan i det primära molnet i Site Recovery-portalen, öppna den **virtuella datorer** och klicka på **Lägg till replikeringsgrupp**.
2. Välj en eller flera VMM-replikeringsgrupper som är kopplade till molnet, verifiera matriser källan och målet och ange replikeringsfrekvensen.

Site Recovery, VMM och SMI-S-providrar etablera platsen mållagring LUN och aktivera storage-replikering. Om gruppen redan har replikerats återanvänder befintliga replikeringsrelationen Site Recovery och uppdaterar informationen.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Steg 7: Aktivera skydd för virtuella datorer


När en lagringsgrupp replikeras, aktivera skydd för virtuella datorer i VMM-konsolen med någon av följande metoder:

* **Ny virtuell dator**: när du skapar en virtuell dator kan du aktivera replikering och koppla den virtuella datorn till replikeringsgruppen.
  Med det här alternativet använder VMM intelligent placering för optimalt placerar VM-lagring på LUN i replikeringsgruppen. Site Recovery samordnar skapandet av en skugga VM på den sekundära platsen och allokerar kapacitet så att Replikdatorerna kan startas efter en redundansväxling.
* **Befintlig virtuell dator**: om en virtuell dator har redan distribuerats i VMM, kan du aktivera replikering och utföra en lagringsmigrering i en replikeringsgrupp. När du har slutfört Site Recovery i VMM och identifiera den nya virtuella datorn och börja hantera i Site Recovery. En skugga virtuell dator skapas på den sekundära platsen och kapacitet som tilldelas så att replikerade virtuella datorn kan startas efter en redundansväxling.

![Aktivera skydd](./media/site-recovery-vmm-san/enable-protect.png)

När virtuella datorer har aktiverats för replikering, visas de i Site Recovery-konsolen. Du kan visa egenskaper för Virtuella datorer, spåra status och spåra redundans replikeringsgrupper som innehåller flera virtuella datorer. I SAN-replikering, måste alla virtuella datorer som är kopplade till en replikeringsgrupp växla över tillsammans. Det beror på att växling vid fel inträffar vid lagringsskiktet först. Det är viktigt att gruppera dina replikgrupper korrekt och plats associerad endast virtuella datorer tillsammans.

> [!NOTE]
> När du har aktiverat replikering för en virtuell dator kan inte lägga till dess virtuella hårddiskar till LUN om de inte finns i en replikeringsgrupp för Site Recovery. Virtuella hårddiskar endast identifieras av Site Recovery om de finns i en replikeringsgrupp för Site Recovery.
>
>

Du kan följa förloppet, inklusive den inledande replikeringen på den **jobb** fliken. När skyddsslutförandejobbet körs är den virtuella datorn redo för redundans.

![Jobb för att skydda virtuella datorer](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>Steg 8: Testa distributionen

Testa distributionen för att kontrollera att misslyckas för virtuella datorer över som förväntat. Gör du genom att skapa en återställningsplan och köra ett redundanstest.

1. På den **Återställningsplaner** klickar du på **skapa Recovery planera**.
2. Ange ett namn för återställningsplanen och välj käll- och VMM-servrar. Källservern måste ha virtuella datorer som är aktiverade för redundans och återställning. Välj **SAN** att visa moln som har konfigurerats för SAN-replikering.

    ![Skapa en återställningsplan](./media/site-recovery-vmm-san/r-plan.png)

3. I **Välj virtuella datorer**, Välj replikeringsgrupper. Alla virtuella datorer som är associerade med gruppen läggs till i återställningsplanen. Dessa virtuella datorer har lagts till standardgruppen (grupp 1) för återställning. Du kan lägga till flera grupper om det behövs. Efter replikeringen, är virtuella datorer numrerade enligt ordningen för återställning planeringsgrupper.

    ![Välj virtuella datorer](./media/site-recovery-vmm-san/r-plan-vm.png)
4. När återställningsplanen har skapats visas den i listan på den **Återställningsplaner** fliken. Välj planen och välj **Redundanstest**.
5. På den **bekräfta Redundanstest** väljer **ingen**. Att med det här alternativet är aktiverat, den redundansväxlade Replikdatorerna inte kommer anslutas till något nätverk. Detta testar att de virtuella datorerna misslyckas över som förväntat, men det inte testa nätverksmiljön. Mer information om andra Nätverksalternativ finns [Site Recovery redundans](site-recovery-failover.md).

    ![Välj testnätverket](./media/site-recovery-vmm-san/test-fail1.png)

6. Testet virtuell dator skapas på samma värddator som värden där replikerade virtuella datorn finns. Det är inte lägga till i molnet som replikerade virtuella datorn finns.
2. Efter replikeringen, kommer replikerade virtuella datorn har en annan IP-adress än den primära virtuella datorn. Om du utfärdar adresser från DHCP, kommer den att uppdateras automatiskt. Om du inte använder DHCP och du vill att samma adresser, måste du köra några skript.
3. Kör följande skript för att hämta IP-adressen:

       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  

4. Kör det här exempelskriptet för uppdatering av DNS. Ange IP-adressen som du hämtade.

       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## <a name="next-steps"></a>Nästa steg

När du har kört ett redundanstest för att kontrollera att din miljö fungerar som förväntat finns [Site Recovery redundans](site-recovery-failover.md) att lära dig om olika typer av redundansväxlingar.
