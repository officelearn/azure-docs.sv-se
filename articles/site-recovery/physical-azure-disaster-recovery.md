---
title: Konfigurera katastrofåterställning till Azure för fysiska lokala servrar med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap till Azure för lokala Windows- och Linux-servrar, med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 173423c1a578500a990d6a7b43017d06ea96f6e7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704908"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Konfigurera katastrofåterställning till Azure för lokala fysiska servrar

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien visar hur du konfigurerar haveriberedskap för lokala fysiska Windows och Linux-servrar till Azure. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ställ in krav för Azure och lokalt
> * Skapa ett Recovery Services-valv för Site Recovery 
> * Ställa in källa och mål replikering miljöer
> * Skapa replikeringsprincip
> * Aktivera replikering för en server

[Granska arkitekturen](concepts-hyper-v-to-azure-architecture.md) för detta haveriberedskapsscenario.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

- Se till att du förstår de [arkitektur och komponenter](physical-azure-architecture.md) för det här scenariot.
- Granska [kraven för stöd](vmware-physical-secondary-support-matrix.md) för alla komponenter.
- Se till att de servrar som du vill replikera följer [kraven för Azure virtuella datorer](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Förbereda Azure. Du behöver en Azure-prenumeration, Azure-nätverk och ett lagringskonto.
- Förbereda ett konto för automatisk installation av mobilitetstjänsten på varje server som du vill replikera.

Innan du börjar kontrollerar du att:

- Efter redundansväxlingen till Azure, att inte fysiska servrar växlas tillbaka till lokala fysiska datorer. Du kan bara växla tillbaka till virtuella VMware-datorer. 
- Den här självstudiekursen ställer in fysisk server haveriberedskap till Azure med de enklaste inställningarna. Om du vill lära dig om andra alternativ, Läs igenom våra guider för hur man:
    - Konfigurera den [replikeringskälla](physical-azure-set-up-source.md), inklusive Site Recovery konfigurationsserver.
    - Konfigurera [replikeringsmålet](physical-azure-set-up-target.md).
    - Konfigurera en [replikeringsprincip](vmware-azure-set-up-replication.md) och [aktivera replikering](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Konfigurera ett Azure-konto

Skaffa en Microsoft [Azure-konto](http://azure.microsoft.com/).

- Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
- Lär dig mer om [priserna för Site Recovery](site-recovery-faq.md#pricing), och få [prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ta reda på vilka [regioner stöds](https://azure.microsoft.com/pricing/details/site-recovery/) för Site Recovery.

### <a name="verify-azure-account-permissions"></a>Kontrollera behörigheter för Azure-konto

Kontrollera att ditt Azure-konto har behörigheter för replikering av virtuella datorer till Azure.

- Granska den [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) du behöver för att replikera datorer till Azure.
- Kontrollera och ändra [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md) behörigheter. 



### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Konfigurera en [Azure-nätverk](../virtual-network/quick-create-portal.md).

- Virtuella Azure-datorer är placerade i det här nätverket när de skapas efter en redundansväxling.
- Nätverket måste finnas i samma region som Recovery Services-valvet


## <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Konfigurera en [Azure storage-konto](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Site Recovery replikerar lokala datorer till Azure storage. Virtuella Azure-datorer skapas från lagringen efter redundansväxlingen.
- Lagringskontot måste finnas i samma region som Recovery Services-valvet.
- Lagringskontot kan vara standard eller [premium](../virtual-machines/windows/premium-storage.md).
- Om du har konfigurerat ett premium-konto, måste du också ett ytterligare konto av standardtyp för loggdata.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje server som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för servern. Om du vill installera automatiskt, måste du förbereda ett konto som Site Recovery använder för att ansluta till servern.

- Du kan använda en domän eller lokalt konto
- För Windows-datorer om du inte använder ett domänkonto, inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn. Att göra detta i registret under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägger du till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet 1.
- Om du vill lägga till registerposten för att inaktivera inställningen från en CLI, skriver du:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- För Linux, måste kontot vara rot på Linux-källservern.


## <a name="create-a-vault"></a>Skapa ett valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Välj ett skyddsmål

Välj vad att replikera och för att replikera den till.

1. Klicka på **Recovery Services-valv** > valv.
2. På Resurs-menyn klickar du på **Site Recovery** > **Förbered infrastrukturen** > **Skyddsmål**.
3. I **skyddsmål**väljer **till Azure** > **inte virtualiserad/övrigt**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

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

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange måldistributionsmodell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Mål](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Klicka för att skapa en ny replikeringsprincip **Site Recovery-infrastruktur** > **replikeringsprinciper** > **+ replikeringsprincip**.
2. I **skapa replikeringsprincip**, ange ett principnamn.
3. I **tröskelvärde för Replikeringspunktmål**, ange objektiva (RPO) återställningspunktgränsen. Det här värdet anger hur ofta data återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
4. I **kvarhållning av återställningspunkt**, ange hur lång tid (i timmar) kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmar kvarhållning stöds för datorer som har replikerats till premiumlagring och 72 timmar för standardlagring.
5. I **appkonsekvent ögonblicksbildsfrekvens**, ange hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Klicka på **OK** för att skapa principen.

    ![Replikeringsprincip](./media/physical-azure-disaster-recovery/replication-policy.png)


Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen är till exempel **rep-policy** sedan en återställningsprincip **rep-policy-failback** har skapats. Den här principen används inte förrän du initierar en återställning efter fel från Azure.

## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering för varje server.

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
10. I **egenskaper** > **konfigurera egenskaper**, väljer du det konto som ska användas av processervern för att automatiskt installera mobilitetstjänsten på datorn.
11. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts. 
12. Klicka på **Aktivera replikering**. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.


Om du vill övervaka servrar som du lägger till, kan du kontrollera senast identifierades för dem i **Konfigurationsservrar** > **senaste kontakt på**. Om du vill lägga till datorer utan att behöva vänta en schemalagda identifieringstiden, markerar du konfigurationsservern (Klicka inte på den), och klicka på **uppdatera**.

## <a name="next-steps"></a>Nästa steg

[Kör ett programåterställningstest](tutorial-dr-drill-azure.md).
