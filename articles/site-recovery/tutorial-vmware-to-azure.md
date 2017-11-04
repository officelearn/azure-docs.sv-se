---
title: "Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer med Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du ställer in katastrofåterställning till Azure för lokala virtuella VMware-datorer med Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1c9bfe567b1e0872abc7aba054127735d5f61754
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer

Den här kursen visar hur du ställer in katastrofåterställning till Azure för lokal VMware virtuell dator med Windows. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett Recovery Services-valv för Site Recovery
> * Konfigurera käll- och mål-replikering miljöer
> * Skapa replikeringsprincip
> * Aktivera replikering för en virtuell dator

Detta är den tredje vägledningen i en serie. Den här kursen förutsätter att du redan har slutfört uppgifterna i de föregående självstudiekurser:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokal VMware](tutorial-prepare-on-premises-vmware.md)

Innan du börjar är det bra att [granska arkitekturen](concepts-vmware-to-azure-architecture.md) för katastrofåterställning.

## <a name="configure-vmware-account-permissions"></a>Konfigurera behörighet för VMware

1. Skapa en roll på vCenter-nivå. Ge rollen namnet **Azure_Site_Recovery**.
2. Tilldela följande behörigheter till den **Azure_Site_Recovery** roll.

   **Aktivitet** | **Rollbehörigheter /** | **Detaljer**
   --- | --- | ---
   **VM-identifiering** | Data Center objektet –> Sprid till underordnade objekt rollen = skrivskyddad | Minst en användare som är skrivskyddad.<br/><br/> Användaren tilldelas på nivån för datacenter och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).
   **Fullständig replikering, redundans och återställning efter fel** |  Data Center objektet –> Sprid till underordnade objekt rollen = Azure_Site_Recovery<br/><br/> DataStore -> allokera utrymme, bläddra datalagret, låg nivå filåtgärder, ta bort filen och uppdatera filer för virtuella datorer<br/><br/> Nätverk -> nätverk tilldela<br/><br/> Resurs -> Tilldela VM resurspool, migrera är avstängt VM, migrera driven på den virtuella datorn<br/><br/> Aktiviteter -> Skapa uppgift, uppdatera uppgift<br/><br/> Konfiguration av virtuell dator -><br/><br/> Virtual machine -> interagera -> fråga enhetsanslutning, konfigurera CD-skivor, konfigurera diskettenheter media, stänga av, slå på strömmen, installera för VMware-verktyg<br/><br/> Virtual machine -> Lager -> Skapa, registrera, avregistrera<br/><br/> Etablering av virtuell dator -> -> Tillåt virtuella hämtning, tillåter Överför filer för virtuella datorer<br/><br/> Virtual machine -> ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på nivån för datacenter och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).

3. Skapa en användare på vCenter server eller vSphere-värd. Tilldela användaren rollen.

## <a name="specify-what-you-want-to-replicate"></a>Ange vad du vill replikera

Mobilitetstjänsten måste installeras på varje virtuell dator som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för den virtuella datorn. För automatisk installation måste du förbereda ett konto som Site Recovery för att komma åt den virtuella datorn.

Du kan använda en domän eller lokalt konto. För Linux virtuella datorer, måste kontot vara rot på källservern för Linux. För virtuella Windows-datorer, om du inte använder ett domänkonto, inaktivera kontroll av fjärråtkomst till användare på den lokala datorn:

  - I registery under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägga till DWORD-posten **LocalAccountTokenFilterPolicy** och ange värdet till 1.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Konfigurera källmiljön består av hämtar Site Recovery Unified installationsprogram, konfigurera konfigurationsservern och registreras i valvet och identifiera virtuella datorer.

Konfigurationsservern är en enda lokal VMware VM som värd för alla komponenter Platsåterställningen. Den här virtuella datorn körs konfigurationsservern, processervern och huvudmålservern.

- Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- Processervern fungerar som en replikerings-gateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Processervern installeras också mobilitetstjänsten på virtuella datorer du vill replikera, och utför automatisk identifiering av virtuella datorer på lokal VMware-servrar.
- Huvudmålservern hanterar replikeringsdata vid återställning från Azure.

Konfigurationsservern VM ska vara en högtillgänglig VMware VM som uppfyller följande krav:

| **Krav** | **Detaljer** |
|-----------------|-------------|
| Antal processorkärnor| 8 |
| RAM | 12 GB |
| Antal diskar | 3 - OS-disk, disk för processen server cache, kvarhållningsenhetens (för återställning) |
| Ledigt diskutrymme (processerverns cacheminne) | 600 GB |
| Ledigt diskutrymme (kvarhållningsdisken) | 600 GB |
| Operativsystemversion | Windows Server 2012 R2 |
| Nationella inställningar för operativsystem | Engelska (en-us) |
| VMware vSphere PowerCLI-version | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Windows Server-roller | Aktivera inte dessa roller: Active Directory Domain Services, Internet Information Services Hyper-V |
| NIC-typ | VMXNET3 |
| IP-adresstyp | Statisk |
| Portar | 443 (kontrolkanalsorchestration)<br/>9443 (dataöverföring)|

Kontrollera att systemklockan är synkroniserad med en på konfigurationsservern VM.
Tiden måste synkroniseras till inom 15 minuter. Om tidsskillnaden är större än 15 minuter, misslyckas installationen.

Kontrollera att konfigurationsservern VM kan komma åt dessa webbadresser:

- *. accesscontrol.windows.net. Används för Access Control och identitetshantering.
- *. backup.windowsazure.com. Används för överföring av replikeringsdata och koordination.
- *. blob.core.windows.net. Används för åtkomst till lagringskontot som lagrar replikerade data.
- *. hypervrecoverymanager.windowsazure.com. Används för åtgärder för replikeringshantering och koordination.
- Time.nist.gov och time.windows.com. Används för att kontrollera tidssynkronisering mellan system och global tid.

URL:er för Azure Government-moln:

- *.ugv.hypervrecoverymanager.windowsazure.us
- *.ugv.backup.windowsazure.us
- *.ugi.hypervrecoverymanager.windowsazure.us
- *.ugi.backup.windowsazure.us

IP-adressbaserade brandväggsregler bör tillåter kommunikation till [IP-intervall för Azure-Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653), och port 443 (HTTPS) och 9443 (replikering). Se till att tillåta IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och Identity Management).

### <a name="download-the-site-recovery-unified-setup"></a>Hämta platsen Recovery enhetlig installation

1. Öppna den [Azure-portalen](https://portal.azure.com) och klicka på **alla resurser**.
2. Klicka på valvet Recovery-tjänsten med namnet **ContosoVMVault**.
3. Klicka på **Site Recovery** > **Förbered infrastrukturen** > **skyddsmål**.
4. Välj **lokalt** för där dina datorer finns **till Azure** där du vill replikera dina datorer och **Ja, med VMware vSphere-Hypervisor**. Klicka på **OK**.
5. Klicka på i fönstret Förbered källa **+ konfigurationsservern**.
6. I **Lägg till Server**, kontrollera att **konfigurationsservern** visas i **servertyp**.
7. Hämta installationsfilen för enhetlig installationsprogram för Site Recovery.
8. Ladda ned valvregistreringsnyckeln. Du behöver detta när du kör installationsprogrammet för enhetlig. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Konfigurera källan](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>Ställ in konfigurationsservern

1. Kör det enhetliga installationsprogrammet.
2. I **innan du börjar**väljer **installera konfigurationsservern och processervern** Klicka **nästa**.

3. I **licensvillkoren för programvara från tredje part**, klickar du på **jag accepterar** om du vill hämta och installera MySQL, klicka på **nästa**.

4. I **Registration** (Registrering) väljer du den registreringsnyckel som du hämtade från valvet.

5. I **Internet Settings** (Internetinställningar) anger du hur providern som körs på konfigurationsservern ska ansluta till Azure Site Recovery via internet.

   - Om du vill ansluta till proxyservern som för närvarande har ställts in på datorn, väljer **Anslut till Azure Site Recovery via en proxyserver**.
   - Om du vill att providern ska ansluta direkt väljer **Anslut direkt till Azure Site Recovery utan en proxyserver**.
   - Om den befintliga proxyservern kräver autentisering, eller om du vill använda en anpassad proxyserver för Provider-anslutning väljer **Anslut med anpassade proxyinställningar**, och ange adress, port och autentiseringsuppgifter.

   ![Brandvägg](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

   ![Krav](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. I **MySQL Configuration** (MySQL-konfiguration) skapar du autentiseringsuppgifter för att logga in på den MySQL-serverinstans som är installerad.

8. I **miljö information**väljer **Ja** att skydda virtuella VMware-datorer. Installationsprogrammet kontrollerar att PowerCLI 6.0 är installerad.

9. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.

10. I **Val av nätverk** anger du lyssnare (nätverkskort och SSL-port) där konfigurationsservern skickar och tar emot replikeringsdata. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Vi kan också öppna port 443, som används för att samordna replikeringen har. Använd inte port 443 för att skicka eller ta emot replikeringstrafik.

11. I **Sammanfattning** granskar du informationen och klickar på **Installera**. Installationsprogrammet installerar konfigurationsservern och registreras med Azure Site Recovery-tjänsten.

    ![Sammanfattning](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    När installationen är klar skapas en lösenfras. Du behöver den när du aktiverar replikering. Kopiera lösenfrasen och förvara den på en säker plats. Servern visas på den **inställningar** > **servrar** rutan i valvet.

### <a name="configure-automatic-discovery"></a>Konfigurera automatisk identifiering

Konfigurationsservern måste ansluta till lokal VMware-servrar för att identifiera virtuella datorer. Lägg till vCenter-servern eller vSphere-värdar, med ett konto som har administratörsbehörighet på servern för den här kursen.

1. På din konfigurationsservern och starta **CSPSConfigtool.exe**. Filen finns som en genväg på skrivbordet och även på följande sökväg: *installationsplatsen*\home\svsystems\bin.

2. Klicka på **Hantera konton** > **Lägg till konto**.

   ![Lägg till konto](./media/tutorial-vmware-to-azure/credentials1.png)

3. I **Kontoinformation** lägger du till kontot som ska användas för automatisk identifiering.

   ![Information](./media/tutorial-vmware-to-azure/credentials2.png)

Lägga till en server:

1. Öppna den [Azure-portalen](https://portal.azure.com) och klicka på **alla resurser**.
2. Klicka på valvet Recovery-tjänsten med namnet **ContosoVMVault**.
3. Klicka på **Site Recovery** > **förbereda infrastrukturen** > **källa**
4. Välj **+ vCenter** att ansluta till en vCenter server eller vSphere ESXi-värd.
5. I **lägga till vCenter**, ange ett eget namn för servern. Ange IP-adress eller fullständigt domännamn.
6. Lämna den port som har angetts till 443, såvida inte VMware-servrar lyssna efter begäranden på en annan port.
7. Välj kontot som ska användas för att ansluta till servern. Klicka på **OK**.

Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

> [!NOTE]
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart klickar du på **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera target-resurser.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange om ditt mål distributionsmodell är Resource Manager-baserade eller klassiska.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![mål](./media/tutorial-vmware-to-azure/storage-network.png)

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
