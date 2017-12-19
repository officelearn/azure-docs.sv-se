---
title: "Använda DPM för att säkerhetskopiera arbetsbelastningar till Azure-portalen | Microsoft Docs"
description: "En introduktion till att säkerhetskopiera DPM-servrar med Azure Backup-tjänsten"
services: backup
documentationcenter: 
author: adigan
manager: nkolli
editor: 
keywords: "System Center Data Protection Manager data protection manager, dpm-säkerhetskopiering"
ms.assetid: c8c322cf-f5eb-422c-a34c-04a4801bfec7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: c22e6fc85e88d89007107c8c3bad142ac91e9d12
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Förbereder för att säkerhetskopiera arbetsbelastningar till Azure med DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Den här artikeln innehåller en introduktion till Microsoft Azure Backup för att skydda dina System Center Data Protection Manager (DPM) servrar och arbetsbelastningar. Genom att läsa det, måste du förstå:

* Hur fungerar Azure DPM server-säkerhetskopiering
* Förutsättningar för att uppnå en smidig säkerhetskopiering upplevelse
* Vanliga fel som inträffar och hur du hanterar dem.
* Scenarier som stöds

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln innehåller information och procedurer för att återställa virtuella datorer som distribueras med hjälp av Resource Manager-modellen.
>
>

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) säkerhetskopierar filer och programdata. Mer information om arbetsbelastningar som stöds finns [här](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Data som säkerhetskopieras så att DPM kan lagras på band på disk, eller säkerhetskopiera till Azure med Microsoft Azure Backup. DPM samverkar med Azure Backup på följande sätt:

* **DPM distribueras som en fysisk server eller lokal virtuell dator** – om DPM distribueras som en fysisk server eller en lokal Hyper-V virtuell dator som du kan säkerhetskopiera data till ett Recovery Services-valv förutom disk och band säkerhetskopiering.
* **DPM distribueras som en virtuell Azure-dator** – från System Center 2012 R2 med uppdatering 3 kan DPM distribueras som en virtuell Azure-dator. Om DPM distribueras som en virtuell Azure-dator kan du säkerhetskopiera data till Azure-diskar kopplade till den virtuella DPM Azure-datorn eller du omfördela lagring av data genom att säkerhetskopiera upp till Recovery Services-valvet.

## <a name="why-backup-from-dpm-to-azure"></a>Varför säkerhetskopiera från DPM till Azure?
Företagets fördelar med hjälp av Azure Backup för att säkerhetskopiera DPM-servrar är:

* Du kan använda Azure som ett alternativ till långsiktig distribution till band för lokal DPM-distribution.
* För DPM-distributioner i Azure kan Azure Backup du omfördela lagring från Azure-disken så att du kan skala upp genom att lagra äldre data i Recovery Services-valvet och nya data på disken.

## <a name="prerequisites"></a>Krav
Förbered Azure Backup för att säkerhetskopiera DPM-data på följande sätt:

1. **Skapa ett Recovery Services-valv** – skapa ett valv i Azure-portalen.
2. **Ladda ned valvautentiseringsuppgifter** – hämta autentiseringsuppgifter som du använder för att registrera DPM-servern till Recovery Services-valvet.
3. **Installera Azure-säkerhetskopieringsagenten** – installera agenten på varje DPM-servern från Azure Backup.
4. **Registrera servern** – registrera DPM-servern till Recovery Services-valvet.

## <a name="key-definitions"></a>Viktiga definitioner
Här följer några viktiga definitioner för säkerhetskopiering till Azure för DPM:

1. **Valvet autentiseringsuppgifter** – Valvautentiseringsuppgifter som behövs för att autentisera datorn för att skicka säkerhetskopierade data till ett identifierade valv i Azure Backup-tjänsten. Det kan hämtas från valvet och är giltig för 48hrs.
2. **Lösenfrasen** – lösenfras som används för att kryptera säkerhetskopiorna till molnet. Spara filen på en säker plats när den behövs under en återställning.
3. **Säkerhet PIN-kod** – om du har aktiverat den [säkerhetsinställningar](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) av valvet, säkerhet PIN-kod krävs för att utföra kritiska säkerhetskopieringsåtgärder. Den här multifaktorautentisering lägger till ytterligare en säkerhetsnivå. 
4. **Återställningsmappen** – det är frasen som säkerhetskopior från molnet hämtas tillfälligt till under molnet återställningar. Storleken ska ungefär vara lika med storleken på de säkerhetskopiera objekten som du vill återställa parallellt.


### <a name="1-create-a-recovery-services-vault"></a>1. Skapa ett Recovery Services-valv
Så här skapar du ett Recovery Services-valv:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På navmenyn klickar du på **Bläddra** och i listan över resurser skriver du **Recovery Services**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-dpm-introduction/open-recovery-services-vault.png)

    Listan över Recovery Services-valv visas.
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-dpm-introduction/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

    ![Skapa ett Recovery Services-valv (steg 5)](./media/backup-azure-dpm-introduction/rs-vault-attributes.png)
4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.
5. Klicka på **Prenumeration** för att visa listan över prenumerationer. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.
6. Klicka på **Resursgrupp** för att visa listan över resursgrupper eller klicka på **Nytt** för att skapa en ny resursgrupp. För komplett information om resursgrupper, se [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Klicka på **Plats** för att välja en geografisk region för valvet.
8. Klicka på **Skapa**. Det kan ta en stund innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger på portalen.
   När du har skapat ditt valv öppnas i portalen.

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering
Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Lämna alternativet inställt på geo-redundant lagring om det här är din primära säkerhetskopia. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokalt redundant](../storage/common/storage-redundancy.md#locally-redundant-storage) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj ditt valv för att öppna instrumentpanelen för valvet och bladet Inställningar. Om bladet **Inställningar** inte öppnas klickar du på **Alla inställningar** på instrumentpanelen för valvet.
2. På bladet **Inställningar** klickar du på **Infrastruktur för säkerhetskopiering** > **Konfiguration av säkerhetskopiering** för att öppna bladet **Konfiguration av säkerhetskopiering**. På bladet **Konfiguration av säkerhetskopiering** väljer du alternativet för lagringsreplikering för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    När du har valt lagringsalternativet för valvet är det dags att associera den virtuella datorn med valvet. För att börja kopplingen identifierar du och registrerar de virtuella Azure-datorerna.

### <a name="2-download-vault-credentials"></a>2. Ladda ned autentiseringsuppgifter för valvet
Valvautentiseringsfilen är ett certifikat som genereras av portalen för varje säkerhetskopieringsvalv. Portalen överför sedan den offentliga nyckeln till Access Control Service (ACS). Den privata nyckeln för certifikatet görs tillgänglig för användaren som en del av arbetsflödet som angetts som indata i arbetsflöde för registrering av datorn. Detta autentiserar datorn för att skicka säkerhetskopierade data till ett identifierade valv i Azure Backup-tjänsten.

Valvautentiseringen används endast under registreringsarbetsflödet. Det är användarens ansvar att se till att valvautentiseringsfilen inte äventyras. Om den hamnar i händerna på en obehörig användare kan valvautentiseringsfilen användas för att registrera andra datorer mot samma valv. Men eftersom den säkerhetskopiera informationen krypteras med en lösenfras som hör till kunden, kan inte befintlig säkerhetskopierad data vara hotad. Valvautentiseringsuppgifter är inställda att gälla inom 48hrs för att minska risken för detta. Du kan ladda ned valvautentiseringsuppgifter av en återställningstjänster valfritt antal gånger – men endast det senaste valvautentiseringsfilen gäller under registreringen av arbetsflödet.

Valvautentiseringsfilen hämtas via en säker kanal från Azure-portalen. Azure Backup-tjänsten inte känner till den privata nyckeln för certifikatet och den privata nyckeln beständig inte i portalen eller tjänsten. Använd följande steg för att hämta valvautentiseringsfilen till en lokal dator.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Öppna Recovery Services-valvet som du vill registrera DPM-datorn.
3. Inställningsbladet öppnas som standard. Om den är stängd klickar du på **inställningar** på valvet instrumentpanelen för att öppna inställningsbladet. I inställningar-bladet klickar du på **egenskaper**.

    ![Öppna bladet för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
4. På sidan Egenskaper **hämta** under **säkerhetskopiering autentiseringsuppgifter**. Portalen genererar valvautentiseringsfilen som görs tillgänglig för hämtning.

    ![Ladda ned](./media/backup-azure-dpm-introduction/vault-credentials.png)

Portalen kommer att generera en valvautentiseringen med hjälp av en kombination av valvnamnet och det aktuella datumet. Klicka på **spara** att hämta autentiseringsuppgifter för valv till det lokala kontot hämtningsmapp eller välj Spara som Spara-menyn för att ange en plats för autentiseringsuppgifter för valv. Det kan ta upp till en minut för filen som ska genereras.

### <a name="note"></a>Obs!
* Se till att valvautentiseringsfilen sparas på en plats som kan nås från datorn. Om den är lagrad i en fil resursen/SMB Kontrollera åtkomstbehörigheten.
* Valvautentiseringsfilen används endast under registreringen av arbetsflödet.
* Valvautentiseringsfilen upphör att gälla efter 48hrs och kan hämtas från portalen.

### <a name="3-install-backup-agent"></a>3. Installera säkerhetskopieringsagenten
När du har skapat Azure Backup-valvet, ska en agent installeras på var och en av dina Windows-datorer (Windows Server, Windows-klient, System Center Data Protection Manager-server eller Azure Backup Server-dator) som möjliggör säkerhetskopiering av data och program till Azure .

1. Öppna Recovery Services-valvet som du vill registrera DPM-datorn.
2. Inställningsbladet öppnas som standard. Om den är stängd klickar du på **inställningar** att öppna inställningsbladet. I inställningar-bladet klickar du på **egenskaper**.

    ![Öppna bladet för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. På sidan Inställningar **hämta** under **Azure Backup-agenten**.

    ![Ladda ned](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   När agenten har hämtats kan köra MARSAgentInstaller.exe om du vill starta installationen av Azure Backup-agenten. Välj installationsmappen och tillfälliga mapp som krävs för agenten. Cacheplats som anges måste ha ledigt utrymme som är minst 5% av säkerhetskopierade data.
4. Om du använder en proxyserver för att ansluta till internet, i den **proxykonfiguration** anger information för proxy-server. Om du använder en autentiserad proxyserver måste du ange användarinformation namn och lösenord i den här skärmen.
5. Azure Backup-agenten installerar .NET Framework 4.5 och Windows PowerShell (om det inte är tillgänglig) om du vill slutföra installationen.
6. När agenten har installerats **Stäng** fönstret.

   ![Stäng](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)
7. Att **registrera DPM-servern** till valvet, i den **Management** fliken, klicka på **Online**. Markera **registrera**. Guiden registrera öppnas.
8. Om du använder en proxyserver för att ansluta till internet, i den **proxykonfiguration** anger information för proxy-server. Om du använder en autentiserad proxyserver måste du ange användarinformation namn och lösenord i den här skärmen.

    ![Proxykonfiguration](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Bläddra till och markera valvautentiseringsfilen som tidigare har hämtat på skärmen valvet autentiseringsuppgifter.

    ![Valvautentiseringsuppgifter](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    Valvautentiseringsfilen är endast giltig för 48 timmar (när den har hämtats från portalen). Om det uppstår något fel i den här skärmen (till exempel ”valvautentiseringsuppgifter tillhandahållna filen har upphört att gälla”), logga in på Azure portal och ladda ned autentiseringsuppgifter för valv filen igen.

    Se till att valvautentiseringsfilen finns på en plats som kan användas av installationsprogrammet. Om du får åtkomst till relaterade fel, kopiera valvautentiseringsfilen till en tillfällig plats på den här datorn och försök igen.

    Om det uppstår ett fel för ogiltiga autentiseringsuppgifter (till exempel ”ogiltiga autentiseringsuppgifter angivna”) i filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med recovery-tjänsten. Försök igen när du har hämtat en ny valvautentiseringsfil från portalen. Det här felet visas vanligen om användaren klickar på den **Download valvautentiseringen** alternativ i Azure-portalen i snabb följd. I det här fallet gäller endast andra valvautentiseringsfilen.
10. Styra användningen av nätverksbandbredden under arbete och icke-arbetstid, i den **begränsning inställningen** skärmen, som du kan ange bandbreddsbegränsningen och definiera arbete och fritid.

    ![Begränsningsinställning](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)
11. I den **Recovery mappen inställningen** skärmen, bläddra för mappen där filerna som hämtats från Azure mellanlagrad tillfälligt.

    ![Inställningen för återställning-mappen](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)
12. I den **krypteringsinställning** skärmen, kan du antingen skapa en lösenfras eller ange en lösenfras (minst 16 tecken). Kom ihåg att spara lösenfrasen på en säker plats.

    ![Kryptering](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Om de tappar bort lösenfrasen eller glömt; Microsoft går inte att återställa säkerhetskopierade data. Användaren äger krypteringslösenfrasen och Microsoft har inte insyn i den lösenfras som används av slutanvändaren. Spara filen på en säker plats när den behövs under en återställning.
    >
    >
13. När du klickar på den **registrera** , datorn har registrerats i valvet och du är nu redo att börja säkerhetskopiera till Microsoft Azure.
14. När du använder Data Protection Manager kan du ändra de inställningar som anges under registreringen arbetsflödet genom att klicka på den **konfigurera** alternativet genom att välja **Online** under den **Management**  Fliken.

## <a name="requirements-and-limitations"></a>Krav (och begränsningar)
* DPM kan köras som en fysisk server eller en virtuell dator för Hyper-V som är installerade på System Center 2012 SP1 eller System Center 2012 R2. Det kan också köras som en Azure-dator som körs på System Center 2012 R2 med minst DPM 2012 R2 Samlad uppdatering 3 eller en Windows-dator i VMWare som körs på System Center 2012 R2 med minst Samlad uppdatering 5.
* Om du kör DPM med System Center 2012 SP1 bör du installera Samlad uppdatering 2 för System Center Data Protection Manager SP1. Detta krävs innan du kan installera Azure Backup-agenten.
* DPM-servern måste ha Windows PowerShell och .net Framework 4.5 installerat.
* DPM kan säkerhetskopiera de flesta arbetsbelastningar till Azure Backup. En fullständig lista över vad har som stöds finns stöd på Azure Backup för objekten nedan.
* Data som lagras i Azure Backup kan inte återställas med alternativet ”Kopiera till band”.
* Du behöver ett Azure-konto med Azure Backup-funktionen aktiverad. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Läs mer om [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Med Azure Backup kräver Azure Backup-agenten installeras på servrar som du vill säkerhetskopiera. Alla servrar måste ha minst 5% av storleken på de data som säkerhetskopieras, tillgänglig som lokala ledigt utrymme. Till exempel kräver säkerhetskopiera 100 GB data minst 5 GB ledigt utrymme på den virtuella platsen.
* Data lagras i Azure valvet lagringen. Det finns ingen gräns på mängden data som du kan säkerhetskopiera till en Azure-säkerhetskopiering valvet men storleken för en datakälla (till exempel en virtuell dator eller en databas) får inte överskrida 54400 GB.

Dessa filtyper stöds för säkerhetskopiera till Azure:

* Krypterade (endast fullständiga säkerhetskopior)
* Komprimerade (inkrementella säkerhetskopior stöds)
* Sparse-filer (inkrementella säkerhetskopior stöds)
* Komprimerade och sparse-filer (hanteras som sparse-filer)

Och de stöds inte:

* Servrar i skiftlägeskänsliga filsystem stöds inte.
* Hårda länkar (ignoreras)
* Referenspunkt (ignoreras)
* Krypterade och komprimerade (ignoreras)
* Krypterade och utspridda (ignoreras)
* Komprimerad ström
* Utspridd ström

> [!NOTE]
> Från i System Center 2012 DPM med SP1 och senare kan du säkerhetskopiera upp arbetsbelastningar skyddade av DPM till Azure med Microsoft Azure Backup.
>
>
