---
title: "Använd Azure Backup-agenten att säkerhetskopiera filer och mappar | Microsoft Docs"
description: "Använda Microsoft Azure Backup-agenten för säkerhetskopiering av Windows-filer och mappar till Azure. Skapa ett Recovery Services-valv, installera säkerhetskopieringsagenten, definiera princip för säkerhetskopiering och kör den första säkerhetskopian på filer och mappar."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "säkerhetskopieringsvalvet; Säkerhetskopiera en Windows-server. Säkerhetskopiera windows;"
ms.assetid: 7f5b1943-b3c1-4ddb-8fb7-3560533c68d5
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/5/2018
ms.author: markgal;trinadhk;
ms.openlocfilehash: 89553ee973e4aebc96b60caef9eefa80178fadfb
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2018
---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Säkerhetskopiera en Windows-server eller -klient till Azure med hjälp av Resource Manager-distributionsmodellen
Den här artikeln förklarar hur du säkerhetskopierar Windows Server (eller Windows-klient) filer och mappar till Azure med Azure Backup med hjälp av Resource Manager-distributionsmodellen.

![Säkerhetskopieringsprocessen steg](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Innan du börjar
Om du vill säkerhetskopiera en server eller klient till Azure, behöver du ett Azure-konto. Om du inte har någon, kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
Recovery Services-valvet är en entitet som lagrar alla säkerhetskopior och återställningspunkter som du skapar över tid. Recovery Services-valvet innehåller också den säkerhetskopieringsprincip som tillämpas på skyddade filer och mappar. När du skapar ett Recovery Services-valv, bör du också välja det lämpliga lagringsalternativet för redundans.

### <a name="to-create-a-recovery-services-vault"></a>Så här skapar du ett Recovery Services-valv
1. Om du inte redan gjort det loggar du in på [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.
2. På navigeringsmenyn klickar du på **Fler tjänster** och skriver **Recovery Services** i listan över resurser och klickar sedan på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Om det finns Recovery Services-valv i prenumerationen visas valven.

3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

    ![Skapa Recovery Services-valv (steg 3)](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.

5. I avsnittet **Prenumeration** använder du listrutan för att välja Azure-prenumerationen. Om du bara använder en prenumeration visas den och du kan gå vidare till nästa steg. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.

6. Gör följande i avsnittet **Resursgrupp**:

    * Välj **Skapa nytt** om du vill skapa en ny resursgrupp.
    Eller
    * Välj **Använd befintlig** och klicka på listrutan om du vill se listan över tillgängliga resursgrupper.

  Fullständig information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Klicka på **Plats** för att välja en geografisk region för valvet. Det här alternativet anger den geografiska region som dina säkerhetskopierade data skickas till.

8. Längst ned på bladet för Recovery Services-valvet klickar du på **Skapa**.

  Det kan ta flera minuter innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv efter ett par minuter klickar du på **Uppdatera**.

  ![Klicka på Uppdatera](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

  När du ser valvet i listan över Recovery Services-valv kan du ange lagringsredundansen.


### <a name="set-storage-redundancy"></a>Ange lagring redundans
Första gången du skapar ett Recovery Services-valv bestämmer du hur lagringen ska replikeras.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet.

    ![Välj det nya valvet i listan över Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Om du väljer valvet minimeras bladet **Recovery Services-valv** och bladet Inställningar (*som har namnet på valvet överst*) och bladet med valvinformation öppnas.

    ![Visa lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)

2. På det nya valvets inställningsblad använder du det lodräta reglaget och bläddrar ned till avsnittet Hantera. Där klickar du på **Infrastruktur för säkerhetskopiering**.

  Bladet Infrastruktur för säkerhetskopiering öppnas.

3. På bladet Infrastruktur för säkerhetskopiering klickar du på **Konfiguration av säkerhetskopiering** för att öppna bladet **Konfiguration av säkerhetskopiering**.

  ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)

4. Välj lämpligt alternativ för lagringsreplikering för valvet.

  ![alternativ för lagringskonfiguration](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

  Valvet använder geo-redundant lagring som standard. Om du använder Azure som primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda **geo-redundant** lagring. Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokalt redundant](../storage/common/storage-redundancy.md#locally-redundant-storage) i denna [översikt av lagringsredundans](../storage/common/storage-redundancy.md).

Nu när du har skapat ett valv, förbereda infrastrukturen för att säkerhetskopiera filer och mappar genom att hämta och installera Microsoft Azure Recovery Services-agent, ladda ned valvautentiseringsuppgifter och sedan använda dessa autentiseringsuppgifter för att registrera agenten med valvet.

## <a name="configure-the-vault"></a>Konfigurera valvet

1. På bladet Recovery Services-valv (för valvet som du precis har skapat) klickar du på **Säkerhetskopiering** i avsnittet Komma igång och väljer sedan **Säkerhetskopieringsmål** på bladet **Kom igång med säkerhetskopiering**.

  ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  Bladet **Säkerhetskopieringsmål** öppnas. Om Recovery Services-valvet har konfigurerats tidigare, kommer **säkerhetskopiering målet** blad öppnas när du klickar på **säkerhetskopiering** valvet bladet på Recovery Services.

  ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Från listrutan **Var körs din arbetsbelastning?** väljer du **Lokalt**.

  Du väljer **Lokalt** eftersom din Windows Server- eller Windows-dator är en fysisk dator som inte finns i Azure.

3. Från menyn **Vad vill du säkerhetskopiera?** väljer du **Filer och mappar** och klickar på **OK**.

  ![Konfigurera filer och mappar](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

  När du klickar på OK visas en markering bredvid **Säkerhetskopieringsmål** och bladet **Förbered infrastruktur** öppnas.

  ![När säkerhetskopieringsmålet har konfigurerats går du vidare och förbereder infrastrukturen](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. På bladet **Förbered infrastruktur** klickar du på **Ladda ned agent för Windows Server eller Windows Client**.

  ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

  Om du använder Windows Server Essential väljer du att hämta agenten för Windows Server Essential. En popup-meny visas och du uppmanas att köra eller spara MARSAgentInstaller.exe.

  ![Dialogrutan MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. På snabbmenyn för hämtningen klickar du på **Spara**.

  Som standard sparas filen **MARSagentinstaller.exe** i mappen för nedladdningar. När installationsprogrammet har slutförts visas ett popup-fönster och du tillfrågas om du vill köra installationsprogrammet eller öppna mappen.

  ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

  Du behöver inte installera agenten än. Du kan installera agenten när du har hämtat valvautentiseringsuppgifterna.

6. Klicka på **Ladda ned** på bladet **Förbered infrastruktur**.

  ![hämta autentiseringsuppgifter för valvet](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

  Autentiseringsuppgifterna för valvet hämtas till mappen Hämtningsbara filer. När autentiseringsuppgifterna för valvet har hämtats visas ett popup-fönster och du tillfrågas om du vill öppna eller spara autentiseringsuppgifterna. Klicka på **Spara**. Om du råkar klicka på **Öppna** av misstag väntar du tills dialogrutan som försöker öppna autentiseringsuppgifterna för valvet misslyckas. Du kan inte öppna valvautentiseringsuppgifterna. Gå vidare till nästa steg. Valvautentiseringsuppgifterna finns i mappen Hämtade filer.   

  ![valvautentiseringsuppgifterna har hämtats](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

> [!NOTE]
> Aktivering av säkerhetskopiering via Azure Portal är inte tillgängligt ännu. Använd Microsoft Azure Recovery Services-agenten för att säkerhetskopiera filer och mappar.
>

1. Leta upp och dubbelklicka på **MARSagentinstaller.exe** från mappen för nedladdade filer (eller en annan lagringsplats).

  Installationsprogrammet visar ett antal meddelanden när Recovery Services-agenten extraheras, installeras och registreras.

  ![Autentiseringsuppgifter för att köra Recovery Services-agentinstallationsprogrammet](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

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

## <a name="network-and-connectivity-requirements"></a>Nätverks- och anslutningskrav

Om din dator/proxy har begränsad tillgång till internet, kontrollerar du att brandväggen på datorn/proxy har konfigurerats för att tillåta följande webbadresser: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.net


## <a name="create-the-backup-policy"></a>Skapa säkerhetskopieringsprincipen
Principen för säkerhetskopiering är schemat när återställningspunkter tas och hur lång tid som återställningspunkterna bevaras. Använda Microsoft Azure Backup-agenten för att skapa säkerhetskopieringsprincip för filer och mappar.

### <a name="to-create-a-backup-schedule"></a>Så här skapar du en schemalagd säkerhetskopiering
1. Öppna Microsoft Azure Backup-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Starta Azure Backup-agenten](./media/backup-configure-vault/snap-in-search.png)
2. I Backup-agenten **åtgärder** rutan klickar du på **schemalägga säkerhetskopiering** att starta guiden Schemalägg säkerhetskopiering.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)

3. På den **komma igång** sidan i guiden Schemalägg säkerhetskopiering klickar du på **nästa**.
4. På den **markerar objekt att säkerhetskopiera** klickar du på **Lägg till objekt**.

  Dialogrutan Välj objekt öppnas.

5. Välj de filer och mappar som du vill skydda och klicka sedan på **OK**.
6. I den **markerar objekt att säkerhetskopiera** klickar du på **nästa**.
7. På den **ange schemat för säkerhetskopiering** anger schemat för säkerhetskopiering och klicka på **nästa**.

    Du kan schemalägga säkerhetskopieringar varje dag (högst tre gånger om dagen) eller varje vecka.

    ![Windows Server Backup-objekt](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > Mer information om hur du anger säkerhetskopieringsschemat finns i artikeln [Använda Azure Backup för att ersätta en bandbaserad infrastruktur](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. På den **Välj bevarandeprincip** väljer specifika bevarandeprinciper den för säkerhetskopia och klickar på **nästa**.

    Bevarandeprincipen anger hur länge där säkerhetskopian lagras. I stället för att bara ange en ”platt princip” för alla säkerhetskopieringspunkter kan du ange olika bevarandeprinciper baserat på när säkerhetskopieringen utförs. Du kan ändra de dagliga, veckovisa, månatliga och årliga bevarandeprinciperna för att uppfylla dina behov.
9. Välj den första säkerhetskopieringstypen på sidan Välj inledande säkerhetskopieringstyp. Lämna alternativet **Automatiskt över nätverket** markerat och klicka på **Nästa**.

    Du kan säkerhetskopiera automatiskt över nätverket, eller så kan du säkerhetskopiera offline. Resten av den här artikeln beskriver hur du säkerhetskopierar automatiskt. Om du föredrar att säkerhetskopiera offline läser du artikeln [Arbetsflöde för säkerhetskopiering offline i Azure Backup](backup-azure-backup-import-export.md) för ytterligare information.
10. Läs informationen på sidan Bekräftelse och klicka sedan på **Slutför**.
11. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

### <a name="enable-network-throttling"></a>Aktivera begränsning
Microsoft Azure Backup agent innehåller nätverksbegränsning. Begränsningskontroller hur nätverksbandbredden används när data överfördes. Den här kontrollen kan vara användbart om du behöver säkerhetskopiera data under arbetstid, men inte vill säkerhetskopieringsprocessen störa andra Internet-trafik. Begränsning gäller för att säkerhetskopiera och återställa aktiviteter.

> [!NOTE]
> Nätverksbegränsning är inte tillgänglig på Windows Server 2008 R2 SP1, Windows Server 2008 SP2 eller Windows 7 (utan servicepack). Azure Backup-nätverket begränsning funktionen aktiverar tjänstkvalitet (QoS) på det lokala operativsystemet. Även om Azure Backup kan skydda dessa operativsystem, fungerar inte versionen av QoS finns på dessa plattformar med Azure Backup nätverksbegränsning. Nätverksbegränsning kan användas på alla andra [operativsystem](backup-azure-backup-faq.md).
>
>

**Aktivera nätverksbegränsning av**

1. Klicka i Microsoft Azure Backup-agenten **ändra egenskaper för**.

    ![Ändra egenskaper för](./media/backup-configure-vault/change-properties.png)
2. På den **begränsning** väljer den **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder** kryssrutan.

    ![Nätverksbegränsning](./media/backup-configure-vault/throttling-dialog.png)
3. När du har aktiverat begränsning, ange tillåtna bandbredd för överföring av säkerhetskopierade data under **arbetstid** och **icke-arbetstid**.

    Värdena bandbredd börja på 512 kilobit per sekund (Kbps) och gå upp till 1,023 megabyte per sekund (MBps). Du kan också ange början och avslutas för **arbetstid**, och vilka dagar i veckan som anses vara arbetsdagar. Timmar utanför avsedda arbete timmar anses fungerar timmar.
4. Klicka på **OK**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Så här säkerhetskopierar du filer och mappar för första gången
1. Klicka på backup-agenten **Säkerhetskopiera nu** att slutföra den inledande seeding över nätverket.

    ![Säkerhetskopiera Windows Server nu](./media/backup-configure-vault/backup-now.png)
2. Gå igenom inställningarna på sidan Bekräftelse som guiden Säkerhetskopiera nu ska använda för att säkerhetskopiera datorn. Klicka på **Säkerhetskopiera**.
3. Stäng guiden genom att klicka på **Stäng**. Om du gör detta innan säkerhetskopieringen är klar fortsätter guiden att köra i bakgrunden.

När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

![IR slutfört](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>Har du några frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
För ytterligare information om hur du säkerhetskopierar virtuella datorer eller andra arbetsbelastningar, se:

* Nu när du har säkerhetskopierat dina filer och mappar kan du [hantera dina valv och servrar](backup-azure-manage-windows-server.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
