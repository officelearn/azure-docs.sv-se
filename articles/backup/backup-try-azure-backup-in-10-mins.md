---
title: "Lär dig hur du säkerhetskopierar filer och mappar från Windows till Azure med Azure Backup med hjälp av Resource Manager-distributionsmodellen | Microsoft Docs"
description: "Lär dig hur du säkerhetskopierar Windows Server-data genom att skapa ett valv, installera Recovery Services-agenten och säkerhetskopiera dina filer och mappar till Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keywords: "så här säkerhetskopierar du, säkerhetskopiera så här"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 900967975694a688b6d5054cb351746819b65f16


---
# <a name="first-look-back-up-files-and-folders-with-azure-backup-using-the-resource-manager-deployment-model"></a>En första titt: Säkerhetskopiera filer och mappar med Azure Backup och med hjälp av Resource Manager-distributionsmodellen
Den här artikeln förklarar hur du säkerhetskopierar filer och mappar i Windows Server (eller en Windows-klient) till Azure med Azure Backup med hjälp av Resource Manager. I den här självstudiekursen går vi igenom grunderna. Om du vill komma igång med Azure Backup är du på rätt ställe.

Om du vill veta mer om Azure Backup läser du den här [översikten](backup-introduction-to-azure-backup.md).

Följande aktiviteter krävs när du säkerhetskopierar filer och mappar till Azure:

![Steg 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Skaffa en Azure-prenumeration (om du inte redan har en).<br>
![Steg 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Skapa ett Recovery Services-valv.<br>
![Steg 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Ladda ned de nödvändiga filerna.<br>
![Steg 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Installera och registrera Recovery Services-agenten.<br>
![Steg 5](./media/backup-try-azure-backup-in-10-mins/step-5.png) Säkerhetskopiera dina filer och mappar.

![Säkerhetskopiera Windows-datorn med Azure Backup](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## <a name="step-1-get-an-azure-subscription"></a>Steg 1: Skaffa en Azure-prenumeration
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) som ger dig åtkomst till Azure-tjänsten.

## <a name="step-2-create-a-recovery-services-vault"></a>Steg 2: Skapa ett Recovery Services-valv
Innan du kan säkerhetskopiera filer och mappar måste du skapa ett Recovery Services-valv i den region som du vill lagra informationen i. Du måste också bestämma hur du vill att lagringen ska replikeras.

### <a name="to-create-a-recovery-services-vault"></a>Så här skapar du ett Recovery Services-valv
1. Om du inte redan gjort det loggar du in på [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.
2. På navmenyn klickar du på **Bläddra** och skriver **Recovery Services** i listan över resurser och klickar sedan på **Recovery Services-valv**.
   
    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/browse-to-rs-vaults.png) <br/>
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.
   
    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)
   
    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.
   
    ![Skapa ett Recovery Services-valv (steg 5)](./media/backup-try-azure-backup-in-10-mins/rs-vault-attributes.png)
4. I **Namn** anger du ett eget namn som identifierar valvet.
5. Klicka på **Prenumeration** för att visa listan över prenumerationer.
6. Klicka på **Resursgrupp** för att visa listan över resursgrupper eller klicka på **Nytt** för att skapa en ny resursgrupp.
7. Klicka på **Plats** för att välja en geografisk region för valvet. Det här alternativet anger den geografiska region som dina säkerhetskopierade data skickas till.
8. Klicka på **Skapa**.
   
    Om ditt valv inte visas när det har skapats klickar du på **Uppdatera**. När listan har uppdaterats klickar du på namnet på valvet.

### <a name="to-determine-storage-redundancy"></a>Så här fastställer du lagringsredundans
Första gången du skapar ett Recovery Services-valv bestämmer du hur lagringen ska replikeras.

1. Öppna instrumentpanelen genom att klicka på det nya valvet.
2. På bladet **Inställningar**, som öppnas automatiskt med instrumentpanelen för valet, klickar du på **Infrastruktur för säkerhetskopiering**.
3. På bladet Infrastruktur för säkerhetskopiering klickar du på **Konfiguration av säkerhetskopiering** för att visa **Lagringsreplikeringstyp**.
   
    ![Skapa ett Recovery Services-valv (steg 5)](./media/backup-try-azure-backup-in-10-mins/backup-infrastructure.png)
4. Välj lämpligt alternativ för lagringsreplikering för valvet.
   
    ![Lista över Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)
   
    Valvet använder geo-redundant lagring som standard. Om du använder Azure som en primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda geo-redundant lagring. Om du använder Azure som en sekundär slutpunkt för lagring av säkerhetskopior väljer du lokalt redundant lagring, vilket minskar kostnaden för datalagring i Azure. Läs mer om alternativen för [geo-redundant](../storage/storage-redundancy.md#geo-redundant-storage) och [lokalt redundant](../storage/storage-redundancy.md#locally-redundant-storage) lagring i den här [översikten](../storage/storage-redundancy.md).

Nu när du har skapat ett valv förbereder du infrastrukturen för säkerhetskopiering av filer och mappar genom att ladda ned agenten och autentiseringsuppgifterna för Microsoft Azure Recovery Services.

## <a name="step-3-download-files"></a>Steg 3 – Ladda ned filer
1. Klicka på **Inställningar** på instrumentpanelen för Recovery Services-valvet.
   
    ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/settings-button.png)
2. Klicka på **Komma igång > Säkerhetskopiering** på bladet Inställningar.
   
    ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/getting-started-backup.png)
3. Klicka på **Säkerhetskopieringsmål** på bladet Säkerhetskopiering.
   
    ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/backup-goal.png)
4. Välj **Lokalt** från menyn Var körs din arbetsbelastning?
5. Välj **Filer och mappar** från menyn Vad vill du säkerhetskopiera? och klicka på **OK**.

### <a name="download-the-recovery-services-agent"></a>Ladda ned Recovery Services-agenten
1. Klicka på **Ladda ned agent för Windows Server eller Windows Client** på bladet **Förbered infrastruktur**.
   
    ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-short.png)
2. Klicka på **Spara** i popup-fönstret för nedladdningen. Som standard sparas filen **MARSagentinstaller.exe** i mappen för nedladdningar.

### <a name="download-vault-credentials"></a>Ladda ned autentiseringsuppgifter för valvet
1. Klicka på **Ladda ned > Spara** på bladet Förbered infrastruktur.
   
    ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-download.png)

## <a name="step-4-install-and-register-the-agent"></a>Steg 4 – Installera och registrera agenten
> [!NOTE]
> Snart kan du aktivera säkerhetskopiering via Azure-portalen. Tills dess använder du Microsoft Azure Recovery Services-agenten lokalt för att säkerhetskopiera filer och mappar.
> 
> 

1. Leta upp och dubbelklicka på **MARSagentinstaller.exe** från mappen för nedladdade filer (eller en annan lagringsplats).
2. Slutför installationsguiden för Microsoft Azure Recovery Services Agent. För att slutföra guiden måste du:
   
   * Välja en plats för installationen och cachelagringsmappen.
   * Ange information om proxyservern om du använder en proxyserver för att ansluta till Internet.
   * Ange ditt användarnamn och lösenord om du använder en autentiserad proxyserver.
   * Ange autentiseringsuppgifterna som du hämtat för valvet.
   * Spara krypteringslösenfrasen på en säker plats.
     
     > [!NOTE]
     > Om du tappar bort eller glömmer lösenfrasen kan Microsoft inte hjälpa dig att återställa dina säkerhetskopierade data. Spara filen på en säker plats. Det krävs för att återställa en säkerhetskopia.
     > 
     > 

Nu installeras agenten och datorn registreras i valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.

## <a name="step-5-back-up-your-files-and-folders"></a>Steg 5: Säkerhetskopiera dina filer och mappar
Den första säkerhetskopieringen omfattar två viktiga uppgifter:

* Schemalägg säkerhetskopieringen
* Säkerhetskopiera filer och mappar för första gången

För att slutföra den första säkerhetskopieringen använder du Microsoft Azure Recovery Services-agenten.

### <a name="to-schedule-the-backup"></a>Så här schemalägger du säkerhetskopieringen
1. Öppna Microsoft Azure Recovery Services-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.
   
    ![Starta Azure Recovery Services-agenten](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)
2. Klicka på Recovery Services-agenten och sedan på **Schemalägg säkerhetskopiering**.
   
    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)
3. Klicka på **Nästa** på sidan Komma igång i guiden Schemalägg säkerhetskopiering.
4. På sidan Välj objekt som ska säkerhetskopieras klickar du på **Lägg till objekt**.
5. Markera de filer och mappar som du vill säkerhetskopiera och klicka på **OK**.
6. Klicka på **Nästa**.
7. På sidan **Ange schema för säkerhetskopiering** anger du **säkerhetskopieringsschemat** och klickar på **Nästa**.
   
    Du kan schemalägga säkerhetskopieringar varje dag (högst tre gånger om dagen) eller varje vecka.
   
    ![Windows Server Backup-objekt](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)
   
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

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Så här säkerhetskopierar du filer och mappar för första gången
1. I Recovery Services-agenten klickar du på **Säkerhetskopiera nu** för att slutföra en inledande seeding över nätverket.
   
    ![Säkerhetskopiera Windows Server nu](./media/backup-try-azure-backup-in-10-mins/backup-now.png)
2. Gå igenom inställningarna på sidan Bekräftelse som guiden Säkerhetskopiera nu ska använda för att säkerhetskopiera datorn. Klicka på **Säkerhetskopiera**.
3. Stäng guiden genom att klicka på **Stäng**. Om du gör detta innan säkerhetskopieringen är klar fortsätter guiden att köra i bakgrunden.

När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

![IR slutfört](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Har du några frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
* Få mer information om hur du [säkerhetskopierar Windows-datorer](backup-configure-vault.md).
* Nu när du har säkerhetskopierat dina filer och mappar kan du [hantera dina valv och servrar](backup-azure-manage-windows-server.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).




<!--HONumber=Nov16_HO2-->


