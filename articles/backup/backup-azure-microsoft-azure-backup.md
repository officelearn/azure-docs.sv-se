---
title: Säkerhetskopiera arbetsbelastningar till Azure med Azure Backup Server | Microsoft Docs
description: Använda Azure Backup Server för att skydda eller säkerhetskopiera arbetsbelastningar till Azure-portalen.
services: backup
documentationcenter: ''
author: PVRK
manager: shivamg
editor: ''
keywords: Azure backup-servern. skydda arbetsbelastningar; Säkerhetskopiera arbetsbelastningar
ms.assetid: e7fb1907-9dc1-4ca1-8c61-50423d86540c
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/5/2018
ms.author: masaran;trinadhk;pullabhk;markgal;adigan
ms.openlocfilehash: 3b37afc9d768313f6cc202eeecca22528cc57b07
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Förbereder för att säkerhetskopiera arbetsbelastningar med Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Den här artikeln beskriver hur du förbereder din miljö för att säkerhetskopiera med hjälp av Azure Backup Server-arbetsbelastningar. Med Azure Backup Server, kan du skydda arbetsbelastningar för program som Hyper-V virtuella datorer, Microsoft SQL Server, SharePoint Server, Microsoft Exchange och Windows-klienter från en enda konsol.

> [!NOTE]
> Azure Backup-Server kan nu att skydda virtuella VMware-datorer och ger funktioner för ökad säkerhet. Installera produkten som beskrivs i avsnitten nedan. använda uppdatering 1 och den senaste Azure Backup-agenten. Mer information om hur du säkerhetskopierar VMware-servrar med Azure Backup Server finns i artikeln [Använd Azure Backup Server att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md). Om du vill veta mer om säkerhetsfunktioner, referera till [Azure backup säkerhetsfunktioner dokumentationen](backup-azure-security-feature.md).
>
>

Du kan även skydda infrastruktur som en tjänst (IaaS) arbetsbelastningar som till exempel virtuella datorer i Azure.

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln innehåller information och procedurer för att återställa virtuella datorer som distribueras med hjälp av Resource Manager-modellen.
>
>

Azure Backup Server ärver mycket av funktionen arbetsbelastning säkerhetskopiering från Data Protection Manager (DPM). Den här innehåller artikellänkar till DPM-dokumentationen förklarar vissa av de funktioner som delas. Även om Azure Backup Server delar en stor del av samma funktioner som DPM. Azure Backup-servern inte säkerhetskopiera till band eller integreras den med System Center.

## <a name="choose-an-installation-platform"></a>Välj en installationsplattform
Det första steget mot hämta Azure Backup Server och kör är att konfigurera en Windows-Server. Servern kan vara i Azure eller lokalt.

### <a name="using-a-server-in-azure"></a>Använder en server i Azure
När du väljer en server för att köra Azure Backup Server, bör du börja med en bild i galleriet för Windows Server 2012 R2 Datacenter eller Windows Server 2016 Datacenter. I artikeln [skapa din första Windows-dator i Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), innehåller en vägledning för att komma igång med den rekommenderade virtuella datorn i Azure, även om du aldrig har använt Azure förut. Rekommenderade minimikraven för den virtuella datorn (VM) ska vara: A2 Standard med två kärnor och 3.5 GB RAM.

Skydda arbetsbelastningar med Azure Backup Server har många olika delarna. I artikeln [installera DPM som en virtuell Azure-dator](https://technet.microsoft.com/library/jj852163.aspx), hjälper beskrivs de olika delarna. Läs den här artikeln helt innan du distribuerar datorn.

### <a name="using-an-on-premises-server"></a>Med hjälp av en lokal server
Om du inte vill köra grundläggande server i Azure kan du köra servern på en Hyper-V virtuell dator, en VMware-VM eller en fysisk värd. Rekommenderade minimikraven för servermaskinvaran är två kärnor och 4 GB RAM-minne. Operativsystem som stöds finns i följande tabell:

| Operativsystem | Plattform | SKU |
|:--- | --- |:--- |
| Windows Server 2016 och senaste Service Pack |64-bitars |Standard, Datacenter, Essentials (MABS v2 onwards) |
| Windows Server 2012 R2 och senaste Service Pack |64-bitars |Standard, Datacenter, Foundation |
| Windows Server 2012 och senaste Service Pack |64-bitars |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 och senaste Service Pack |64-bitars |Standard, Workgroup |
| Windows Storage Server 2012 och senaste Service Pack |64-bitars |Standard, Workgroup |

Du kan deduplicera DPM-lagring med hjälp av Windows Server Deduplicering. Läs mer om hur [DPM och deduplicering](https://technet.microsoft.com/library/dn891438.aspx) tillsammans när de distribueras i Hyper-V virtuella datorer.

> [!NOTE]
> Azure Backup-Server är avsedd att köras på en dedikerad server med enda syfte. Du kan inte installera Azure Backup Server på:
> - En dator som körs som en domänkontrollant
> - En dator där programserverrollen är installerad
> - En dator som är en System Center Operations Manager-hanteringsserver
> - En dator som kör Exchange Server
> - En dator som är en nod i ett kluster

Alltid ansluta till Azure Backup Server till en domän. Om du planerar att flytta servern till en annan domän, rekommenderas att du ansluter servern till den nya domänen innan du installerar Azure Backup Server. Flytta en befintlig Azure Backup Server-dator till en ny domän när distributionen är *stöds inte*.

## <a name="recovery-services-vault"></a>Recovery Services-valv
Om du skickar säkerhetskopierade data till Azure eller se till att den lokalt, måste programmet vara anslutna till Azure. För att vara mer specifik, Azure Backup Server-datorn måste vara registrerad med en recovery services-valvet.

Så här skapar du ett Recovery Services-valv:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På navmenyn klickar du på **Bläddra** och i listan över resurser skriver du **Recovery Services**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    Listan över Recovery Services-valv visas.
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

    ![Skapa ett Recovery Services-valv (steg 5)](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)
4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.
5. Klicka på **Prenumeration** för att visa listan över prenumerationer. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.
6. Klicka på **Resursgrupp** för att visa listan över resursgrupper eller klicka på **Nytt** för att skapa en ny resursgrupp. För komplett information om resursgrupper, se [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Klicka på **Plats** för att välja en geografisk region för valvet.
8. Klicka på **Skapa**. Det kan ta en stund innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger på portalen.
   När du har skapat ditt valv öppnas i portalen.

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering
Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Om det här valvet är ditt primära valv lämna lagringsalternativet inställt på geo-redundant lagring. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj ditt valv för att öppna instrumentpanelen för valvet och bladet Inställningar. Om bladet **Inställningar** inte öppnas klickar du på **Alla inställningar** på instrumentpanelen för valvet.
2. På bladet **Inställningar** klickar du på **Infrastruktur för säkerhetskopiering** > **Konfiguration av säkerhetskopiering** för att öppna bladet **Konfiguration av säkerhetskopiering**. På bladet **Konfiguration av säkerhetskopiering** väljer du alternativet för lagringsreplikering för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    När du har valt lagringsalternativet för valvet är det dags att associera den virtuella datorn med valvet. För att börja kopplingen identifierar du och registrerar de virtuella Azure-datorerna.

## <a name="software-package"></a>Programpaket
### <a name="downloading-the-software-package"></a>Hämta programpaket
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Om du redan har ett öppet för Recovery Services-valvet vidare till steg 3. Om inget Recovery Services-valv är öppet, men du befinner dig på Azure-portalen klickar du på **Bläddra** på navmenyn.

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

     ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Listan över Recovery Services-valv visas.
   * Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.

     ![Öppna bladet för valvet](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Den **inställningar** bladet som öppnas som standard. Om den är stängd klickar du på **inställningar** att öppna inställningsbladet.

    ![Öppna bladet för valvet](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Klicka på **säkerhetskopiering** att öppna guiden komma igång.

    ![Säkerhetskopiering komma igång](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    I den **Kom igång med säkerhetskopiering** bladet som öppnas, **säkerhetskopiering mål** blir automatiskt markerad.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. I den **säkerhetskopiering målet** bladet från den **var körs din arbetsbelastning** väljer du **lokalt**.

    ![lokalt och arbetsbelastningar som mål](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Från den **vad vill du säkerhetskopiera?** nedrullningsbara menyn, Välj de arbetsbelastningar som du vill skydda med Azure Backup-Server och klicka sedan på **OK**.

    Den **Kom igång med säkerhetskopiering** guiden växlar den **Förbered infrastruktur** alternativet om du vill säkerhetskopiera arbetsbelastningar till Azure.

   > [!NOTE]
   > Om du bara vill säkerhetskopiera filer och mappar rekommenderar vi med Azure Backup-agenten och följa instruktionerna i artikeln, [förhandstitt: säkerhetskopiera filer och mappar](backup-try-azure-backup-in-10-mins.md). Om du ska skydda flera filer och mappar eller om du planerar att expandera skydd behov i framtiden, Välj dessa arbetsbelastningar.
   >
   >

    ![Komma igång-guiden Ändra](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. I den **Förbered infrastruktur** bladet som öppnas klickar du på den **hämta** länkar för att installera Azure Backup-Server och ladda ned valvautentiseringsuppgifter. Du kan använda valvautentiseringsuppgifter under registrering av Azure Backup Server till recovery services-valvet. Länkarna tar dig till Download Center där du kan hämta programpaketet.

    ![Förbereda infrastrukturen för Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Markera alla filer och på **nästa**. Hämta alla filer som kommer från hämtningssidan för Microsoft Azure Backup och placera alla filer i samma mapp.

    ![Hämta center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Eftersom hämtningsstorleken för alla filer tillsammans > 3G, på en 10 Mbit/s ned länken som det kan ta upp till 60 minuter för att slutföra hämtningen.

### <a name="extracting-the-software-package"></a>Extrahera programpaket
När du har laddat ned alla filer, klickar du på **MicrosoftAzureBackupInstaller.exe**. Detta startar den **installationsguiden för Microsoft Azure Backup** att extrahera installationsfilerna till en plats som anges av du. Fortsätter med guiden och klicka på den **extrahera** för att starta extraheringsprocessen.

> [!WARNING]
> Minst 4GB ledigt diskutrymme krävs för att extrahera installationsfilerna.
>
>

![Installationsguiden för Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

När extraheringsprocessen slutförd, markera kryssrutan för att starta nyligen extraherade *setup.exe* börja installera Microsoft Azure Backup Server och klicka på den **Slutför** knappen.

### <a name="installing-the-software-package"></a>Installera programpaket
1. Klicka på **Microsoft Azure Backup** att starta installationsguiden.

    ![Installationsguiden för Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. På välkomstskärmen klickar du på den **nästa** knappen. Då kommer du att den *nödvändiga kontrollerar* avsnitt. Klicka på den här skärmen **Kontrollera** att avgöra om kraven för maskinvara och programvara för Azure Backup Server är uppfyllda. Om alla krav är uppfyllda har, visas ett meddelande som anger att datorn uppfyller kraven. Klicka på den **nästa** knappen.

    ![Kontrollera om Azure Backup Server - Välkommen och förutsättningar](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server kräver SQL Server Standard. Installationspaketet för Azure Backup Server kommer dessutom paketerade med lämpliga binärfilerna för SQL Server krävs om du inte vill använda din egen SQL. När du börjar med en ny Azure Backup Server-installation, bör du välja alternativet **installera nya SQL Server-instans med de här inställningarna** och klicka på den **kontrollera och installera** knappen. När de nödvändiga komponenter har installerats klickar du på **nästa**.

    ![Azure Backup-Server - kontroll av SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Om det uppstår ett fel med en rekommendation att starta om datorn, göra det och klickar på **kontrollen igen**.

   > [!NOTE]
   > Azure Backup Server fungerar inte med en fjärrinstans av SQL Server. Den instans som används av Azure Backup Server måste vara lokal.
   >
   >
4. Ange en plats för installation av Microsoft Azure Backup serverfiler och klicka på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Den virtuella platsen är ett krav för säkerhetskopiering till Azure. Se till att den virtuella platsen är minst 5% av de data som ska säkerhetskopieras till molnet. För skydd på disk måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns [konfigurera lagringspooler och disklagring](https://technet.microsoft.com/library/hh758075.aspx).
5. Ange ett starkt lösenord för begränsade lokala användarkonton och klicka på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Välj om du vill använda *Microsoft Update* att söka efter uppdateringar och klicka på **nästa**.

   > [!NOTE]
   > Vi rekommenderar att du har Windows Update omdirigera till Microsoft Update, vilket ger säkerhet och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup-Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Granska de *inställningar för sammanfattning av* och på **installera**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Installationen sker i faser. I den första fasen är Microsoft Azure Recovery Services-agenten installerad på servern. Guiden kontrollerar också Internet-anslutningen. Om Internet-anslutning är tillgänglig kan du fortsätta med installationen, annars måste du ange proxyinformationen att ansluta till Internet.

    Nästa steg är att konfigurera Microsoft Azure Recovery Services-agenten. Som en del av konfigurationen måste du ange dina valvautentiseringsuppgifter för att registrera datorn till recovery services-valvet. Du kan också ange en lösenfras och Kryptera/dekryptera data som skickas mellan Azure och ditt lokala. Du kan automatiskt generera en lösenfras eller ange egna minst 16 tecken lösenfras. Fortsätt med guiden förrän agenten har konfigurerats.

    ![Azure Backup Serer PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. När registreringen av Microsoft Azure Backup-server har slutförts fortsätter övergripande installationsguiden med installationen och konfigurationen av SQL Server och Azure Backup Server-komponenter. När installationen av SQL Server-komponenten har slutförts installeras Azure Backup Server-komponenter.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

När installationssteget har slutförts, kommer produktens skrivbordsikoner har skapats samt. Dubbelklickar du på ikonen om du vill starta produkten.

### <a name="add-backup-storage"></a>Lägg till lagring för säkerhetskopiering
Den första säkerhetskopian sparas på lagringsutrymme som är anslutet till Azure Backup-Server-datorn. Läs mer om att lägga till diskar i [konfigurera lagringspooler och disklagring](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Du måste lägga till lagring för säkerhetskopiering, även om du planerar att skicka data till Azure. I den aktuella arkitekturen i Azure Backup-Server Azure Backup-valvet innehåller den *andra* kopia av data under den lokala lagringen innehåller säkerhetskopian första (och obligatoriska).
>
>

## <a name="network-connectivity"></a>Nätverksanslutning
Azure Backup-servern kräver anslutning till Azure Backup-tjänsten för att produkten ska fungera korrekt. Du kan kontrollera om datorn är ansluten till Azure genom att använda den ```Get-DPMCloudConnection``` cmdlet i Azure Backup Server PowerShell-konsolen. Om utdata från cmdleten är TRUE och anslutning finns, eller det finns ingen nätverksanslutning.

Azure-prenumerationen måste vara i felfritt tillstånd på samma gång. Ta reda på status för din prenumeration och hantera den, logga in på den [prenumeration portal](https://account.windowsazure.com/Subscriptions).

När du vet att anslutningen Azure och Azure-prenumerationen kan använda du tabellen nedan ta reda på inverkan på funktionen säkerhetskopiering/återställning.

| Tillstånd för anslutning | Azure-prenumeration | Säkerhetskopiera till Azure | Säkerhetskopiera till disk | Återställa från Azure | Återställa från disken |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Aktiv |Tillåtet |Tillåtet |Tillåtet |Tillåtet |
| Ansluten |Har upphört att gälla |Stoppad |Stoppad |Tillåtet |Tillåtet |
| Ansluten |Avetableras |Stoppad |Stoppad |Stoppad och Azure återställningspunkter tas bort |Stoppad |
| Förlorade anslutningen > 15 dagar |Aktiv |Stoppad |Stoppad |Tillåtet |Tillåtet |
| Förlorade anslutningen > 15 dagar |Har upphört att gälla |Stoppad |Stoppad |Tillåtet |Tillåtet |
| Förlorade anslutningen > 15 dagar |Avetableras |Stoppad |Stoppad |Stoppad och Azure återställningspunkter tas bort |Stoppad |

### <a name="recovering-from-loss-of-connectivity"></a>Återställa från anslutningsproblem
Om du har en brandvägg eller en proxy som hindrar åtkomst till Azure måste du godkänd domän följande adresser i brandväggen/proxy-profil:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

När anslutningen till Azure har återställts till Azure Backup Server-datorn och bestäms de åtgärder som kan utföras av tillståndet Azure-prenumeration. Tabellen ovan innehåller information om åtgärder som är tillåtna när datorn är ”ansluten”.

### <a name="handling-subscription-states"></a>Hantera prenumerationstillstånd
Det är möjligt att ta en Azure-prenumeration från en *utgångna* eller *Deprovisioned* till den *Active* tillstånd. Men det har vissa effekter på produkt-beteende när tillståndet inte är *Active*:

* En *Deprovisioned* prenumeration förlorar funktioner för den period som den är avetableras. För att aktivera *Active*, produktfunktioner av säkerhetskopiering/återställning återställas. Säkerhetskopierade data på den lokala disken kan även hämtas om det var hålls med en tillräckligt lång Bevarandeperiod. Säkerhetskopierade data i Azure är dock oåterkalleligen förlorade när prenumerationen försätts den *Deprovisioned* tillstånd.
* En *utgångna* prenumeration förlorar endast funktioner för tills det har gjorts *Active* igen. Alla schemalagda säkerhetskopieringar för den period som prenumerationen var *utgångna* körs inte.

## <a name="troubleshooting"></a>Felsökning
Om Microsoft Azure Backup-servern misslyckas med fel under installationsfasen (eller säkerhetskopiering eller återställning), [fel koder dokumentet](https://support.microsoft.com/kb/3041338) för mer information.
Du kan också läsa [Azure Backup relaterade vanliga frågor och svar](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg
Du kan få detaljerad information om [förbereda din miljö för DPM](https://technet.microsoft.com/library/hh758176.aspx) på webbplatsen Microsoft TechNet. Det innehåller även information om konfigurationer som stöds med Azure Backup Server kan distribueras och används.

Du kan använda dessa artiklar för att få en djupare förståelse för skydd av arbetsbelastning med Microsoft Azure Backup-server.

* [SQL Server-säkerhetskopiering](backup-azure-backup-sql.md)
* [SharePoint server-säkerhetskopiering](backup-azure-backup-sharepoint.md)
* [Alternativ server-säkerhetskopiering](backup-azure-alternate-dpm-server.md)
