---
title: Flytta virtuella Azure IaaS-datorer mellan Azure Government och offentliga regioner med Azure Site Recovery-tjänsten | Microsoft Docs
description: Använd Azure Site Recovery för att flytta virtuella Azure IaaS-datorer mellan Azure Government och offentliga regioner.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: fe2620c7a07389b2a86d36420eadd2ef5883f5da
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119643"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Flytta virtuella Azure-datorer mellan Azure Government och offentliga regioner 

Du kanske vill flytta dina virtuella IaaS-datorer mellan Azure Government och offentliga regioner för att öka tillgängligheten för dina befintliga virtuella datorer, förbättra hanterbarhet eller styrning skäl, enligt beskrivningen [här](azure-to-azure-move-overview.md).

Utöver att använda tjänsten [Azure Site Recovery](site-recovery-overview.md) för att hantera och samordna haveriberedskap på lokala datorer och virtuella Azure-datorer i syftet affärskontinuitet och haveriberedskap (BCDR) kan du även använda Site Recovery för att hantera flytt av virtuella Azure-datorer till en sekundär region.       

Den här självstudien Lär dig att flytta virtuella Azure-datorer mellan Azure Government och offentliga regioner med Azure Site Recovery. Samma kan utökas för att flytta virtuella datorer mellan regionpar som inte är inom samma geografiska kluster. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera förutsättningar
> * Förbereda källregionens virtuella datorer
> * Förbereda målregionen
> * Kopiera data till målregionen
> * Testa konfigurationen
> * Utföra flytten
> * Ta bort resurserna från källregionen

> [!IMPORTANT]
> Den här självstudien visar hur du flyttar virtuella Azure-datorer mellan Azure Government och offentliga regioner, eller mellan regioner-par som inte stöds av den vanliga haveriberedskapslösning för virtuella Azure-datorer. Om, din källa och mål-par av regioner är [stöds](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), finns i den här [dokumentet](azure-to-azure-tutorial-migrate.md) för flytten. Om dina krav är att förbättra tillgängligheten genom att flytta virtuella datorer i en tillgänglighetsuppsättning till zonlåsta virtuella datorer i en annan region måste referera till självstudien [här](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Det är inte lämpligt att använda den här metoden för att konfigurera Haveriberedskap mellan stöds inte regionpar som definieras i par Tänk datafördröjning, vilket är viktigt för katastrofåterställning.

## <a name="verify-prerequisites"></a>Kontrollera förutsättningar

> [!NOTE]
> Se till att du förstår de [arkitektur och komponenter](physical-azure-architecture.md) för det här scenariot. Den här arkitekturen används för att flytta virtuella Azure-datorer **genom att behandla de virtuella datorerna som fysiska servrar**.

- Granska [kraven för stöd](vmware-physical-secondary-support-matrix.md) för alla komponenter.
- Se till att de servrar som du vill replikera följer [kraven för Azure virtuella datorer](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Förbereda ett konto för automatisk installation av mobilitetstjänsten på varje server som du vill replikera.

- Observera att när du har fel till målregion i Azure kan du inte kan direkt utföra en växling vid fel till källregionen. Du måste konfigurera replikering igen till målet.

### <a name="verify-azure-account-permissions"></a>Kontrollera behörigheter för Azure-konto

Kontrollera att ditt Azure-konto har behörigheter för replikering av virtuella datorer till Azure.

- Granska den [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) du behöver för att replikera datorer till Azure.
- Kontrollera och ändra [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md) behörigheter. 

### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Konfigurera en målet [Azure-nätverk](../virtual-network/quick-create-portal.md).

- Virtuella Azure-datorer är placerade i det här nätverket när de skapas efter en redundansväxling.
- Nätverket måste finnas i samma region som Recovery Services-valvet


### <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Konfigurera en [Azure storage-konto](../storage/common/storage-quickstart-create-account.md).

- Site Recovery replikerar lokala datorer till Azure storage. Virtuella Azure-datorer skapas från lagringen efter redundansväxlingen.
- Lagringskontot måste finnas i samma region som Recovery Services-valvet.


## <a name="prepare-the-source-vms"></a>Förbereda virtuella källdatorer

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje server som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för servern. Om du vill installera automatiskt, måste du förbereda ett konto som Site Recovery använder för att ansluta till servern.

- Du kan använda en domän eller lokalt konto
- För Windows-datorer om du inte använder ett domänkonto, inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn. Att göra detta i registret under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägger du till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet 1.
- Om du vill lägga till registerposten för att inaktivera inställningen från en CLI, skriver du:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- För Linux, måste kontot vara rot på Linux-källservern.


## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen för haveriberedskap. Kontakta supporten och aktivera den kvot som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Om du använder Site Recovery till att kopiera data till målet väljer det samma storlek eller närmast möjliga storlek för den virtuella måldatorn.

3. Se till att du skapar en målresurs för varje komponent som identifieras i källans nätverkslayout. Det är viktigt att se till att, publicera övergripande till målregionen, dina virtuella datorer har alla funktioner och funktioner som fanns i källan.

    > [!NOTE]
    > Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk när du aktiverar replikering för den virtuella källdatorn. Du kan också skapa ett nätverk i förväg och tilldela det till den virtuella datorn i arbetsflödet där du aktiverar replikering. För vissa resurser måste du dock skapa dem manuellt i målregionen, vilket beskrivs nedan.

     I följande dokument visas hur du skapar de vanligaste nätverksresurserna, baserat på den virtuella källdatorkonfigurationen.

    - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Offentlig IP-adress](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Alla andra nätverkskomponenter beskrivs i nätverkets [dokumentation](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Du kan [skapa ett icke-produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manuellt i målregionen om du vill testa konfigurationen innan du slutgiltigt växlar över till målregionen. Detta ger minimala störningar i produktionen och rekommenderas därför.

## <a name="copy-data-to-the-target-region"></a>Kopiera data till målregionen
I stegen nedan får du hjälp att kopiera data till målregionen med hjälp av Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Hanteringsverktyg** > **Backup och Site Recovery**.
3. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har fler än en prenumeration väljer du den rätta.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. I Recovery Services-valv klickar du på **översikt** > **ConsotoVMVault** > **+ replikera**
7. Välj **till Azure** > **inte virtualiserad/övrigt**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Ställ in konfigurationsservern för att identifiera virtuella datorer.


Ställ in konfigurationsservern, registrera den i valvet och identifiera virtuella datorer.

1. Klicka på **Webbplatsåterställning** > **förbereda infrastrukturen** > **källa**.
2. Om du inte har en konfigurationsserver, klickar du på **+ konfigurationsserver**.
3. I **Lägg till Server**, kontrollerar du att **konfigurationsservern** visas i **servertyp**.
4. Ladda ned installationsfilen enhetliga installationsprogrammet för Site Recovery.
5. Ladda ned valvregistreringsnyckeln. Du behöver den när du kör enhetliga installationsprogrammet. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Konfigurera källan](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrera konfigurationsservern i valvet

Gör följande innan du börjar: 

#### <a name="verify-time-accuracy"></a>Kontrollera tid noggrannhet
På configuration server-datorn, se till att systemklockan är synkroniserad med en [tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Det måste matcha. Om det är 15 minuter framför eller bakom, kan installationen misslyckas.

#### <a name="verify-connectivity"></a>Verifiera anslutningen
Kontrollera att datorn kan komma åt dessa URL: er baserat på miljön: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP-adressbaserade brandväggsregler ska tillåta kommunikation till alla de URL: er i Azure som listas ovan över HTTPS-port (443). Du rekommenderas att URL-filtrering ska utföras för att förenkla och begränsa IP-intervall.

- **Extern IP-adresser** -Tillåt den [Azure Datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653), och HTTPS-port (443). Tillåt IP-adressintervall för Azure-regionen för din prenumeration med stöd för AAD, säkerhetskopiering, replikering och lagring URL: er.  
- **Offentliga IP-adresser** -Tillåt den [IP-intervall i Azure Government Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=57063), och port HTTPS (443) för alla USGov regioner (Virginia, Texas, Arizona och Iowa) för AAD, säkerhetskopiering, replikering och lagring URL: er.  

#### <a name="run-setup"></a>Kör installationsprogrammet
Kör enhetliga installationsprogrammet som en lokal administratör, installera konfigurationsservern. Processervern och huvudmålservern installeras som standard på konfigurationsservern.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

När registreringen är klar visas konfigurationsservern på den **inställningar** > **servrar** i valvet.

### <a name="configure-target-settings-for-replication"></a>Konfigurera Målinställningar för replikering

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange måldistributionsmodell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Mål](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Om du vill skapa en ny replikeringsprincip klickar du på **Site Recovery-infrastruktur** > **Replikeringsprinciper** > **+Replikeringsprincip**.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO). Det här värdet anger hur ofta data återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmars kvarhållning stöds för datorer replikerade till premiumlagring och 72 timmar för standardlagring.
5. I **appkonsekvent ögonblicksbildsfrekvens**, ange hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Klicka på **OK** för att skapa principen.

    ![Replikeringsprincip](./media/physical-azure-disaster-recovery/replication-policy.png)


Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen är till exempel **rep-policy** sedan en återställningsprincip **rep-policy-failback** har skapats. Den här principen används inte förrän du initierar en återställning efter fel från Azure.

### <a name="enable-replication"></a>Aktivera replikering

- Site Recovery installerar mobilitetstjänsten när replikering har aktiverats.
- När du aktiverar replikering för en server kan det ta 15 minuter eller längre för att ändringarna ska träda i kraft och visas på portalen.

1. Klicka på **Replikera program** > **Källa**.
2. I **Källa** väljer du konfigurationsservern.
3. I **datortyp**väljer **fysiska datorer**.
4. Välj processerver (konfigurationsserver). Klicka sedan på **OK**.
5. I **Target**, Välj prenumerationen och resursgrupp där du vill skapa virtuella Azure-datorer efter redundansväxling. Välj den distributionsmodell som du vill använda i Azure (klassisk eller resource Manager).
6. Välj Azure-lagringskonto som du vill använda när du replikerar data. 
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. 
9. I **fysiska datorer**, och klicka på **+ fysisk dator**. Ange namn och IP-adress. Välj operativsystemet på den dator du vill replikera. Det tar några minuter för servrarna som ska identifieras och visas. 

   > [!WARNING]
   > Du måste ange IP-adressen för Azure VM som du tänker flytta

10. I **egenskaper** > **konfigurera egenskaper**, väljer du det konto som ska användas av processervern för att automatiskt installera mobilitetstjänsten på datorn.
11. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts. 
12. Klicka på **Aktivera replikering**. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.


Om du vill övervaka servrar som du lägger till, kan du kontrollera senast identifierades för dem i **Konfigurationsservrar** > **senaste kontakt på**. Om du vill lägga till datorer utan att behöva vänta en schemalagda identifieringstiden, markerar du konfigurationsservern (Klicka inte på den), och klicka på **uppdatera**.

## <a name="test-the-configuration"></a>Testa konfigurationen


1. Navigera till valvet. I **Inställningar** > **Replikerade objekt** klickar du på den virtuella dator som du vill flytta till målregionen och klickar sedan på ikonen **+ Testa redundans**.
2. I **Testa redundans** väljer du en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstid (RTO)
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassat**: Välj en annan återställningspunkt.

3. Välj det virtuella Azure-målnätverk dit du vill flytta de virtuella Azure-datorerna för att testa konfigurationen. 

   > [!IMPORTANT]
   > Vi rekommenderar att du använder en separat Azure VM-nätverk för redundanstestet och inte produktionsnätverket dit du vill flytta dina virtuella datorer så småningom som skapades när du aktiverade replikeringen.

4. När du vill börja testa flytten klickar du på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** > **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella dator som skapades i samband med flyttestet klickar du på **Rensa redundanstestning** i det replikerade objektet. I **Kommentarer** skriver du ned och sparar eventuella observationer från testet.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Utför flytten till målregionen och bekräfta.

1. Gå till valvet. I **Inställningar** > **Replikerade objekt** klickar du på den virtuella datorn och sedan på **Redundans**.
2. I **Redundans** väljer du **Senaste**. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**. 
4. När jobbet har slutförts kan du kontrollera att den virtuella datorn visas i Azure-målregionen som förväntat.
5. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar flytten till målregionen. Vänta tills incheckningsjobbet har slutförts.

## <a name="discard-the-resource-in-the-source-region"></a>Ta bort resursen från källregionen 

- Gå till den virtuella datorn.  Klicka på **Inaktivera replikering**.  Detta stoppar processen att kopiera data för den virtuella datorn.  

   > [!IMPORTANT]
   > Det är viktigt att du utför det här steget så att du inte debiteras för ASR-replikering.

Fortsätt med nästa uppsättning steg om du inte har för avsikt att återanvända några av källresurserna.

1. Ta bort alla relevanta nätverksresurser från den källregion som ingick i steg 4 i [Förbereda de virtuella källdatorerna](#prepare-the-source-vms) 
2. Ta bort motsvarande lagringskonto från källregionen.



## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en virtuell Azure-dator till en annan Azure-region. Nu kan du konfigurera haveriberedskap för de flyttade virtuella datorerna.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)
