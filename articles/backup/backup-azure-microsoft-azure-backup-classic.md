---
title: "Använda Azure Backup Server för att säkerhetskopiera arbetsbelastningar till klassiska Azure-portalen | Microsoft Docs"
description: "Kontrollera att din miljö är korrekt beredd att säkerhetskopiera arbetsbelastningar med Azure Backup Server"
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
keywords: "Azure backup-servern. säkerhetskopieringsvalvet"
ms.assetid: d86450e8-da63-4283-8fd7-2ee629fa14ab
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: masaran;trinadhk;pullabhk;markgal
ms.openlocfilehash: ffef289e154986e4b08a072d3a95f77818fb9c35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Förbereder för att säkerhetskopiera arbetsbelastningar med Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup-Server (klassisk)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (klassisk)](backup-azure-dpm-introduction-classic.md)
>
>

Den här artikeln handlar om hur du förbereder din miljö för att säkerhetskopiera arbetsbelastningar med Azure Backup Server. Med Azure Backup Server, kan du skydda arbetsbelastningar för program som Hyper-V virtuella datorer, Microsoft SQL Server, SharePoint Server, Microsoft Exchange och Windows-klienter från en enda konsol.

> [!WARNING]
> Azure Backup Server ärver funktionen av Data Protection Manager (DPM) för säkerhetskopiering av arbetsbelastningen. Du hittar pekare till DPM-dokumentationen för några av dessa funktioner. Azure Backup Server dock inte ge skydd på band eller integrera med System Center.
>
>

## <a name="1-windows-server-machine"></a>1. Windows Server-dator
![steg 1](./media/backup-azure-microsoft-azure-backup/step1.png)

Det första steget mot Azure Backup Server komma och köra är att ha en dator med Windows Server.

| Plats | Minimikrav | Ytterligare instruktioner |
| --- | --- | --- |
| Azure |Virtuell Azure IaaS-dator<br><br>A2 Standard: 2 kärnor, 3.5GB RAM-minne |Du kan börja med en enkel galleriet bild av Windows Server 2012 R2 Datacenter. [Skydda IaaS-arbetsbelastningar med Azure Backup Server (DPM)](https://technet.microsoft.com/library/jj852163.aspx) har många olika delarna. Se till att läsa artikeln helt innan du distribuerar datorn. |
| Lokal |Hyper-V-dator<br> VMWare-dator<br> eller en fysisk värd<br><br>2 kärnor och 4GB RAM-minne |Du kan deduplicera DPM-lagring med hjälp av Windows Server Deduplicering. Läs mer om hur [DPM och deduplicering](https://technet.microsoft.com/library/dn891438.aspx) tillsammans när de distribueras i Hyper-V virtuella datorer. |

> [!NOTE]
> Du rekommenderas att Azure Backup Server installeras på en dator med Windows Server 2012 R2 Datacenter. Många av kraven omfattas automatiskt med den senaste versionen av Windows-operativsystemet.
>
>

Om du planerar att ansluta till Azure Backup Server till en domän, rekommenderas att du ansluter fysisk server eller virtuell dator till domänen innan du installerar Azure Backup Server-programvara. Flytta ett Azure Backup Server till en ny domän efter distribution, är *stöds inte*.

## <a name="2-backup-vault"></a>2. Säkerhetskopieringsvalvet
![steg 2](./media/backup-azure-microsoft-azure-backup/step2.png)

Om du skickar säkerhetskopierade data till Azure eller se till att den lokalt, måste Azure Backup-servern registreras för ett valv. Om du har använt Azure Backup och vill använda Azure Backup Server, finns i Azure portal versionen av den här artikeln - [förbereda säkerhetskopiering av arbetsbelastningar med Azure Backup Server](backup-azure-microsoft-azure-backup.md).

> [!IMPORTANT]
> Från och med mars 2017 kan du inte längre använda den klassiska portalen för att skapa säkerhetskopieringsvalv.
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> Efter den 15 oktober 2017 kan du inte längre använda PowerShell för att skapa säkerhetskopieringsvalv. **Från den 1 november 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>



## <a name="3-software-package"></a>3. Programpaket
![steg 3](./media/backup-azure-microsoft-azure-backup/step3.png)

### <a name="downloading-the-software-package"></a>Hämta programpaket
Precis som valvet autentiseringsuppgifter, du kan hämta Microsoft Azure Backup för programbelastningar från den **snabb startsida** i säkerhetskopieringsvalvet.

1. Klicka på **för programbelastningar (Disk till Disk till moln)**. Detta tar dig vidare till sidan Download Center från där du kan hämta programpaketet.

    ![Microsoft Azure Backup välkomstskärmen](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)
2. Klicka på **Hämta**.

    ![Hämta center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)
3. Markera alla filer och på **nästa**. Hämta alla filer som kommer från hämtningssidan för Microsoft Azure Backup och placera alla filer i samma mapp.
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
2. På välkomstskärmen klickar du på den **nästa** knappen. Då kommer du att den *nödvändiga kontrollerar* avsnitt. På den här skärmen klickar du på den **Kontrollera** knappen för att bestämma om maskinvara och programvara krav för Azure Backup Server har uppfyllts. Om alla krav har uppfyllts har visas ett meddelande om att datorn uppfyller kraven. Klicka på den **nästa** knappen.

    ![Kontrollera om Azure Backup Server - Välkommen och förutsättningar](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server kräver SQL Server Standard och Azure Backup Server installationspaketet medföljer med lämpliga binärfilerna för SQL Server krävs. När du börjar med en ny Azure Backup Server-installation, bör du välja alternativet **installera nya SQL Server-instans med de här inställningarna** och klicka på den **kontrollera och installera** knappen. När de nödvändiga komponenter har installerats klickar du på **nästa**.

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

    Nästa steg är att konfigurera Microsoft Azure Recovery Services-agenten. Som en del av konfigurationen måste du ange dina autentiseringsuppgifter för valv att registrera datorn för säkerhetskopieringsvalvet. Du kan också ange en lösenfras och Kryptera/dekryptera data som skickas mellan Azure och ditt lokala. Du kan automatiskt generera en lösenfras eller ange egna minst 16 tecken lösenfras. Fortsätt med guiden förrän agenten har konfigurerats.

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

## <a name="4-network-connectivity"></a>4. Nätverksanslutning
![step4](./media/backup-azure-microsoft-azure-backup/step4.png)

Azure Backup-servern kräver anslutning till Azure Backup-tjänsten för att produkten ska fungera korrekt. Du kan kontrollera om datorn är ansluten till Azure genom att använda den ```Get-DPMCloudConnection``` cmdlet i Azure Backup Server PowerShell-konsolen. Om utdata från cmdleten igen är TRUE och anslutning finns, eller det finns ingen nätverksanslutning.

Azure-prenumerationen måste vara i felfritt tillstånd på samma gång. Ta reda på status för din prenumeration och hantera den, logga in på den [prenumeration portal](https://account.windowsazure.com/Subscriptions).

När du vet att anslutningen Azure och Azure-prenumerationen kan använda du tabellen nedan ta reda på inverkan på funktionen säkerhetskopiering/återställning.

| Tillstånd för anslutning | Azure-prenumeration | Säkerhetskopiering till Azure | Säkerhetskopiering till disk | Återställa från Azure | Återställa från disken |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Active |Tillåtna |Tillåtna |Tillåtna |Tillåtna |
| Ansluten |Upphört att gälla |Stoppad |Stoppad |Tillåtna |Tillåtna |
| Ansluten |Avetableras |Stoppad |Stoppad |Stoppad och Azure återställningspunkter tas bort |Stoppad |
| Förlorade anslutningen > 15 dagar |Active |Stoppad |Stoppad |Tillåtna |Tillåtna |
| Förlorade anslutningen > 15 dagar |Upphört att gälla |Stoppad |Stoppad |Tillåtna |Tillåtna |
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
