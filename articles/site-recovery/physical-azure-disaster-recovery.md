---
title: Konfigurera haveriberedskap av fysiska lokala servrar med Azure Site Recovery
description: Lär dig hur du konfigurerar haveriberedskap till Azure för lokala Windows- och Linux-servrar med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 2f92c2b800c6d30cc5f365e6d24925a70d3db55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257932"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Konfigurera haveriberedskap på Azure för lokala fysiska servrar

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien visar hur du konfigurerar haveriberedskap för lokala fysiska Windows- och Linux-servrar till Azure. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera Azure och lokala förutsättningar
> * Skapa ett Recovery Services-valv för Site Recovery 
> * Ställa in käll- och målreplikeringsmiljöer
> * Skapa replikeringsprincip
> * Aktivera replikering för en server

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Se till att du förstår [arkitekturen och komponenterna](physical-azure-architecture.md) för det här scenariot.
- Granska [kraven för stöd](vmware-physical-secondary-support-matrix.md) för alla komponenter.
- Kontrollera att de servrar som du vill replikera uppfyller [Kraven på Azure VM](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Förbered Azure. Du behöver en Azure-prenumeration, ett virtuellt Azure-nätverk och ett lagringskonto.
- Förbered ett konto för automatisk installation av mobilitetstjänsten på varje server som du vill replikera.

Innan du börjar bör du tänka på följande:

- Efter redundans till Azure kan fysiska servrar inte återställas till lokala fysiska datorer. Du kan bara växla tillbaka till virtuella datorer med VMware. 
- Den här självstudien konfigurerar fysisk serverkatastrofåterställning till Azure med de enklaste inställningarna. Om du vill lära dig mer om andra alternativ kan du läsa igenom våra guider för hur du gör:
    - Konfigurera [replikeringskällan](physical-azure-set-up-source.md), inklusive konfigurationsservern för platsåterställning.
    - Konfigurera [replikeringsmålet](physical-azure-set-up-target.md).
    - Konfigurera en [replikeringsprincip](vmware-azure-set-up-replication.md) och [aktivera replikering](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Konfigurera ett Azure-konto

Skaffa ett Microsoft [Azure-konto](https://azure.microsoft.com/).

- Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
- Läs mer om [priser för webbplatsåterställning](site-recovery-faq.md#pricing)och få [prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ta reda på vilka regioner som [stöds](https://azure.microsoft.com/pricing/details/site-recovery/) för Webbplatsåterställning.

### <a name="verify-azure-account-permissions"></a>Verifiera Azure-kontobehörigheter

Kontrollera att ditt Azure-konto har behörigheter för replikering av virtuella datorer till Azure.

- Granska de [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) du behöver för att replikera datorer till Azure.
- Verifiera och ändra [rollbaserade](../role-based-access-control/role-assignments-portal.md) åtkomstbehörigheter. 



### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Konfigurera ett [Azure-nätverk](../virtual-network/quick-create-portal.md).

- Virtuella Azure-datorer placeras i det här nätverket när de skapas efter redundans.
- Nätverket ska finnas i samma region som Recovery Services-valvet


## <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Konfigurera ett [Azure-lagringskonto](../storage/common/storage-account-create.md).

- Site Recovery replikerar lokala datorer till Azure-lagring. Virtuella Azure-datorer skapas från lagringen när redundans inträffar.
- Lagringskontot måste finnas i samma region som Recovery Services-valvet.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje server som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för servern. Om du vill installera automatiskt måste du förbereda ett konto som Site Recovery använder för att komma åt servern.

- Du kan använda en domän eller ett lokalt konto
- Om du inte använder ett domänkonto för Windows-datorer inaktiverar du kontrollen Fjärråtkomst på den lokala datorn. Det gör du i registret under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägger du till DWORD-posten **LocalAccountTokenFilterPolicy**med värdet 1.
- Om du vill lägga till registerposten för att inaktivera inställningen från en CLI skriver du:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- För Linux bör kontot vara rot på källan Linux-server.


## <a name="create-a-vault"></a>Skapa ett valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Välj ett skyddsmål

Välj vad du vill replikera och replikera det till.

1. Klicka på **Recovery Services-valv** > valv.
2. Klicka på Målet för förbereda > **infrastrukturskydd**för **webbplatsåterställning** > **Prepare Infrastructure**på Resurs-menyn.
3. I **Skyddsmål**väljer du **Till Azure** > **Inte virtualiserad/Annan**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Konfigurera konfigurationsservern, registrera den i valvet och upptäck virtuella datorer.

1. Klicka på > **Infrastrukturkälla för**förbereda infrastruktur för > **platsåterställning**. **Site Recovery**
2. Om du inte har någon konfigurationsserver klickar du på **+Konfigurationsserver**.
3. Kontrollera att Configuration **Server** visas i **servertyp**i **Lägg till**server .
4. Hämta installationsfilen för installationsprogrammet för enhetlig installation för webbplatsåterställning.
5. Ladda ned valvregistreringsnyckeln. Du behöver detta när du kör Unified Setup. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Konfigurera källan](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrera konfigurationsservern i valvet

Gör följande innan du börjar: 

#### <a name="verify-time-accuracy"></a>Verifiera tidsnoggrannhet
Kontrollera att systemklockan är synkroniserad med en [tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)på konfigurationsserverdatorn . Det borde stämma. Om det är 15 minuter framför eller bakom kan installationen misslyckas.

#### <a name="verify-connectivity"></a>Verifiera anslutning
Kontrollera att datorn kan komma åt dessa webbadresser baserat på din miljö: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP-adressbaserade brandväggsregler bör tillåta kommunikation till alla Azure-url:er som visas ovan via HTTPS-port (443). För att förenkla och begränsa IP-intervallen rekommenderar vi att URL-filtrering görs.

- **Kommersiella IP-adresser** – Tillåt [Azure Datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653)och HTTPS-porten (443). Tillåt IP-adressintervall för Azure-regionen för din prenumeration för att stödja webbadresserna AAD, Backup, Replication och Lagring.  
- **Myndighets-IP** - Tillåt [azure government datacenter IP-intervall](https://www.microsoft.com/en-us/download/details.aspx?id=57063)och HTTPS-porten (443) för alla USGov-regioner (Virginia, Texas, Arizona och Iowa) för att stödja AAD-, säkerhetskopierings-, replikerings- och lagringsadresser.  

#### <a name="run-setup"></a>Kör installationsprogrammet
Installera konfigurationsservern genom att köra enhetliga installationsprogrammet som lokal administratör. Processservern och huvudmålservern installeras också som standard på konfigurationsservern.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

När registreringen är klar visas konfigurationsservern på sidan **Inställningars** > **servrar** i valvet.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastrukturmål** > **Target**och välj den Azure-prenumeration som du vill använda.
2. Ange måldistributionsmodellen.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Mål](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Om du vill skapa en ny replikeringsprincip klickar du på > **Replikeringsprinciper för** > replikering av **platsåterställning****+Replikeringsprincip**.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO). Det här värdet anger hur ofta dataåterställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmars kvarhållning stöds för datorer replikerade till premiumlagring och 72 timmar för standardlagring.
5. I **Appkonsekvent ögonblicksbildfrekvens**anger du hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Klicka på **OK** för att skapa principen.

    ![Replikeringsprincip](./media/physical-azure-disaster-recovery/replication-policy.png)


Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen till exempel är **rep-princip** skapas en återställningsprincip för återställning av **principåterfrisör.** Den här principen används inte förrän du initierar en återställning efter fel från Azure.

## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering för varje server.

- Site Recovery installerar mobilitetstjänsten när replikering är aktiverad.
- När du aktiverar replikering för en server kan det ta 15 minuter eller längre innan ändringarna börjar gälla och visas i portalen.

1. Klicka på **Replikera programkälla** > **Source**.
2. I **Källa** väljer du konfigurationsservern.
3. I **Maskintyp**väljer du **Fysiska datorer**.
4. Välj processserver (konfigurationsservern). Klicka sedan på **OK**.
5. I **Mål**väljer du den prenumeration och resursgrupp där du vill skapa virtuella Azure-datorer efter redundans. Välj den distributionsmodell som du vill använda i Azure (klassisk hantering eller resurshantering).
6. Välj Azure-lagringskonto som du vill använda när du replikerar data. 
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer**om du vill använda nätverksinställningen på alla datorer som du väljer för skydd. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. 
9. I **Fysiska datorer**och klicka på **+Fysisk dator**. Ange namn och IP-adress. Välj operativsystemet för den dator som du vill replikera. Det tar några minuter för servrarna att upptäckas och listas. 
10. I **Egenskaper** > **Konfigurera egenskaper**väljer du det konto som ska användas av processservern för att automatiskt installera mobilitetstjänsten på datorn.
11. Kontrollera att rätt replikeringsprincip är markerad i **Replikeringsinställningar** > Konfigurera**replikeringsinställningar.** 
12. Klicka på **Aktivera replikering**. Du kan spåra förloppet för jobbet **Aktivera skydd** i**Jobs** > **Jobbplatsåterställningsjobb**för **inställningar.** >  När jobbet **Slutför skydd** har körts är datorn redo för redundans.


Om du vill övervaka servrar som du lägger till kan du kontrollera den senast identifierade tiden för dem i **konfigurationsservrar** > **senaste kontakt vid**. Om du vill lägga till datorer utan att vänta på en schemalagd identifieringstid markerar du konfigurationsservern (klicka inte på den) och klickar på **Uppdatera**.

## <a name="next-steps"></a>Nästa steg

[Kör en haveriberedskapsövning](tutorial-dr-drill-azure.md).
