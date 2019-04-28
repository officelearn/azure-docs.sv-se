---
title: Replikera virtuella Azure Stack-datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap till Azure för virtuella datorer i Azure Stack med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: a84cbba968baf50563a2c2b0e2843d64f17bb34a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124721"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikera Azure Stack-datorer till Azure

Den här artikeln visar hur du konfigurerar haveriberedskap Azure Stack-datorer till Azure, med hjälp av den [Azure Site Recovery-tjänsten](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Site Recovery bidrar till din affärskontinuitet och haveriberedskap (BCDR) strategi. Tjänsten säkerställer att dina VM-arbetsbelastningar är tillgängliga när förväntade och oväntade avbrott inträffar.

- Site Recovery dirigerar och hanterar replikering av virtuella datorer till Azure storage.
- När ett eventuellt strömavbrott på din primära plats använder du Site Recovery för att växla över till Azure.
- Azure virtuella datorer skapas från lagrad VM-data på växling vid fel, och användarna kan fortsätta använda arbetsbelastningar som körs på de virtuella Azure-datorer.
- När allt är igång igen kan du växla tillbaka Azure virtuella datorer till din primära platsen och börja replikera till Azure storage igen.


I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * **Steg 1: Förbereda Azure stack virtuella datorer för replikering**. Kontrollera att virtuella datorer uppfyller kraven för Site Recovery och förbereda för installation av Site Recovery-mobilitetstjänsten. Den här tjänsten installeras på varje virtuell dator som du vill replikera.
> * **Steg 2: Ställer in ett Recovery Services-valv**. Ställer in ett valv för Site Recovery och ange vad du vill replikera. Site Recovery-komponenterna och åtgärder som konfigureras och hanteras i valvet.
> * **Steg 3: Konfigurera källmiljön för replikering**. Ställ in en konfigurationsserver för Site Recovery. Konfigurationsservern är en virtuell Azure Stack-dator som kör alla komponenter som krävs av Site Recovery. När du har konfigurerat konfigurationsservern kan registrera du den i valvet.
> * **Steg 4: Konfigurera målmiljön för replikeringen**. Välj ditt Azure-konto och Azure storage-konto och nätverk som du vill använda. Under replikering kopieras VM-data till Azure storage. Virtuella Azure-datorer är anslutna till det angivna nätverket efter redundansväxlingen.
> * **Steg 5: Aktivera replikering**. Konfigurera replikeringsinställningar och aktivera replikering för virtuella datorer. Mobilitetstjänsten installeras på en virtuell dator när replikering har aktiverats. Site Recovery fungerar som en inledande replikering av den virtuella datorn och sedan pågående replikeringen påbörjas.
> * **Steg 6: Kör ett programåterställningstest**: När replikering är igång kan kontrollera du att redundansväxlingen fungerar som förväntat genom att köra ett test. För att initiera detaljerade kör du ett redundanstest i Site Recovery. Redundanstestningen påverkar inte din produktionsmiljö.

Med de här stegen är klar, kan du sedan kör en fullständig redundans till Azure som och när du behöver.

## <a name="architecture"></a>Arkitektur

![Arkitektur](./media/azure-stack-site-recovery/architecture.png)

**Plats** | **Komponent** |**Detaljer**
--- | --- | ---
**Konfigurationsserver** | Körs på en virtuell Azure Stack-dator. | I varje prenumeration konfigurerar du en konfigurationsserver VM. Den här virtuella datorn körs följande Site Recovery-komponenter:<br/><br/> -Server konfiguration: Samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering. -Process server: Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar med cachelagring, komprimering och kryptering och skickar dem till Azure storage.<br/><br/> Om virtuella datorer du vill replikera överskrider de gränser som anges nedan, kan du konfigurera en separat fristående processerver. [Läs mer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Mobilitetstjänsten** | Installerad på varje virtuell dator som du vill replikera. | I steg i den här artikeln ska förbereda vi ett konto, så att mobilitetstjänsten installeras automatiskt på en virtuell dator när replikering har aktiverats. Om du inte vill installera tjänsten automatiskt, finns det ett antal andra metoder som du kan använda. [Läs mer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | I Azure behöver du ett Recovery Services-valv, ett lagringskonto och ett virtuellt nätverk. |  Replikerade data lagras i lagringskontot. Virtuella Azure-datorer har lagts till det Azure-nätverket vid redundans. 


Replikeringen fungerar på följande sätt:

1. I valvet anger du replikeringskälla och mål, ställer in konfigurationsservern, skapa en replikeringsprincip och aktiverar replikering.
2. Mobilitetstjänsten har installerats på datorn (om du har använt push-installation) och datorer börja replikering i enlighet med replikeringsprincipen.
3. En inledande kopia av server-data replikeras till Azure storage.
4. När den inledande replikeringen är klar börjar replikeringen av deltaändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
5. Konfigurationsservern samordnar replikeringshantering med Azure (port HTTPS 443 för utgående trafik).
6. Processervern tar emot data från källdatorer, optimerar och krypterar dem och skickar dem till Azure storage (port 443 för utgående trafik).
7. Replikerade datorer kommunicerar med konfigurationsservern (port HTTPS 443 inkommande, för replikeringshantering. Datorer skickar replikeringsdata till processervern (port HTTPS 9443 inkommande - kan ändras).
8. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Alternativt kan använda du Azure ExpressRoute offentliga peering. Replikering av trafik via en plats-till-plats-VPN från en lokal plats till Azure stöds inte.

## <a name="prerequisites"></a>Nödvändiga komponenter

Här är vad du behöver att ställa in det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumerationskonto** | Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
**Behörigheter för Azure-konto** | Azure-kontot du använder måste ha behörighet att:<br/><br/> – Skapa ett Recovery Service-valv<br/><br/> – Skapa en virtuell dator i resursgruppen och virtuella nätverk som du använder för scenariot<br/><br/> -Skriva till det lagringskonto som du anger<br/><br/> Tänk på följande:<br/><br/> – Om du skapar ett konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> -Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> -Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**Azure Stack VM** | Du behöver en Azure Stack-VM i klient-prenumeration, som kommer att distribueras som Site Recovery konfigurationsserver. 


### <a name="prerequisites-for-the-configuration-server"></a>Krav för konfigurationsservern

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>Steg 1: Förbereda virtuella datorer i Azure Stack

### <a name="verify-the-operating-system"></a>Kontrollera operativsystemet

Se till att de virtuella datorerna kör något av de operativsystem som sammanfattas i tabellen.


**Operativsystem** | **Detaljer**
--- | ---
**64-bitars Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (from SP1)
**CentOS** | 5.2 5.11, 6.1 6,9, 7.0 7.3
**Ubuntu** | 14.04 server LTS, 16.04 LTS server. Granska [kernlar som stöds](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Förbereda för installation av Mobilitetstjänsten

Varje virtuell dator som du vill replikera måste ha mobilitetstjänsten installerad. Kontrollera inställningarna för virtuella datorer för processervern för att installera tjänsten automatiskt på den virtuella datorn när replikering har aktiverats.

#### <a name="windows-machines"></a>Windows-datorer

- Du behöver nätverksanslutning mellan den virtuella datorn som du vill aktivera replikering och den dator som kör processervern (som standard är detta konfigurationsservern VM).
- Du behöver ett konto med administratörsrättigheter (domän eller lokalt) på datorn som du aktiverar replikering.
    - Du kan ange det här kontot när du ställer in Site Recovery. Processervern använder sedan det här kontot för att installera mobilitetstjänsten när replikering har aktiverats.
    - Det här kontot ska bara användas av Site Recovery för push-installation och för att uppdatera mobilitetstjänsten.
    - Om du inte använder ett domänkonto, måste du inaktivera kontroll av åtkomst för fjärranvändare på den virtuella datorn:
        - Skapa DWORD-värde i registret **LocalAccountTokenFilterPolicy** under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Ange värdet till 1.
        - Om du vill göra detta i Kommandotolken, skriver du följande: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- Tillåt fil- och skrivardelning- och WMI i Windows-brandväggen på den virtuella datorn som du vill replikera.
    - Gör detta genom att köra **wf.msc** att öppna Windows-brandväggens konsol. Högerklicka på **regler för inkommande trafik** > **ny regel**. Välj **fördefinierade**, och välj **fil- och skrivardelning delning** i listan. Slutför guiden, väljer för att tillåta anslutningen > **Slutför**.
    - Du kan använda ett grupprincipobjekt för domänanslutna datorer för att göra detta.

    
#### <a name="linux-machines"></a>Linux-datorer

- Kontrollera att det finns en nätverksanslutning mellan Linux-datorn och processervern.
- På den dator som du aktiverar replikering, behöver du ett konto som är en rotanvändare på Linux-källservern:
    - Du kan ange det här kontot när du ställer in Site Recovery. Processervern använder sedan det här kontot för att installera mobilitetstjänsten när replikering har aktiverats.
    - Det här kontot ska bara användas av Site Recovery för push-installation och för att uppdatera mobilitetstjänsten.
- Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
- Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
- Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
- Aktivera SFTP-undersystemet och lösenordsautentisering i sshd_config-filen:
    1. Om du vill göra detta måste du logga in som rot.
    2. Hitta raden som börjar med **PasswordAuthentication**, i filen /etc/ssh/sshd_config. Ta bort kommentarerna på raden och ändra värdet till **yes**.
    3. Hitta raden som börjar med **Subsystem** och ta bort den.

        ![Mobilitetstjänsten på Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Starta om tjänsten sshd.


### <a name="note-the-vm-private-ip-address"></a>Obs Virtuella privata IP-adress

Hitta IP-adress för varje dator som du vill replikera:

1. Klicka på den virtuella datorn i Azure Stack-portalen.
2. På den **Resource** -menyn klickar du på **nätverksgränssnitt**.
3. Anteckna privat IP-adress.

    ![Privat IP-adress](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Steg 2: Skapa ett valv och välj ett replikeringsmål

1. I Azure-portalen väljer du **skapa en resurs** > **hanteringsverktyg** > **Backup och Site Recovery**.
2. I **Namn** anger du ett eget namn som identifierar valvet. 
3. I **resursgrupp**, skapa eller välj en resursgrupp. Vi använder **contosoRG**.
4. I **plats**, anger du den Azure-regionen. Använder vi **Västeuropa**.
5. För att snabbt komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Skapa ett nytt valv](./media/azure-stack-site-recovery/new-vault-settings.png)

   Det nya valvet visas på **Instrumentpanelen** > **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

### <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

1. I **Recovery Services-valv** > Ange ett valvnamn. Vi använder **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **är dina datorer virtualiserade**väljer **inte virtualiserad/övrigt**. Välj sedan **OK**.

    ![Skyddsmål](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Steg 3: Konfigurera källmiljön

Konfigurera configuration server-dator, registrera den i valvet och identifiera datorer som du vill replikera.

1. Klicka på **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** klickar du på **+Konfigurationsserver**.

    ![Konfigurera källan](./media/azure-stack-site-recovery/plus-config-srv.png)

3. I **Lägg till Server**, kontrollerar du att **konfigurationsservern** visas i **servertyp**.
5. Ladda ned installationsfilen enhetliga installationsprogrammet för Site Recovery.
6. Ladda ned valvregistreringsnyckeln. Du behöver Registreringsnyckeln när du kör enhetliga installationsprogrammet. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Kör Azure Site Recovery ett enhetligt installationsprogram

För att installera och registrera konfigurationsservern, gör en RDP-anslutning till den virtuella datorn som du vill använda för konfigurationsservern och kör enhetliga installationsprogrammet.

Innan du börjar bör du se till att klockan är [synkroniseras med en tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) på den virtuella datorn innan du börjar. Installationen misslyckas om tiden är mer än fem minuter jämfört med lokal tid.

Nu installera konfigurationsservern:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurationsservern kan även installeras från kommandoraden. [Läs mer](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart väljer du **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.

## <a name="step-4-set-up-the-target-environment"></a>Steg 4: Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. I **Förbered infrastruktur** > **Target**, ange den prenumeration du vill använda.
2. Ange måldistributionsmodell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. Om det inte finns dem, måste du skapa minst ett lagringskonto och virtuella nätverk för att kunna slutföra guiden.


## <a name="step-5-enable-replication"></a>Steg 5: Aktivera replikering

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Klicka på **förbereda infrastrukturen** > **replikeringsinställningar**.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO).
    - I enlighet med är tidpunkten inställd skapas återställningspunkter för replikerade data.
    - Den här inställningen påverkar inte replikering, vilket är kontinuerlig. Den kan bara utfärdar en avisering om tröskelvärdet gränsen har nåtts utan en återställningspunkt skapas.
4. I **kvarhållning av återställningspunkt**, ange hur lång tid varje återställningspunkt sparas. Replikerade virtuella datorer kan återställas till valfri punkt i angivet tidsintervall.
5. I **appkonsekvent ögonblicksbildsfrekvens**, anger du hur ofta programkonsekventa ögonblicksbilder skapas.

    - En appkompatibel ögonblicksbild är en point-in-time-ögonblicksbild av AppData på den virtuella datorn.
    - Volume Shadow Copy Service (VSS) säkerställer att program på den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.
6. Välj **OK** för att skapa principen.


### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Du kan hoppa över det här steget just nu. I **distributionsplanering** listrutan klickar du på **Ja, jag har gjort det**.



### <a name="enable-replication"></a>Aktivera replikering

Kontrollera att du har slutfört alla aktiviteter i [steg 1: Förbered datorn](#step-1-prepare-azure-stack-vms). Aktivera replikering på följande sätt:

1. Välj **Replikera program** > **Källa**.
2. I **Källa** väljer du konfigurationsservern.
3. I **datortyp**väljer **fysiska datorer**.
4. Välj processerver (konfigurationsserver). Klicka sedan på **OK**.
5. I **Target**, väljer du prenumerationen och resursgruppen som du vill skapa de virtuella datorerna efter en redundansväxling. Välj den distributionsmodell som du vill använda för redundansväxlade virtuella datorerna.
6. Välj Azure storage-kontot som du vill lagra replikerade data.
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **konfigurera senare** om du vill välja Azure-nätverket separat för varje dator.
9. I **fysiska datorer**, klickar du på **+ fysisk dator**. Ange namn, IP-adress och OS-typ för varje dator som du vill replikera.

    - Använda interna IP-adressen för datorn.
    - Om du anger den offentliga IP-adressen kan replikering inte fungerar som förväntat.

10. I **egenskaper** > **konfigurera egenskaper**, väljer du det konto som processervern använder för att automatiskt installera Mobilitetstjänsten på datorn.
11. I **replikeringsinställningar** > **konfigurera replikeringsinställningar**, kontrollera att rätt replikeringsprincip har valts.
12. Klicka på **Aktivera replikering**.
13. Spåra förloppet för den **Aktivera skydd** jobb **inställningar** > **jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

> [!NOTE]
> Site Recovery installerar mobilitetstjänsten när replikering är aktiverad för en virtuell dator.
> 
> Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.
> 
> Om du vill övervaka de virtuella datorer som du lägger till, kan du se när de senast identifierades i **Konfigurationsservrar** > **Senaste kontakt**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (välj den inte) och väljer **Uppdatera**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Steg 6: Köra ett programåterställningstest

Du kan köra ett redundanstest till Azure för att se till att allt fungerar som förväntat. Den här redundansen påverkar inte din produktionsmiljö.

### <a name="verify-machine-properties"></a>Kontrollera egenskaperna för datorn

Innan du kör ett redundanstest, kontrollera egenskaperna för datorn och se till att de uppfyller [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). Du kan visa och ändra egenskaper enligt följande:

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.
2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. I **beräkning och nätverk**, ändra inställningarna efter behov.

    - Du kan ändra Azure VM-namnet, resursgrupp, Målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md), och hanterade Diskinställningar.
    - Du kan också visa och ändra inställningar. Dessa inkluderar det nätverk/undernät som den virtuella Azure-datorn är ansluten efter redundansen och IP-adressen som ska tilldelas den virtuella datorn.
1. I **diskar**, visa information om operativsystem och datadiskar på den virtuella datorn.
   

### <a name="run-a-test-failover"></a>Köra ett redundanstest

När du kör ett redundanstest händer följande:

1. En kravkontroll körs för att säkerställa att alla de villkor som krävs för redundans är på plats.
2. Redundansprocessen bearbetar data med hjälp av den angivna återställningspunkten:
    - **Senaste bearbetade**: Datorn flyttas över till den senaste återställningspunkten som bearbetats av Site Recovery. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt RTO (mål för återställningstid).
    - **Senaste appkonsekventa**: Datorn flyttas över till den senaste appkonsekventa återställningspunkten.
    - **Anpassat**: Välj den återställningspunkt som används för redundans.

3. En Azure-dator skapas med hjälp av bearbetade data.
4. Testa redundans kan automatiskt Rensa Azure virtuella datorer som skapas under detaljerade.

Kör ett redundanstest för en virtuell dator på följande sätt:

1. I **Inställningar** > **Replikerade objekt** klickar du på >**+Testa redundans** för den virtuella datorn.
2. Den här genomgången ska vi välja för att använda den **senaste bearbetade** återställningspunkt. 
3. I **Redundanstest**, Välj Azure-målnätverket.
4. Starta redundansväxlingen genom att klicka på **OK**.
5. Spåra förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Alternativt klickar du på den **Redundanstest** jobb *valvnamnet* > **inställningar** > **jobb**  > **Site Recovery-jobb**.
6. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn är rätt storlek, ansluten till rätt nätverk och körs.
7. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure. [Läs mer](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. Vill du ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på den virtuella datorn. I **anteckningar**, sparar eventuella observationer från redundanstestningen.

## <a name="fail-over-and-fail-back"></a>Redundans och återställning

När du har konfigurerat replikeringen och kör ett test för att kontrollera att allt fungerar kan kan du redundansväxla datorer till Azure efter behov.

Innan du kör en redundans om du vill ansluta till datorn i Azure efter redundansen kan sedan [förbereder för att ansluta](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) innan du börjar.

Kör en redundans enligt följande:


1. I **inställningar** > **replikerade objekt**, klickar du på datorn > **redundans**.
2. Välj den återställningspunkt som du vill använda.
3. I **Redundanstest**, Välj Azure-målnätverket.
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av källdatorn innan du påbörjar redundans med den här inställningen. Men redundansväxlingen fortsätter även om avstängningen misslyckas. 
5. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa redundansförloppet på sidan **Jobb**.
6. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Om du har förberett att ansluta efter redundans kan du kontrollera att den virtuella datorn är rätt storlek, ansluten till rätt nätverk och som körs.
7. När du har verifierat den virtuella datorn klickar du på **genomför** att slutföra redundansväxlingen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> Avbryt inte en redundansväxling pågår: Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.


### <a name="fail-back-to-azure-stack"></a>Växla tillbaka till Azure Stack

När den primära platsen är igång igen kan växla du tillbaka från Azure till Azure Stack. Om du vill göra detta måste du ladda ned VHD: N för Azure VM och överföra den till Azure Stack.

1. Stänga av Azure-dator, så att den virtuella Hårddisken kan laddas ned. 
2. Om du vill börja ladda ned den virtuella Hårddisken, installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
3. Navigera till den virtuella datorn i Azure-portalen (med VM-namn).
4. I **diskar**klickar du på namnet på disken och samla in inställningar.

    - Till exempel VHD-URI som används i vår test: https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd kan delas upp att hämta följande indataparametrar som används för att hämta den virtuella Hårddisken.
        - Lagringskonto: 502055westcentralus
        - Container: wahv9b8d2ceb284fb59287
        - Namn på virtuell Hårddisk: kopieras-3676553984.vhd

5. Nu kan du använda Azure Storage Explorer för att ladda ned den virtuella Hårddisken.
6. Ladda upp den virtuella Hårddisken till Azure Stack med [här](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm).
7. I den befintliga virtuella datorn eller en ny virtuell dator, bifoga de överförda virtuella hårddiskarna.
8. Kontrollera att OS-disken är korrekt och starta den virtuella datorn.


I det här skedet har återställning efter fel slutförts.


## <a name="conclusion"></a>Sammanfattning

I den här artikeln replikeras vi Azure Stack-datorer till Azure. Med replikering på plats körde vi ett programåterställningstest för att kontrollera att redundans till Azure fungerade som förväntat. Artikeln ingår också steg för att köra en fullständig redundans till Azure och växla tillbaka till Azure Stack.

## <a name="next-steps"></a>Nästa steg

Du kan återaktivera skyddet för den virtuella datorn och börja replikera dem till Azure igen för att gör du genom att upprepa stegen i den här artikeln när du växlar tillbaka.

