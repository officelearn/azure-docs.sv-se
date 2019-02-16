---
title: Säkerhetskopiera filer och mappar med hjälp av Azure Backup-agenten
description: Säkerhetskopiera Windows-filer och mappar till Azure med hjälp av Microsoft Azure Backup-agenten. Skapa ett Recovery Services-valv, installera säkerhetskopieringsagenten, definiera principen för säkerhetskopiering och kör den första säkerhetskopieringen för filer och mappar.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/5/2018
ms.author: raynew
ms.openlocfilehash: 006d47d397bab0869ae8a75d6c17d239e71608c3
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310583"
---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Säkerhetskopiera en Windows-server eller -klient till Azure med hjälp av Resource Manager-distributionsmodellen
Den här artikeln förklarar hur du säkerhetskopierar Windows Server (eller Windows-klienten) filer och mappar till Azure med Azure Backup med hjälp av Resource Manager-distributionsmodellen.

![Säkerhetskopieringsprocessen steg](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Innan du börjar
Om du vill säkerhetskopiera en server eller klient till Azure, behöver du ett Azure-konto. Om du inte har någon kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv
Ett Recovery Services-valv är en entitet som lagrar alla säkerhetskopior och återställningspunkter som du skapar med tiden. Recovery Services-valvet innehåller också säkerhetskopieringspolicyn som tillämpas på de skyddade filer och mappar. När du skapar ett Recovery Services-valv, bör du också välja alternativet för lämpliga redundans.

### <a name="to-create-a-recovery-services-vault"></a>Så här skapar du ett Recovery Services-valv
1. Om du inte redan gjort det loggar du in på [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.
2. På navigeringsmenyn klickar du på **Alla tjänster** och skriver **Recovery Services** i listan över resurser och klickar sedan på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Om det finns Recovery Services-valv i prenumerationen visas valven.

3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

    ![Skapa Recovery Services-valv (steg 3)](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.

5. I avsnittet **Prenumeration** använder du listrutan för att välja Azure-prenumerationen. Om du bara använder en prenumeration visas den och du kan gå vidare till nästa steg. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.

6. Gör följande i avsnittet **Resursgrupp**:

    * Klicka på den **Välj befintlig...**  nedrullningsbara menyn för att visa listan över resursgrupper.
    Eller
    * Välj **Skapa nytt** om du vill skapa en ny resursgrupp.

  Fullständig information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Klicka på **Plats** för att välja en geografisk region för valvet. Det här alternativet anger den geografiska region som dina säkerhetskopierade data skickas till.

8. Längst ned på bladet för Recovery Services-valvet klickar du på **Skapa**.

  Det kan ta flera minuter innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv efter ett par minuter klickar du på **Uppdatera**.

  ![Klicka på Uppdatera](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

  När du ser valvet i listan över Recovery Services-valv kan du ange lagringsredundansen.


### <a name="set-storage-redundancy"></a>Ange lagringsredundans
Första gången du skapar ett Recovery Services-valv bestämmer du hur lagringen ska replikeras.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet.

    ![Välj det nya valvet i listan över Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault.png)

    När du väljer valvet, Recovery Services-valv bladet valvinformation och **översikt** bladet (*som har namnet på valvet överst*) och bladet med valvinformation öppen.

    ![Visa lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-overview.png)

2. I det nya valvet under den **inställningar** går du till avsnittet **egenskaper**.

  Den **egenskaper** blad öppnas.

3. I den **egenskaper** bladet klickar du på **uppdatering** under **Säkerhetskopieringskonfigurationen** bladet. Den **Säkerhetskopieringskonfigurationen** blad öppnas.

  ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

4. Välj lämplig med alternativet för lagringsreplikering för ditt valv och klicka på **spara**.

  ![alternativ för lagringskonfiguration](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

  Valvet använder geo-redundant lagring som standard. Om du använder Azure som primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda **geo-redundant** lagring. Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) i denna [översikt av lagringsredundans](../storage/common/storage-redundancy.md).

Nu när du har skapat ett valv kan förbereda infrastrukturen för att säkerhetskopiera filer och mappar genom att hämta och installera Microsoft Azure Recovery Services-agenten, laddar ned valvautentiseringsuppgifter och sedan använda dessa autentiseringsuppgifter för att registrera agenten med den valvet.

## <a name="configure-the-vault"></a>Konfigurera valvet

1. På bladet Recovery Services-valv (för valvet som du precis har skapat) klickar du på **Säkerhetskopiering** i avsnittet Komma igång och väljer sedan **Säkerhetskopieringsmål** på bladet **Kom igång med säkerhetskopiering**.

  ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  Bladet **Säkerhetskopieringsmål** öppnas. Om Recovery Services-valvet har konfigurerats tidigare, kommer **säkerhetskopieringsmål** blad öppnas när du klickar på **Backup** på Recovery Services-valv bladet.

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


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

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

Om din dator/proxy har begränsad tillgång till internet, kontrollerar du att brandväggsinställningarna på dator/proxy har konfigurerats för att tillåta följande webbadresser: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.net


## <a name="create-the-backup-policy"></a>Skapa säkerhetskopieringsprincipen
Säkerhetskopieringspolicyn är schemat när återställningspunkter skapas och hur lång tid som återställningspunkterna bevaras. Använd Microsoft Azure Backup-agenten för att skapa principen för säkerhetskopiering för filer och mappar.

### <a name="to-create-a-backup-schedule"></a>Skapa ett schema för säkerhetskopiering

Ange schemat för säkerhetskopiering på den dator du vill säkerhetskopiera. Observera att den tid som angetts för säkerhetskopiering kan skilja sig från den lokala datortid, eftersom Azure Backup inte ta sommartid (DST) till konto.
1. Öppna Microsoft Azure Backup-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Starta Azure Backup-agenten](./media/backup-configure-vault/snap-in-search.png)
2. I Backup-agenten **åtgärder** fönstret klickar du på **Schemalägg säkerhetskopiering** att starta guiden Schemalägg säkerhetskopiering.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)

3. På den **komma igång** sidan i guiden Schemalägg säkerhetskopiering klickar du på **nästa**.
4. På den **Välj objekt som ska säkerhetskopieras** klickar du på **Lägg till objekt**.

  Dialogrutan Välj objekt öppnas.

5. Markera de filer och mappar som du vill skydda och klicka sedan på **OK**.
6. I den **Välj objekt som ska säkerhetskopieras** klickar du på **nästa**.
7. På den **Ange schema för säkerhetskopiering** anger schemat för säkerhetskopiering och klicka på **nästa**.

    Du kan schemalägga säkerhetskopieringar varje dag (högst tre gånger om dagen) eller varje vecka.

    ![Windows Server Backup-objekt](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > Mer information om hur du anger säkerhetskopieringsschemat finns i artikeln [Använda Azure Backup för att ersätta en bandbaserad infrastruktur](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. På den **välja bevarandeprincip** väljer du de specifika bevarandeprinciper den för säkerhetskopian och klicka på **nästa**.

    Bevarandeprincipen anger hur länge där säkerhetskopian lagras. I stället för att bara ange en ”platt princip” för alla säkerhetskopieringspunkter kan du ange olika bevarandeprinciper baserat på när säkerhetskopieringen utförs. Du kan ändra de dagliga, veckovisa, månatliga och årliga bevarandeprinciperna för att uppfylla dina behov.
9. Välj den första säkerhetskopieringstypen på sidan Välj inledande säkerhetskopieringstyp. Lämna alternativet **Automatiskt över nätverket** markerat och klicka på **Nästa**.

    Du kan säkerhetskopiera automatiskt över nätverket, eller så kan du säkerhetskopiera offline. Resten av den här artikeln beskriver hur du säkerhetskopierar automatiskt. Om du föredrar att säkerhetskopiera offline läser du artikeln [Arbetsflöde för säkerhetskopiering offline i Azure Backup](backup-azure-backup-import-export.md) för ytterligare information.
10. Läs informationen på sidan Bekräftelse och klicka sedan på **Slutför**.
11. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

### <a name="enable-network-throttling"></a>Aktivera nätverksbegränsning
Microsoft Azure Backup-agenten tillhandahåller nätverksbegränsning. Begränsningskontroller hur nätverksbandbredden används under dataöverföringar. Den här kontrollen kan vara användbart om du vill säkerhetskopiera data under arbetstid, men inte vill att säkerhetskopieringsprocessen ska störa annan Internettrafik. Begränsning gäller för att säkerhetskopiera och återställa aktiviteter.

> [!NOTE]
> Nätverksbegränsning är inte tillgänglig på Windows Server 2008 R2 SP1, Windows Server 2008 SP2 eller Windows 7 (utan servicepack). Azure Backup-funktionen för nätverksbegränsning aktiverar tjänstkvalitet (QoS) på det lokala operativsystemet. Även om Azure Backup kan skydda dessa operativsystem, fungerar inte versionen av QoS som är tillgängliga i de här plattformarna med Azure Backup nätverksbegränsning. Nätverksbegränsning kan användas på alla andra [operativsystem som stöds](backup-azure-backup-faq.md).
>
>

**Aktivera nätverksbegränsning**

1. I Microsoft Azure Backup-agenten klickar du på **ändra egenskaper för**.

    ![Ändra egenskaper](./media/backup-configure-vault/change-properties.png)
2. På den **begränsning** fliken den **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder** markerar du kryssrutan.

    ![Nätverksbegränsning](./media/backup-configure-vault/throttling-dialog.png)
3. När du har aktiverat begränsning, ange tillåtna bandbredd för överföring av säkerhetskopierade data under **arbetstimmar** och **icke-arbetstid**.

    Bandbreddsvärden börjar på 512 kilobit per sekund (kbit/s) och kan gå upp till 1,023 megabyte per sekund (MBps). Du kan också ange början och avslutas för **arbetstimmar**, och vilka dagar i veckan är räknas som arbetsdagar. Timmar utanför avsedda work timmar betraktas som icke-arbete timmar.
4. Klicka på **OK**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Så här säkerhetskopierar du filer och mappar för första gången
1. I backup-agenten klickar du på **Säkerhetskopiera nu** att slutföra en inledande seeding över nätverket.

    ![Säkerhetskopiera Windows Server nu](./media/backup-configure-vault/backup-now.png)
2. Gå igenom inställningarna på sidan Bekräftelse som guiden Säkerhetskopiera nu ska använda för att säkerhetskopiera datorn. Klicka på **Säkerhetskopiera**.
3. Stäng guiden genom att klicka på **Stäng**. Om du gör detta innan säkerhetskopieringen är klar fortsätter guiden att köra i bakgrunden.

När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

![IR slutfört](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>Har du några frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du säkerhetskopierar virtuella datorer eller andra arbetsbelastningar finns i:

* Nu när du har säkerhetskopierat dina filer och mappar kan du [hantera dina valv och servrar](backup-azure-manage-windows-server.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
