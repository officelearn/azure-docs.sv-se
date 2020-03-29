---
title: Replikera virtuella Azure Stack-datorer till Azure med Azure Site Recovery | Microsoft-dokument
description: Lär dig hur du konfigurerar haveriberedskap till Azure för virtuella Azure Stack-datorer med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 15cd729063545914f791de39a075af9084f72bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426573"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikera virtuella Azure Stack-datorer till Azure

Den här artikeln visar hur du konfigurerar virtuella virtuella azure stack-virtuella virtuella azure stack-datorer med hjälp av [Azure Site Recovery-tjänsten](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Site Recovery bidrar till din strategi för affärskontinuitet och haveriberedskap (BCDR). Tjänsten säkerställer att dina VM-arbetsbelastningar förblir tillgängliga när förväntade och oväntade avbrott inträffar.

- Site Recovery dirigerar och hanterar replikering av virtuella datorer till Azure-lagring.
- När ett avbrott inträffar på din primära plats använder du Site Recovery för att växla över till Azure.
- Vid redundans skapas virtuella Azure-datorer från lagrade VM-data och användare kan fortsätta att komma åt arbetsbelastningar som körs på dessa virtuella Azure-datorer.
- När allt är igång igen kan du återställa virtuella Azure-datorer till din primära plats och börja replikera till Azure-lagring igen.


I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * **Steg 1: Förbereda virtuella Azure-stack-datorer för replikering**. Kontrollera att virtuella datorer uppfyller kraven för webbplatsåterställning och förbered installation av tjänsten Site Recovery Mobility. Den här tjänsten är installerad på varje virtuell dator som du vill replikera.
> * **Steg 2: Konfigurera ett recovery services-valv**. Konfigurera ett valv för platsåterställning och ange vad du vill replikera. Site Recovery komponenter och åtgärder konfigureras och hanteras i valvet.
> * **Steg 3: Ställ in källreplikeringsmiljön**. Konfigurera en konfigurationsserver för platsåterställning. Konfigurationsservern är en enda Virtuell Azure Stack som kör alla komponenter som behövs av Site Recovery. När du har konfigurerat konfigurationsservern registrerar du den i valvet.
> * **Steg 4: Ställ in målreplikeringsmiljön**. Välj ditt Azure-konto och det Azure-lagringskonto och det nätverk som du vill använda. Under replikering kopieras VM-data till Azure-lagring. Efter redundans ansluts virtuella Azure-datorer till det angivna nätverket.
> * **Steg 5: Aktivera replikering**. Konfigurera replikeringsinställningar och aktivera replikering för virtuella datorer. Mobilitetstjänsten installeras på en virtuell dator när replikeringen är aktiverad. Site Recovery utför en första replikering av den virtuella datorn och sedan börjar den pågående replikeringen.
> * **Steg 6: Kör en haveriberedskapsövning:** När replikeringen är igång kontrollerar du att redundansen fungerar som förväntat genom att köra en övning. Om du vill starta övningen kör du en testundanställning i Site Recovery. Test failover påverkar inte din produktionsmiljö.

Med de här stegen slutförda kan du sedan köra en fullständig redundans till Azure om och när du behöver.

## <a name="architecture"></a>Arkitektur

![Arkitektur](./media/azure-stack-site-recovery/architecture.png)

**Location** | **Komponent** |**Detaljer**
--- | --- | ---
**Konfigurationsserver** | Körs på en enda virtuell Azure Stack-dator. | I varje prenumeration ställer du in en vm-tjänst för konfigurationsserver. Den här virtuella datorn kör följande site recovery-komponenter:<br/><br/> - Konfigurationsserver: Samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering. - Processserver: Fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar med cachelagring, komprimering och kryptering. och skickar den till Azure-lagring.<br/><br/> Om virtuella datorer som du vill replikera överskrider de gränser som anges nedan kan du ställa in en separat fristående processserver. [Läs mer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Mobilitetstjänst** | Installerad på varje virtuell dator som du vill replikera. | I stegen i den här artikeln förbereder vi ett konto så att mobilitetstjänsten installeras automatiskt på en virtuell dator när replikering är aktiverad. Om du inte vill installera tjänsten automatiskt finns det ett antal andra metoder som du kan använda. [Läs mer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | I Azure behöver du ett Recovery Services-valv, ett lagringskonto och ett virtuellt nätverk. |  Replikerade data lagras i lagringskontot. Virtuella Azure-datorer läggs till i Azure-nätverket när redundans inträffar. 


Replikering fungerar på följande sätt:

1. I valvet anger du replikeringskällan och målet, ställer in konfigurationsservern, skapar en replikeringsprincip och aktiverar replikering.
2. Mobilitetstjänsten är installerad på datorn (om du har använt push-installation) och datorer börjar replikering i enlighet med replikeringsprincipen.
3. En första kopia av serverdata replikeras till Azure-lagring.
4. När den första replikeringen är klar börjar replikering av deltaändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
5. Konfigurationsservern dirigerar replikeringshantering med Azure (port HTTPS 443 utgående).
6. Processservern tar emot data från källdatorer, optimerar och krypterar den och skickar den till Azure-lagring (port 443 utgående).
7. Replikerade datorer kommunicerar med konfigurationsservern (port HTTPS 443 inkommande, för replikeringshantering. Datorer skickar replikeringsdata till processservern (port HTTPS 9443 inkommande - kan ändras).
8. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute offentlig peering. Replikering av trafik via en plats-till-plats-VPN från en lokal plats till Azure stöds inte.

## <a name="prerequisites"></a>Krav

Det här behöver du för att konfigurera det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumerationskonto** | Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
**Azure-kontobehörigheter** | Det Azure-konto som du använder behöver behörigheter för att:<br/><br/> - Skapa ett recovery service-valv<br/><br/> - Skapa en virtuell dator i resursgruppen och det virtuella nätverk som du använder för scenariot<br/><br/> - Skriv till det lagringskonto du anger<br/><br/> Tänk på följande:<br/><br/> -Om du skapar ett konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> - Om du använder en befintlig prenumeration och inte är administratör måste du arbeta med administratören för att tilldela dig behörigheter för ägare eller deltagare.<br/><br/> - Om du behöver mer detaljerade behörigheter, granska [den här artikeln](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**Virtuell Azure Stack** | Du behöver en Azure Stack-virtuell dator i klientprenumerationen som distribueras som konfigurationsserver för platsåterställning. 


### <a name="prerequisites-for-the-configuration-server"></a>Förutsättningar för konfigurationsservern

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>Steg 1: Förbereda virtuella Azure Stack-datorer

### <a name="verify-the-operating-system"></a>Verifiera operativsystemet

Kontrollera att de virtuella datorerna kör ett av de operativsystem som sammanfattas i tabellen.


**Operativsystem** | **Detaljer**
--- | ---
**64-bitars Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (från SP1)
**Centos** | 5,2 till 5,11, 6,1 till 6,9, 7,0 till 7,3
**Ubuntu** | 14.04 LTS-server, 16,04 LTS-server. Granska [kärnor som stöds](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Förbered för installation av mobilitetstjänster

Varje virtuell dator som du vill replikera måste ha mobilitetstjänsten installerad. Kontrollera vm-inställningarna för att processservern ska installeras automatiskt på den virtuella datorn när replikeringen är aktiverad.

#### <a name="windows-machines"></a>Windows-maskiner

- Du behöver nätverksanslutning mellan den virtuella datorn där du vill aktivera replikering och datorn som kör processservern (som standard är detta konfigurationsservern VM).
- Du behöver ett konto med administratörsrättigheter (domän eller lokal) på den dator som du aktiverar replikering för.
    - Du anger det här kontot när du konfigurerar Webbplatsåterställning. Sedan använder processservern det här kontot för att installera mobilitetstjänsten när replikering är aktiverad.
    - Det här kontot används endast av Site Recovery för push-installationen och för att uppdatera mobilitetstjänsten.
    - Om du inte använder ett domänkonto måste du inaktivera kontrollen Fjärråtkomst på den virtuella datorn:
        - Skapa DWORD-värde **LocalAccountTokenFilterPolicy** i registret under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Ange värdet 1.
        - Om du vill göra detta i kommandotolken skriver du följande: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- I Windows-brandväggen på den virtuella datorn som du vill replikera tillåter du Fil- och skrivardelning och WMI.
    - Det gör du genom att köra **wf.msc** för att öppna windows-brandväggskonsolen. Högerklicka på **Inkommande regler** > **Ny regel**. Välj **Fördefinierad**och välj **Fil- och skrivardelning** i listan. Slutför guiden, välj om du vill tillåta anslutningen > **Slutför**.
    - För domändatorer kan du använda ettrincipobjekt för att göra detta.

    
#### <a name="linux-machines"></a>Linux-maskiner

- Kontrollera att det finns en nätverksanslutning mellan Linux-datorn och processervern.
- På den dator som du aktiverar replikering för behöver du ett konto som är en rotanvändare på käll-Linux-servern:
    - Du anger det här kontot när du konfigurerar Webbplatsåterställning. Sedan använder processservern det här kontot för att installera mobilitetstjänsten när replikering är aktiverad.
    - Det här kontot används endast av Site Recovery för push-installationen och för att uppdatera mobilitetstjänsten.
- Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
- Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
- Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
- Aktivera SFTP-undersystemet och lösenordsautentisering i sshd_config-filen:
    1. För att göra detta, logga in som root.
    2. Hitta raden som börjar med **PasswordAuthentication**, i filen /etc/ssh/sshd_config. Ta bort kommentarerna på raden och ändra värdet till **yes**.
    3. Hitta raden som börjar med **Subsystem** och ta bort den.

        ![Tjänsten Linux Mobility](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Starta om sshd-tjänsten.


### <a name="note-the-vm-private-ip-address"></a>Observera den privata IP-adressen för den virtuella datorn

Leta reda på IP-adressen för varje dator som du vill replikera:

1. Klicka på den virtuella datorn i Azure Stack Portal.
2. Klicka på **Nätverksgränssnitt**på **Resurs-menyn** .
3. Anteckna den privata IP-adressen.

    ![Privat IP-adress](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Steg 2: Skapa ett valv och välj ett replikeringsmål

1. I Azure-portalen väljer du **Skapa en säkerhetskopiering** > **av** > resurser**och återställning av webbplats**.
2. I **Namn** anger du ett eget namn som identifierar valvet. 
3. Skapa eller välj en resursgrupp i **resursgruppen.** Vi använder **contosoRG.**
4. Ange Azure-regionen i **Plats.** Använder vi **Europa, västra**.
5. Om du snabbt vill komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Skapa ett nytt valv](./media/azure-stack-site-recovery/new-vault-settings.png)

   Det nya valvet visas på **instrumentpanelen** > **Alla resurser**och på sidan för de viktigaste valven för **Återställningstjänster.**

### <a name="select-a-replication-goal"></a>Väljer ett replikeringsmål

1. I **Recovery Services-valv** > ange ett valvnamn. Vi använder **ContosoVMVault.**
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina maskiner**väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade**väljer du Inte **virtualiserad/Annan**. Välj sedan **OK**.

    ![Skyddsmål](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Steg 3: Konfigurera källmiljön

Konfigurera konfigurationsserverdatorn, registrera den i valvet och upptäck datorer som du vill replikera.

1. Klicka på **Förbered infrastrukturkälla** > **Source**.
2. I **Förbered källa** klickar du på **+Konfigurationsserver**.

    ![Konfigurera källan](./media/azure-stack-site-recovery/plus-config-srv.png)

3. Kontrollera att Configuration **Server** visas i **servertyp**i **Lägg till**server .
5. Hämta installationsfilen för installationsprogrammet för enhetlig installation för webbplatsåterställning.
6. Ladda ned valvregistreringsnyckeln. Du behöver registreringsnyckeln när du kör Enhetlig installation. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Köra enhetlig installation för enhetlig Azure-webbplatsåterställning

Om du vill installera och registrera konfigurationsservern gör du en RDP-anslutning till den virtuella dator som du vill använda för konfigurationsservern och kör Enhetlig installation.

Innan du börjar kontrollerar du att klockan [är synkroniserad med en tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) på den virtuella datorn innan du börjar. Installationen misslyckas om tiden är mer än fem minuter bort lokal tid.

Installera nu konfigurationsservern:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurationsservern kan också installeras från kommandoraden. [Läs mer](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart väljer du**Uppdatera*****servernamn*** > för **konfigurationsservrar.** > 

## <a name="step-4-set-up-the-target-environment"></a>Steg 4: Ställ in målmiljön

Välj och kontrollera målresurserna.

1. Välj den Azure-prenumeration som du vill använda i Förbereda > **infrastrukturmål.** **Prepare infrastructure**
2. Ange måldistributionsmodellen.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. Om den inte hittar dem måste du skapa minst ett lagringskonto och ett virtuellt nätverk för att kunna slutföra guiden.


## <a name="step-5-enable-replication"></a>Steg 5: Aktivera replikering

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Klicka på Förbered inställningar för > **infrastrukturreplikering**. **Prepare Infrastructure**
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO).
    - Återställningspunkter för replikerade data skapas i enlighet med den inställda tiden.
    - Den här inställningen påverkar inte replikering, som är kontinuerlig. Det utfärdar helt enkelt en avisering om tröskelgränsen nås utan att en återställningspunkt skapas.
4. Ange hur länge varje återställningspunkt behålls i **återställningspunkt.** Replikerade virtuella datorer kan återställas till valfri punkt i det angivna tidsfönstret.
5. I **Appkonsekvent ögonblicksbildfrekvens**anger du hur ofta programkonsekventa ögonblicksbilder skapas.

    - En appkonsekvent ögonblicksbild är en ögonblicksbild av appdata i den virtuella datorn.
    - Vss (Volume Shadow Copy Service) säkerställer att appar på den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.
6. Välj **OK** för att skapa principen.


### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Du kan hoppa över det här steget nu. I listrutan **Distributionsplanering** klickar du på **Ja, jag har gjort det**.



### <a name="enable-replication"></a>Aktivera replikering

Kontrollera att du har slutfört alla uppgifter i [steg 1: Förbered maskin](#step-1-prepare-azure-stack-vms). Aktivera sedan replikering enligt följande:

1. Välj **Replikera programkälla** > **Source**.
2. I **Källa** väljer du konfigurationsservern.
3. I **Maskintyp**väljer du **Fysiska datorer**.
4. Välj processerver (konfigurationsserver). Klicka sedan på **OK**.
5. I **Mål**väljer du den prenumeration och resursgrupp där du vill skapa de virtuella datorerna efter redundans. Välj den distributionsmodell som du vill använda för de misslyckade virtuella datorerna.
6. Välj det Azure-lagringskonto där du vill lagra replikerade data.
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket separat för varje dator.
9. Klicka på **+Fysisk dator**i Fysiska **datorer.** Ange namn, IP-adress och OS-typ för varje dator som du vill replikera.

    - Använd maskinens interna IP-adress.
    - Om du anger den offentliga IP-adressen kanske replikeringen inte fungerar som förväntat.

10. I **Egenskaper** > **Konfigurera egenskaper**väljer du det konto som processservern ska använda för att automatiskt installera Mobilitetstjänsten på datorn.
11. Kontrollera att rätt replikeringsprincip är markerad i **Replikeringsinställningar** > Konfigurera**replikeringsinställningar.**
12. Klicka på **Aktivera replikering**.
13. Spåra förloppet för jobbet **Aktivera skydd** i**Jobs** > **Jobbplatsåterställningsjobb**för **inställningar.** >  När jobbet **Slutför skydd** körs är datorn klar för redundans.

> [!NOTE]
> Site Recovery installerar mobilitetstjänsten när replikering är aktiverad för en virtuell dator.
> 
> Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.
> 
> Om du vill övervaka virtuella datorer som du lägger till kontrollerar du den senast identifierade tiden för virtuella datorer i **konfigurationsservrar** > **senaste kontakt vid**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (välj den inte) och väljer **Uppdatera**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Steg 6: Kör en borr för haveriberedskap

Du kör en testväxling till Azure för att se till att allt fungerar som förväntat. Den här redundansen påverkar inte produktionsmiljön.

### <a name="verify-machine-properties"></a>Verifiera maskinegenskaper

Innan du kör en testväxling kontrollerar du datorns egenskaper och ser till att de uppfyller [Azure-kraven](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). Du kan visa och ändra egenskaper på följande sätt:

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.
2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. Ändra inställningarna efter behov i **Beräkning och Nätverk.**

    - Du kan ändra Azure VM-namn, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md)och hanterade diskinställningar.
    - Du kan också visa och ändra nätverksinställningar. Dessa inkluderar det nätverk/undernät som Den virtuella Azure-datorn är ansluten till efter redundans och IP-adressen som ska tilldelas den virtuella datorn.
1. Visa information om operativsystemet och datadiskarna på den virtuella datorn i **Diskar.**
   

### <a name="run-a-test-failover"></a>Köra ett redundanstest

När du kör ett redundanstest händer följande:

1. En kravkontroll körs för att säkerställa att alla de villkor som krävs för redundans är på plats.
2. Redundans bearbetar data med den angivna återställningspunkten:
    - **Senast bearbetad:** Datorn växlar inte över till den senaste återställningspunkten som bearbetas av Site Recovery. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt RTO (mål för återställningstid).
    - **Senaste appkonsekvent:** Datorn växlar inte över till den senaste appkonsekventa återställningspunkten.
    - **Anpassad:** Välj den återställningspunkt som används för redundans.

3. En Virtuell Azure skapas med hjälp av bearbetade data.
4. Test redundans kan automatiskt rensa Azure virtuella datorer som skapats under övningen.

Kör en testväxling för en virtuell dator enligt följande:

1. Klicka på den virtuella datorn > **+Test Redundans**i **Inställningar** > **replikerade objekt.**
2. För den här genomgången väljer vi att använda den **senaste bearbetade återställningspunkten.** 
3. Välj målet Azure-nätverk i **Test Redundans.**
4. Starta redundansväxlingen genom att klicka på **OK**.
5. Spåra förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** **Jobs** >i**jobbjobben** > **Platsåterställning**av *valvnamn.* > 
6. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, ansluten till rätt nätverk och körs.
7. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure. [Läs mer](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. Vill du ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på den virtuella datorn. Spara alla observationer som är associerade med testundansen i **Anteckningar.**

## <a name="fail-over-and-fail-back"></a>Redundans och återställning

När du har konfigurerat replikering och kört en övning för att se till att allt fungerar kan du växla datorer över till Azure efter behov.

Innan du kör en redundans, om du vill ansluta till datorn i Azure efter redundansen, [förbered dig sedan för att ansluta](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) innan du börjar.

Kör sedan en redundans enligt följande:


1. Klicka på datorn > **redundans**i **Inställningar** > **replikerade objekt.**
2. Välj den återställningspunkt som du vill använda.
3. Välj målet Azure-nätverk i **Test Redundans.**
4. Välj **Stäng datorn innan du påbörjar redundans**. Med den här inställningen försöker Site Recovery stänga av källdatorn innan du startar redundansen. Redundansen fortsätter dock även om avstängningen misslyckas. 
5. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet för redundans på sidan **Jobb**.
6. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Om du är beredd att ansluta efter redundans kontrollerar du att den virtuella datorn har rätt storlek, är ansluten till rätt nätverk och körs.
7. När du har verifierat den virtuella datorn klickar du på **Commit** för att slutföra redundansen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> Avbryt inte en pågående redundansväxling: Innan redundansen startas så stoppas replikeringen av den virtuella datorn. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.


### <a name="fail-back-to-azure-stack"></a>Växla tillbaka till Azure Stack

När din primära webbplats är igång igen kan du växla tillbaka från Azure till Azure Stack. För att göra detta måste du hämta Azure VM VHD och ladda upp den till Azure Stack.

1. Stäng av den virtuella azure-datorn så att den virtuella hårddisken kan hämtas. 
2. Om du vill börja hämta den virtuella hårddisken installerar du [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
3. Navigera till den virtuella datorn i Azure Portal (med vm-namnet).
4. Klicka på disknamnet i **Diskar**och samla in inställningar.

    - Som ett exempel kan VHD URI https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd som används i vårt test: delas upp för att få följande indataparametrar som används för att hämta den virtuella hårddisken.
        - Lagringskonto: 502055westcentralus
        - Behållare: wahv9b8d2ceb284fb59287
        - VHD Namn: kopieras-3676553984.vhd

5. Nu använder Azure Storage Explorer för att ladda ner den virtuella hårddisken.
6. Ladda upp den virtuella hårddisken till Azure Stack med [dessa steg](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-disks-to-a-vm).
7. I den befintliga virtuella datorn eller den nya virtuella datorn ansluter du de uppladdade virtuella hårddiskarna.
8. Kontrollera att OS-disken är korrekt och starta den virtuella datorn.


I detta skede failback är klar.


## <a name="conclusion"></a>Slutsats

I den här artikeln replikerade vi virtuella Azure Stack-datorer till Azure. Med replikering på plats körde vi en haveriberedskapsövning för att se till att redundansen till Azure fungerade som förväntat. Artikeln innehöll också steg för att köra en fullständig redundans till Azure och misslyckas tillbaka till Azure Stack.

## <a name="next-steps"></a>Nästa steg

När du har misslyckats tillbaka kan du reprotera den virtuella datorn och börja replikera den till Azure igen För att göra detta kan du upprepa stegen i den här artikeln.

