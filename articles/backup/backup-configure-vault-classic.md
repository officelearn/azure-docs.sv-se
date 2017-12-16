---
title: "Säkerhetskopiera Windows server eller arbetsstation till Azure (klassiska modellen) | Microsoft Docs"
description: "Säkerhetskopiera Windows-servrar eller klienter till ett säkerhetskopieringsvalv i Azure. Gå igenom grunderna för att skydda filer och mappar till ett säkerhetskopieringsvalv med hjälp av Azure Backup-agenten."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "säkerhetskopieringsvalvet; Säkerhetskopiera en Windows-server. Säkerhetskopiera windows;"
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: cwatson
ms.openlocfilehash: 16d4581b94501203707099692b37fd9a04a213f1
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="back-up-a-windows-server-or-workstation-to-azure-using-the-classic-portal"></a>Säkerhetskopiera en Windows server eller arbetsstation till Azure med hjälp av den klassiska portalen
> [!div class="op_single_selector"]
> * [Klassisk portal](backup-configure-vault-classic.md)
> * [Azure Portal](backup-configure-vault.md)
>
>

Den här artikeln innehåller procedurer som du måste följa för att förbereda din miljö och säkerhetskopiera Windows server (eller arbetsstationen) till Azure. Den omfattar också överväganden för distribution av din lösning för säkerhetskopiering. Om du är intresserad av försök Azure Backup för första gången den här artikeln guidar snabbt dig genom processen.

Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

## <a name="before-you-start"></a>Innan du börjar
Om du vill säkerhetskopiera en server eller klient till Azure, behöver du ett Azure-konto. Om du inte har någon, kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter.

## <a name="create-a-backup-vault"></a>Skapa ett säkerhetskopieringsvalv
Om du vill säkerhetskopiera filer och mappar från en server eller klient, måste du skapa ett säkerhetskopieringsvalv i den geografiska region där du vill lagra data.

> [!IMPORTANT]
> Från och med mars 2017 kan du inte längre använda den klassiska portalen för att skapa säkerhetskopieringsvalv.
>
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> Efter **30 November 2017**, kommer du inte kunna använda PowerShell för att skapa säkerhetskopieringsvalv. <br/> **Med 30 November 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>


## <a name="download-the-vault-credential-file"></a>Hämta valvautentiseringsfilen
Den lokala datorn måste autentiseras med ett säkerhetskopieringsvalv innan den kan säkerhetskopiera data till Azure. Autentisering uppnås genom *valvet autentiseringsuppgifter*. Valvautentiseringsfilen hämtas via en säker kanal från den klassiska portalen. Certifikatets privata nyckel sparas inte i portalen eller tjänsten.

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>Hämta valvautentiseringsfilen till en lokal dator
1. I det vänstra navigeringsfönstret klickar du på **återställningstjänster**, och välj sedan det säkerhetskopieringsvalv som du skapade.

    ![IR slutfört](./media/backup-configure-vault-classic/rs-left-nav.png)
2. På sidan Snabbstart **ladda ned valvautentiseringsuppgifter**.

   Den klassiska portalen genererar en valvautentiseringen med hjälp av en kombination av valvnamnet och det aktuella datumet. Valvautentiseringsfilen används endast under registreringen arbetsflödet och upphör att gälla efter 48 timmar.

   Valvautentiseringsfilen kan hämtas från portalen.
3. Klicka på **spara** att hämta valvautentiseringsfilen till mappen på det lokala kontot. Du kan också välja **Spara som** från den **spara** menyn för att ange en plats för valvautentiseringsfilen.

   > [!NOTE]
   > Kontrollera att valvautentiseringsfilen sparas på en plats som kan nås från datorn. Om den är lagrad i en fil resurs eller server message block, kontrollerar du att du har behörighet att komma åt den.
   >
   >

## <a name="download-install-and-register-the-backup-agent"></a>Hämta, installera och registrera Backup-agenten
När du skapar säkerhetskopieringsvalvet och hämta valvautentiseringsfilen installeras en agent på varje Windows-datorer.

### <a name="to-download-install-and-register-the-agent"></a>Hämta, installera och registrera agenten
1. Klicka på **återställningstjänster**, och välj sedan det säkerhetskopieringsvalv som du vill registrera en server.
2. På sidan Snabbstart klickar du på agenten **Agent för Windows Server och System Center Data Protection Manager eller Windows-klient**. Klicka sedan på **Spara**.

    ![Spara agent](./media/backup-configure-vault-classic/agent.png)
3. När du har hämtat filen MARSagentinstaller.exe klickar du på **kör** (eller dubbelklicka på **MARSAgentInstaller.exe** från lagringsplatsen).
4. Välj installationsmappen och cachemappen som krävs för agenten och klicka sedan på **nästa**. Cacheplats som du anger måste ha ledigt utrymme som är lika med minst 5 procent av säkerhetskopierade data.
5. Du kan fortsätta att ansluta till Internet via en proxy-inställningarna som standard.             Om du använder en proxyserver för att ansluta till Internet, på sidan proxykonfiguration markerar den **anpassade proxyinställningar** kryssrutan och sedan ange proxyinformationen för servern. Om du använder en autentiserad proxyserver Ange användarinformation för namn och lösenord och klicka sedan på **nästa**.
6. Klicka på **installera** att påbörja installationen av agenten. Säkerhetskopieringsagenten installerar .NET Framework 4.5 och Windows PowerShell (om det inte redan är installerat) att slutföra installationen.
7. När agenten är installerad klickar du på **fortsätta till registreringen** att fortsätta med arbetsflödet.
8. Bläddra till och markera valvautentiseringsfilen som du tidigare hämtade på sidan valvet identifiering.

    Valvautentiseringsfilen är giltig endast 48 timmar efter att den har hämtats från portalen. Om det uppstår ett fel på den här sidan (till exempel ”valvautentiseringsuppgifter tillhandahållna filen har upphört att gälla”), logga in på portalen och hämta valvautentiseringsfilen igen.

    Se till att valvautentiseringsfilen finns på en plats som kan användas av installationsprogrammet. Om du får åtkomst-relaterade fel, kopiera valvautentiseringsfilen till en tillfällig plats på samma dator och försök igen.

    Om det uppstår ett fel med valvet autentiseringsuppgifter, till exempel ”ogiltiga autentiseringsuppgifter tillhandahålls” i filen är skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med recovery-tjänsten. Försök igen när du har hämtat en ny valvautentiseringsfil från portalen. Det här felet kan också inträffa om en användare klickar på den **Download valvautentiseringen** alternativet flera gånger i snabb följd. I det här fallet gäller bara den senaste valvautentiseringsfilen.
9. På sidan krypteringsinställning kan du generera en lösenfras eller ange en lösenfras (med minst 16 tecken). Kom ihåg att spara lösenfrasen på en säker plats.
10. Klicka på **Slutför**. Guiden registrera Server registrerar servern med säkerhetskopieringen.

    > [!WARNING]
    > Om du förlorar eller glömmer bort lösenfrasen kan Microsoft hjälpa dig återställa säkerhetskopierade data. Du äger krypteringslösenfrasen och Microsoft har inte insyn i det lösenord som du använder. Spara filen på en säker plats eftersom den måste utföras under en återställning.
    >
    >

11. När du har angett krypteringsnyckeln lämna den **starta Microsoft Azure Recovery Services-agenten** vara markerad och klicka sedan på **Stäng**.

## <a name="complete-the-initial-backup"></a>Slutför den första säkerhetskopian
Den första säkerhetskopieringen omfattar två viktiga uppgifter:

* Skapa schemat för säkerhetskopiering
* Säkerhetskopiera filer och mappar för första gången

När den första säkerhetskopian är klar i principen för säkerhetskopiering skapar säkerhetskopiering återställningspunkter som du kan använda om du behöver återställa data. Principen för säkerhetskopiering sker enligt det schema som du definierar.

### <a name="to-schedule-the-backup"></a>Så här schemalägger du säkerhetskopieringen
1. Öppna Microsoft Azure Backup-agenten. (Öppnas automatiskt om du lämnar den **starta Microsoft Azure Recovery Services-agenten** markerad när du har stängt guiden registrera Server.) Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Starta Azure Backup-agenten](./media/backup-configure-vault-classic/snap-in-search.png)
2. Klicka på Backup-agenten **schemalägga säkerhetskopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. Klicka på **Nästa** på sidan Komma igång i guiden Schemalägg säkerhetskopiering.
4. På sidan Välj objekt som ska säkerhetskopieras klickar du på **Lägg till objekt**.
5. Markera de filer och mappar som du vill säkerhetskopiera och klicka på **OK**.
6. Klicka på **Nästa**.
7. På sidan **Ange schema för säkerhetskopiering** anger du **säkerhetskopieringsschemat** och klickar på **Nästa**.

    Du kan schemalägga säkerhetskopieringar varje dag (högst tre gånger om dagen) eller varje vecka.

    ![Windows Server Backup-objekt](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > Mer information om hur du anger säkerhetskopieringsschemat finns i artikeln [Använda Azure Backup för att ersätta en bandbaserad infrastruktur](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. På sidan **Välj bevarandeprincip** väljer du **bevarandeprincipen** för säkerhetskopian.

    Bevarandeprincipen anger hur länge säkerhetskopian ska lagras. I stället för att bara ange en ”platt princip” för alla säkerhetskopieringspunkter kan du ange olika bevarandeprinciper baserat på när säkerhetskopieringen utförs. Du kan ändra de dagliga, veckovisa, månatliga och årliga bevarandeprinciperna för att uppfylla dina behov.
9. Välj den första säkerhetskopieringstypen på sidan Välj inledande säkerhetskopieringstyp. Lämna alternativet **Automatiskt över nätverket** markerat och klicka på **Nästa**.

    Du kan säkerhetskopiera automatiskt över nätverket, eller så kan du säkerhetskopiera offline. Resten av den här artikeln beskriver hur du säkerhetskopierar automatiskt. Om du föredrar att säkerhetskopiera offline läser du artikeln [Arbetsflöde för säkerhetskopiering offline i Azure Backup](backup-azure-backup-import-export.md) för ytterligare information.
10. Läs informationen på sidan Bekräftelse och klicka sedan på **Slutför**.
11. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

### <a name="enable-network-throttling-optional"></a>Aktivera begränsning av (valfritt)
Säkerhetskopieringsagenten ger nätverksbegränsning. Begränsningskontroller hur nätverksbandbredden används när data överfördes. Den här kontrollen kan vara användbart om du behöver säkerhetskopiera data under arbetstid, men inte vill säkerhetskopieringsprocessen störa andra Internet-trafik. Begränsning gäller för att säkerhetskopiera och återställa aktiviteter.

**Aktivera nätverksbegränsning av**

1. Klicka på Backup-agenten **ändra egenskaper för**.

    ![Ändra egenskaper för](./media/backup-configure-vault-classic/change-properties.png)
2. På den **begränsning** väljer den **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder** kryssrutan.

    ![Nätverksbegränsning](./media/backup-configure-vault-classic/throttling-dialog.png)
3. När du har aktiverat begränsning, ange tillåtna bandbredd för överföring av säkerhetskopierade data under **arbetstid** och **icke-arbetstid**.

    Värdena bandbredd börja på 512 kilobit per sekund (Kbps) och gå upp till 1,023 megabyte per sekund (MBps). Du kan också ange början och avslutas för **arbetstid**, och vilka dagar i veckan som anses vara arbetsdagar. Timmar utanför avsedda arbete timmar anses fungerar timmar.
4. Klicka på **OK**.

### <a name="to-back-up-now"></a>Säkerhetskopiera nu
1. Klicka på Backup-agenten **Säkerhetskopiera nu** att slutföra den inledande seeding över nätverket.

    ![Säkerhetskopiera Windows Server nu](./media/backup-configure-vault-classic/backup-now.png)
2. Gå igenom inställningarna på sidan Bekräftelse som guiden Säkerhetskopiera nu ska använda för att säkerhetskopiera datorn. Klicka på **Säkerhetskopiera**.
3. Stäng guiden genom att klicka på **Stäng**. Om du gör detta innan säkerhetskopieringen är klar fortsätter guiden att köra i bakgrunden.

När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

![IR slutfört](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>Nästa steg
* Registrera dig för en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

För ytterligare information om hur du säkerhetskopierar virtuella datorer eller andra arbetsbelastningar, se:

* [Säkerhetskopiera virtuella IaaS-datorer](backup-azure-vms-prepare.md)
* [Säkerhetskopiera arbetsbelastningar till Azure med Microsoft Azure Backup-Server](backup-azure-microsoft-azure-backup.md)
* [Säkerhetskopiera arbetsbelastningar till Azure med DPM](backup-azure-dpm-introduction.md)
