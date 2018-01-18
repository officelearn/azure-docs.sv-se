---
title: "Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer med Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du ställer in katastrofåterställning till Azure för lokala virtuella VMware-datorer med Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8acc8deff8b635c97e8722d65a728aebf0e49bb3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer

Den här kursen visar hur du ställer in katastrofåterställning till Azure för lokala virtuella VMware-datorer kör Windows. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ange källa för replikering och målet.
> * Konfigurera källmiljön för replikering, inklusive lokal Site Recovery-komponenter och replikering målmiljön.
> * Skapa replikeringsprincip
> * Aktivera replikering för en virtuell dator

Detta är den tredje vägledningen i en serie. Den här kursen förutsätter att du redan har slutfört uppgifterna i de föregående självstudiekurser:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokal VMware](tutorial-prepare-on-premises-vmware.md)

Innan du börjar är det bra att [granska arkitekturen](concepts-vmware-to-azure-architecture.md) för katastrofåterställning.


## <a name="select-a-replication-goal"></a>Välj ett mål för replikering

1. I **Recovery Services-valv**, klicka på valvnamnet, **ContosoVMVault**.
2. I **komma igång**, klicka på Site Recovery. Klicka på **Förbered infrastrukturen**.
3. I **skyddsmål** > **där är dina datorer finns**väljer **lokalt**.
4. I ** där du vill replikera dina datorer Välj **till Azure**.
5. I **är dina datorer virtualiserade**väljer **Ja, med VMware vSphere-Hypervisor**. Klicka sedan på **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Om du vill konfigurera källmiljön behöver du en enskild hög tillgänglighet, lokala datorn till värden lokalt Site Recovery-komponenter. Komponenter omfattar konfigurationsservern, processervern och huvudmålservern.

- Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- Processervern fungerar som en replikerings-gateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Processervern installeras också mobilitetstjänsten på virtuella datorer du vill replikera, och utför automatisk identifiering av lokala virtuella VMware-datorer.
- Huvudmålservern hanterar replikeringsdata vid återställning från Azure.

Om du vill konfigurera konfigurationsservern som en hög tillgänglighet VMware VM hämtar en förberedd OVF-mall och importera mallen till VMware för att skapa den virtuella datorn. När du skapat konfigurationsservern kan registrera du den i valvet. Site Recovery identifierar lokala virtuella VMware-datorer efter registreringen.

### <a name="download-the-vm-template"></a>Hämta mall för virtuell dator

1. I valvet, går du till **Förbered infrastrukturen** > **källa**.
2. I **Förbered källa**, klickar du på **+ konfigurationsservern**.
3. I **Lägg till Server**, kontrollera att **konfigurationsservern för VMware** visas i **servertyp**.
4. Hämta Open Virtualization Format OVF ()-mall för konfigurationsservern.

  > [!TIP]
  Den senaste versionen av configuration server-mall kan hämtas direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importera VMware-mallen

1. Logga in på VMware vCenter server eller vSphere ESXi värden med VMWare vSphere klienten.
2. På den **filen** väljer du **distribuera OVF mallen**, för att starta guiden Distribuera OVF-mall.  

     ![OVF-mall](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. I **Välj källa**, ange platsen för den hämtade OVF.
4. I **information**, klickar du på **nästa**.
5. I **Välj namn och mappen**, och **Select configuration**, accepterar du standardinställningarna.
6. I **Välj lagring**, för bästa prestanda väljer **tjock etablera gärna nollställs** i **väljer virtuella diskformatet**.
4. Acceptera standardinställningarna i resten av sidorna i guiden.
5. I **redo att slutföra**:
  - Om du vill konfigurera den virtuella datorn med standardinställningarna, Välj **slå på strömmen efter distributionen** > **Slutför**.
  - Ta bort om du vill lägga till ytterligare ett nätverksgränssnitt **slå på strömmen efter distributionen**, och välj sedan **Slutför**. Mallen configuration server distribueras med ett enda nätverkskort som standard, men du kan lägga till ytterligare nätverkskort efter distributionen.

  
## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till en extra NIC konfigurationsservern kan du göra det innan du registrerar servern i valvet. Att lägga till fler nätverkskort stöds inte efter registreringen.

1. I vSphere klienten lagret, högerklicka på den virtuella datorn och välj **redigera inställningar för**.
2. I **maskinvara**, klickar du på **Lägg till** > **Ethernet-nätverkskort**. Klicka sedan på **Nästa**.
3. Välj och typ av nätverkskort och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen, Välj **ansluta vid effekt på**. Klicka på **nästa** > **Slutför**, och klicka sedan på **OK**.


## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

1. Aktivera den virtuella datorn från konsolen VMWare vSphere-klienten.
2. Den virtuella datorn startar i en Windows Server 2016-installationsproceduren. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar, inloggning till den virtuella datorn som administratör.
4. Startar för första gången du loggar in Azure Site Recovery-konfigurationsverktyget.
5. Ange ett namn som används för att registrera konfigurationsservern med Site Recovery. Klicka sedan på **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats, klickar du på **logga in**, för att logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till valvet som du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar om.
8. Logga in på datorn igen. Guiden Konfigurera server management startas automatiskt.

### <a name="configure-settings-and-connect-to-vmware"></a>Konfigurera inställningar och ansluta till VMware

1. I guiden Konfigurera server management > **Konfigurera anslutningen**, markera nätverkskortet som ska ta emot replikeringstrafik. Klicka sedan på **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valvet**, Välj din Azure-prenumeration och relevant resursgrupp och valvet.
3. I **installera programvara från tredje part**accepterar licensen agreeemtn och klicka på **ladda ned och installera**, för att installera MySQL-servern.
4. Klicka på **installera VMware PowerLCI**. Kontrollera att alla fönster stängs innan du gör detta. Klicka på **Fortsätt**
5. I **verifiera installation configuration**, kommer att verifieras krav innan du fortsätter.
6. I **konfigurera vCenter Server/vSphere ESXi-servern**, anger du FQDN eller IP-adressen för vCenter-servern eller vSphere-värd som du vill replikera på vilka virtuella datorer är placerade. Ange den port som servern lyssnar och ett eget namn som ska användas för VMware-servern i valvet.
7. Ange autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Klicka på **Lägg till**, och klicka sedan på **Fortsätt**.
8. I **Konfigurera virtuella autentiseringsuppgifter**, ange användarnamn och lösenord som används för att automatiskt installera mobilitetstjänsten på datorer, om replikering har aktiverats. För Windows-datorer måste kontot lokal administratörsbehörighet på de datorer som du vill replikera. Ange information för Linux för rotkontot.
9. Klicka på **Slutför konfigurationen** och slutföra registreringen. 
10. När registreringen är klar i Azure-portalen, kontrollera att konfigurationsservern och VMware-server visas på den **källa** i valvet. Klicka på **OK** att konfigurera inställningar för target.


Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

> [!NOTE]
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart klickar du på **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera target-resurser.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange om ditt mål distributionsmodell är Resource Manager-baserade eller klassiska.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Mål](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Öppna den [Azure-portalen](https://portal.azure.com) och klicka på **alla resurser**.
2. Klicka på valvet Recovery-tjänsten med namnet **ContosoVMVault**.
3. Klicka för att skapa en replikeringsprincip **Site Recovery-infrastruktur** > **replikeringsprinciper** > **+ replikeringsprincip**.
4. I **skapa replikeringsprincip**, ange ett principnamn **VMwareRepPolicy**.
5. I **Återställningspunktmål tröskelvärdet**, använder standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras om kontinuerlig replikering överskrider den gränsen.
6. I **kvarhållningstid för återställningspunkten**, använder standardvärdet 24 timmar för hur länge kvarhållningsperiod är för varje återställningspunkt. För den här kursen väljer vi 72 timmar. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
7. I **frekvens av programkonsekventa ögonblicksbilder**, använder standardvärdet 60 minuter för att skapa programkonsekventa ögonblicksbilder frekvensen. Klicka på **OK** vill skapa principen.

   ![Princip](./media/tutorial-vmware-to-azure/replication-policy.png)

Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om exempelvis replikeringsprinciper är **rep-policy** sedan principen för återställning kommer att **rep-princip-återställning**. Den här principen används inte förrän du har initierat en återställning från Azure.

## <a name="enable-replication"></a>Aktivera replikering

Site Recovery installerar mobilitetstjänsten när replikeringen är aktiverad för en virtuell dator. Det kan ta 15 minuter eller längre ändringarna träder i kraft och visas på portalen.

Aktivera replikering på följande sätt:

1. Klicka på **replikera program** > **källa**.
2. I **källa**, Välj konfigurationsservern.
3. I **datorn typen**väljer **virtuella datorer**.
4. I **vCenter/vSphere-Hypervisor**väljer vCenter-servern som hanterar vSphere-värd, eller värden.
5. Välj processerver (konfigurationsserver). Klicka på IThen **OK**.
6. I **mål**, Välj prenumerationen och resursgrupp som du vill skapa den misslyckade över virtuella datorer. Välj distributionsmodell som du vill använda i Azure (klassiska eller resource management), för den redundansväxlade virtuella datorer.
7. Välj Azure storage-konto som du vill använda för att replikera data.
8. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
9. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator.
10. I **Virtual Machines** > **Välj virtuella datorer** klickar du på och väljer de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.
11. I **egenskaper** > **konfigurera egenskaper för**, Välj det konto som ska användas av processervern för att automatiskt installera mobilitetstjänsten på datorn.
12. I **replikeringsinställningarna** > **konfigurerar replikeringsinställningar**, kontrollera att rätt replikeringsprinciper har valts.
13. Klicka på **Aktivera replikering**.

Du kan följa förloppet för den **Aktivera skydd** jobb **inställningar** > **jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

Om du vill övervaka virtuella datorer som du lägger till, kan du identifierade senast för virtuella datorer i **Configuration-servrar**
> **Senaste kontakt på**. Lägg till virtuella datorer utan att vänta på den schemalagda identifieringen genom att markera konfigurationsservern (inte på den), och klicka på **uppdatera**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra ett återställningstest](site-recovery-test-failover-to-azure.md)
