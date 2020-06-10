---
title: Säkerhetskopiera virtuella datorer i Azure VMware-lösningen (AVS) med Microsoft Azure Backup Server
description: Konfigurera din Azure VMware-lösning (AVS) för att säkerhetskopiera virtuella datorer som använder Microsoft Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 16941753d614bbfacbc63e4ae518986901153662
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612763"
---
# <a name="back-up-avs-vms-with-microsoft-azure-backup-server"></a>Säkerhetskopiera virtuella datorer i AVS-form med Microsoft Azure Backup Server

I den här artikeln går vi igenom procedurerna för att säkerhetskopiera virtuella VMware-datorer som körs på Azure VMware-lösning (AVS) med Azure Backup Server. Innan du börjar bör du kontrol lera att du har gått igenom [konfigurera Microsoft Azure Backup Server för AVS](set-up-mabs-for-avs.md). 

Sedan går vi igenom alla nödvändiga procedurer för att:

> [!div class="checklist"] 
> * Konfigurera en säker kanal så att Azure Backup Server kan kommunicera med VMware-servrar via HTTPS. 
> * Lägg till kontoautentiseringsuppgifter till Azure Backup Server. 
> * Lägg till vCenter i Azure Backup Server. 
> * Konfigurera en skydds grupp som innehåller de virtuella VMware-datorer som du vill säkerhetskopiera, ange inställningar för säkerhets kopiering och Schemalägg säkerhets kopieringen.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter Server

Azure Backup Server kommunicerar som standard med VMware-servrar via HTTPS. Om du vill konfigurera HTTPS-anslutningen laddar du ned VMware Certificate Authority-certifikatet (CA) och importerar det på Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurera certifikatet

1. I webbläsaren, på Azure Backup Server, anger du webb adressen för webb klienten vSphere.

   > [!NOTE] 
   > Om sidan VMware Komma igång inte visas kontrollerar du inställningarna för anslutning och webbläsare och försöker igen.

1. På sidan VMware Komma igång klickar du på **Hämta betrodda certifikat från rot certifikat utfärdare**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere-webbklient":::

1. Spara filen **Download. zip** på Azure Backup Server datorn och extrahera sedan innehållet till mappen **certifikat** , som innehåller:

   - Rot certifikat fil med ett tillägg som börjar med en numrerad sekvens som. 0 och. 1.

   - CRL-filen har ett tillägg som börjar med en sekvens som. R0 eller. R1.

1. I mappen **certifikat** högerklickar du på rot certifikat filen och väljer **Byt namn** för att ändra tillägget till **. CRT**.

   Fil ikonen ändras till en som representerar ett rot certifikat.

1. Högerklicka på rot certifikatet och välj **Installera certifikat**.

1. Välj **lokal dator** som mål för certifikatet i **guiden Importera certifikat**och klicka på **Nästa**.

   ![Välkommen till guiden](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Bekräfta att du vill tillåta ändringar på datorn om du uppmanas att göra det.    

1. Välj **Placera alla certifikat i följande Arkiv**och klicka på **Bläddra** för att välja certifikat arkiv.

   ![Certifikat lagring](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Välj **betrodda rot certifikat utfärdare** som målmapp och klicka på **OK**.

1. Granska inställningarna och klicka på **Slutför** för att börja importera certifikatet.

   ![Verifiera att certifikatet finns i rätt mapp](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. När certifikat importen har bekräftats loggar du in på vCenter Server för att bekräfta att anslutningen är säker.

### <a name="enable-tls-12-on-azure-backup-server"></a>Aktivera TLS 1,2 på Azure Backup Server

VMWare 6,7 och tidigare hade TLS aktiverat som kommunikations protokoll. 

1. Kopiera följande register inställningar och klistra in i anteckningar och spara sedan filen som TLS. REG utan tillägget. txt.

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Högerklicka på TLS. REG-fil och välj **sammanfoga** eller **Öppna** för att lägga till inställningarna i registret.

## <a name="add-the-provisioning-ip-address-for-avs-esxi-hosts-on-azure-backup-server"></a>Lägg till etablerings-IP-adressen för AVS-ESXi värdar på Azure Backup Server

I för hands versionen löser inte den ESX-värden från den virtuella datorn som distribueras i det virtuella nätverket. Du måste utföra ytterligare åtgärder för att lägga till en värd fil post på den virtuella Azure Backup Server datorn.

### <a name="identify-ip-address-for-esxi-hosts"></a>Identifiera IP-adress för ESXi-värdar

1. Öppna webbläsaren och logga in på vCenter-URL: er. 

   > [!TIP]
   > Du hittar webb adresserna i artikeln [Anslut till den lokala vCenter för ditt privata moln](tutorial-access-private-cloud.md#connect-to-the-local-vcenter-of-your-private-cloud) .

1. I vSphere-klienten väljer du det kluster som du planerar att aktivera säkerhets kopiering.

   :::image type="content" source="media/avs-backup/vsphere-client-select-host.png" alt-text="VSphere-klient – Välj värd":::

1. Välj **Konfigurera**  >  **nätverk**  >  **VMKernel-kort**och gå till listan över enheter och identifiera nätverkskortet som har **etablerings** rollen aktive rad och anteckna sedan **IP-adressen** och ESXi-värdnamnet.

   :::image type="content" source="media/avs-backup/vmkernel-adapters-provisioning-enabled.png" alt-text="VMKernel-adaptrar – etablering av aktiverade enheter":::

1. Upprepa föregående steg för varje ESXi-värd under varje kluster som du planerar att aktivera säkerhets kopiering.

### <a name="update-the-host-file-on-the-azure-backup-server"></a>Uppdatera värd filen på Azure Backup Server

1. Öppna **anteckningar** som administratör.

2. Välj **fil > öppna** och Sök efter c:\Windows\System32\Drivers\etc\hosts.

3. Lägg till posten för varje ESXi-värd tillsammans med den IP-adress som du identifierade i avsnittet ovan.

4. Spara ändringarna och Stäng Anteckningar.

## <a name="add-the-account-on-azure-backup-server"></a>Lägg till kontot på Azure Backup Server


1. Öppna Azure Backup Server och välj **hanterings**  >  **produktions servrar**i Azure Backup Server-konsolen  >  **Hantera VMware**.

   ![Azure Backup Server-konsol](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. I dialog rutan **Hantera autentiseringsuppgifter** klickar du på **Lägg till**.

   ![Dialog rutan Azure Backup Server Hantera autentiseringsuppgifter](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. I **Lägg till autentiseringsuppgift**anger du ett namn och en beskrivning för den nya autentiseringsuppgiften och anger det användar namn och lösen ord som du definierade på VMware-servern.

   > [!NOTE] 
   > Om VMware-servern och Azure Backup Server inte finns i samma domän anger du domänen i användar namnet.

   ![Dialog rutan Azure Backup Server Lägg till autentiseringsuppgift](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Klicka på **Lägg** till för att lägga till den nya autentiseringsuppgiften.

   ![Dialog rutan Azure Backup Server Hantera autentiseringsuppgifter](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Lägg till vCenter Server i Azure Backup Server

1. I Azure Backup Server-konsolen klickar du på **hantering**  >  **produktions servrar**  >  **Lägg till**.

   ![Öppna guiden för att lägga till produktions server](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Välj **VMware-servrar** och klicka på **Nästa**.

   ![Guiden för att lägga till produktions server](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Ange IP-adressen för vCenter.

   ![Ange VMware-Server](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. I **SSL-port**anger du den port som används för att kommunicera med vCenter.

   > [!TIP] 
   > Port 443 är standard porten, men du kan ändra den om vCenter lyssnar på en annan port.

1. I **Ange autentiseringsuppgift**väljer du de autentiseringsuppgifter som du skapade i föregående avsnitt.

1. Klicka på **Lägg** till för att lägga till vCenter i listan servrar och klicka på **Nästa**.

   ![Lägg till VMWare-Server och autentiseringsuppgift](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. På sidan **Sammanfattning** klickar du på **Lägg** till för att lägga till vCenter till Azure Backup Server.

   Den nya servern läggs till omedelbart. vCenter behöver inte en agent.

   ![Lägg till VMware-Server i Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. På sidan **Slutför** granskar du inställningarna och klickar sedan på **Stäng**.

   ![Sidan Slutför](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Du bör se vCenter-servern som anges under produktions servern med typen som VMware-Server och agent status som *OK*. Om du ser agent statusen som *okänd*klickar du på **Uppdatera**.

## <a name="configure-a-protection-group"></a>Konfigurera en skydds grupp

Skydds grupper samlar in flera virtuella datorer och tillämpar samma data lagrings-och säkerhets kopierings inställningar för alla virtuella datorer i gruppen.

1. I Azure Backup Server-konsolen klickar du på **skydd**  >  **nytt**.

   ![Öppna guiden Skapa ny skydds grupp](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klicka på **Nästa**på Välkomst sidan **Skapa ny skydds grupp** .

   ![Dialog rutan skapa ny skydds grupp guide](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. På sidan **Välj typ av skydds grupp** väljer du **servrar** och klickar sedan på **Nästa**. Sidan **Välj grupp medlemmar** visas.

1. I **Välj grupp medlemmar**väljer du de virtuella datorer (eller VM-mappar) som du vill säkerhetskopiera. Klicka sedan på **Nästa**.

   > [!NOTE]
   > När du väljer en mapp eller virtuella datorer eller mappar i mappen väljs även för säkerhets kopiering. Du kan avmarkera mappar och virtuella datorer som du inte vill säkerhetskopiera. Om en virtuell dator eller mapp redan säkerhets kopie ras kan du inte välja den, vilket garanterar att dubbla återställnings punkter inte skapas för en virtuell dator.

   ![Välj grupp medlemmar](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. På sidan **Välj data skydds metod** anger du ett namn på skydds gruppen och skydds inställningarna. 

1. Ange kortvarigt skydd till **disk**, aktivera onlineskydd och klicka sedan på **Nästa**.

   ![Välj dataskyddsmetod](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Ange hur länge du vill behålla data som säkerhets kopie ras till disk.

   - **Kvarhållningsintervall** – antal dagar som disk återställnings punkter hålls kvar.
   - **Snabb och fullständig säkerhets kopiering** – hur ofta disk återställnings punkter tas. Om du vill ändra de tidpunkter/datum då kortsiktiga säkerhets kopieringar sker klickar du på **ändra**.

   :::image type="content" source="media/avs-backup/new-protection-group-specify-short-term-goals.png" alt-text="Ange kortsiktiga mål för disk-baserat skydd":::

1. Granska disk utrymmet för VM-säkerhetskopieringarna i **Granska diskallokering**.

   - De rekommenderade disk tilldelningarna baseras på det kvarhållningsintervall du angav, typ av arbets belastning och storleken på skyddade data. Gör de ändringar som krävs och klicka sedan på **Nästa**.

   - **Data storlek:** Storlek på data i skydds gruppen.

   - **Disk utrymme:** Rekommenderad mängd disk utrymme för skydds gruppen. Om du vill ändra den här inställningen bör du allokera totalt utrymme som är något större än det belopp som du uppskattar att varje data källa växer.

   - **Information om lagringspool:** Visar status för lagringspoolen, inklusive total och återstående disk storlek.

   :::image type="content" source="media/avs-backup/review-disk-allocation.png" alt-text="Granska disk utrymme som allokerats i lagringspoolen":::

   > [!NOTE]
   > I vissa fall är data storleken som rapporteras större än den faktiska storleken på den virtuella datorn. Vi är medvetna om problemet och undersöker det just nu.

1. På sidan **Välj metod för skapande av replik** anger du hur du vill att den första säkerhets kopieringen ska utföras och klickar på **Nästa**.

   - Standardvärdet sker **automatiskt över nätverket** och **nu**.   Om du använder standardvärdet rekommenderar vi att du anger en tid med låg belastning. Välj **senare** och ange dag och tid.

   - Överväg att replikera data offline med hjälp av flyttbara media för stora mängder data eller mindre än optimala nätverks förhållanden.

   ![Välj metod för skapande av replik](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. I **alternativ för konsekvens kontroll**väljer du hur och när du vill automatisera konsekvens kontroller och klickar på **Nästa**.

   - Du kan köra konsekvens kontroller när replik data blir inkonsekventa eller enligt ett angivet schema.

   - Om du inte vill konfigurera automatiska konsekvens kontroller kan du köra en manuell kontroll genom att högerklicka på skydds gruppen **utför konsekvens kontroll**.

1. På sidan **Ange online skydds data** väljer du de virtuella datorer eller VM-mappar som du vill säkerhetskopiera och klickar sedan på **Nästa**. 

   > [!TIP]
   > Du kan välja medlemmar individuellt eller klicka på **Välj alla** för att välja alla medlemmar.

   ![Ange skydds data online](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. På sidan **Ange schema för onlinesäkerhetskopiering** anger du hur ofta du vill säkerhetskopiera data från lokal lagring till Azure och klickar på **Nästa**. 

   - Moln återställnings punkter för data som genereras enligt schemat. 

   - När återställnings punkten har genererats överförs den till Recovery Services valvet i Azure.

   ![Ange schemat för onlinesäkerhetskopiering](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. På sidan **Ange princip för kvarhållning av online** anger du hur länge du vill behålla återställnings punkterna som skapas med säkerhets kopieringar per dag/vecka/månad/år till Azure. Klicka sedan på **Nästa**.

   - Det finns ingen tids gräns för hur länge du kan lagra data i Azure.

   - Den enda begränsningen är att du inte kan ha mer än 9999 återställnings punkter per skyddad instans. I det här exemplet är den skyddade instansen VMware-servern.

   ![Ange bevarande princip online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. På sidan **Sammanfattning** granskar du inställningarna och klickar sedan på **Skapa grupp**.

   ![Skydds grupp medlem och inställnings Sammanfattning](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-azure-backup-server-console"></a>Övervaka med Azure Backup Server-konsolen

När du har konfigurerat skydds gruppen för säkerhets kopiering av virtuella datorer i AVS-läge kan du övervaka säkerhets kopierings jobbets status och avisering med hjälp av Azure Backup Server-konsolen. Här är vad du kan övervaka.

- På fliken aviseringar i fönstret övervakning kan du övervaka fel, varningar och allmän information för en skydds grupp för en enskild skyddad dator eller allvarlighets grad för meddelande. Du kan visa aktiva och inaktiva aviseringar och konfigurera e-postaviseringar

- På fliken jobb i fönstret övervakning kan du Visa jobb som initierats av Azure Backup Server för en bestämd skyddad data källa eller skydds grupp. Du kan följa jobb förloppet eller kontrol lera resurser som förbrukas av jobb.

- I avsnittet **skydds** aktivitet kan du kontrol lera statusen för volymer och resurser i skydds gruppen och kontrol lera konfigurations inställningarna, till exempel återställnings inställningar, diskallokering och schema för säkerhets kopiering.

- I aktivitets avsnittet **hantering** kan du Visa fliken **diskar, online**och **agenter** för att kontrol lera statusen för diskarna i lagringspoolen, registrera till Azure och distribuerad status för DPM-agenten.

:::image type="content" source="media/avs-backup/monitor-backup-jobs-in-mabs.png" alt-text="Övervaka status för säkerhets kopierings jobb i Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Återställa virtuella VMware-datorer

I Azure Backup Server Administratörskonsol finns det två sätt att hitta återställnings bara data – Sök eller bläddra. När du återställer data kan du kanske eller inte vill återställa data eller en virtuell dator till samma plats. Därför stöder Azure Backup Server tre återställnings alternativ för säkerhets kopiering av virtuella VMware-datorer:

- **Återställning av ursprunglig plats (OLR)** – Använd OLR för att återställa en skyddad virtuell dator till dess ursprungliga plats. Du kan bara återställa en virtuell dator till dess ursprungliga plats om inga diskar har lagts till eller tagits bort sedan säkerhets kopieringen genomfördes. Om diskarna har lagts till eller tagits bort måste du använda alternativ plats återställning.

- **Återställning av alternativ plats (återställning till)** – när den ursprungliga virtuella datorn saknas eller om du inte vill störa den ursprungliga virtuella datorn återställer du den virtuella datorn till en annan plats. Om du vill återställa en virtuell dator till en alternativ plats måste du ange platsen för en ESXi-värd, resurspool, mapp och lagrings plats och sökväg för lagring. För att hjälpa till att skilja den återställda virtuella datorn från den ursprungliga virtuella datorn lägger Azure Backup Server till "-återställd" till namnet på den virtuella datorn.

- **Individuell återställning av fil plats (ILR)** – om den skyddade virtuella datorn är en virtuell Windows Server-dator kan enskilda filer/mappar i den virtuella datorn återställas med hjälp av Azure Backup Server ILR-funktionen. Om du vill återställa enskilda filer, se proceduren senare i den här artikeln. Att återställa en enskild fil från en virtuell dator är bara tillgängligt för virtuella Windows-datorer och disk återställnings punkter.

### <a name="restore-a-recovery-point"></a>Återställa en återställnings punkt

1. Klicka på återställnings visning i Azure Backup Server Administratörskonsol. 

1. Använd fönstret Bläddra, bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visas tillgängliga återställnings punkter i rutan återställnings punkter för.

   ![Tillgängliga återställnings punkter](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. I fältet **återställnings punkter för** använder du kalender-och list menyerna för att välja ett datum när en återställnings punkt skulle utföras. Kalender datum i fetstil har tillgängliga återställnings punkter. Alternativt kan du högerklicka på den virtuella datorn och välja **Visa alla återställnings punkter** och sedan välja återställnings punkten i listan.

   > [!NOTE] 
   > För kortsiktigt skydd väljer du en diskbaserad återställnings punkt för snabbare återställning. När kortsiktiga återställnings punkter upphör att gälla ser du bara återställnings punkter online att återställa.

1. Innan du återställer från en onlineåterställningspunkt ser du till att mellanlagringsplatsen innehåller tillräckligt med ledigt utrymme för att kunna ta del av den fulla okomprimerade storleken på den virtuella dator som du vill återställa. Du kan visa/ändra mellanlagringsplatsen genom att köra **guiden Konfigurera prenumerations inställningar**.

   :::image type="content" source="media/avs-backup/mabs-recovery-folder-settings.png" alt-text="Azure Backup Server inställningar för återställnings mapp":::

1. Klicka på **Återställ** för att öppna **återställnings guiden**.

   ![Återställnings guiden, granska val av återställning](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Klicka på **Nästa** för att gå till skärmen **Ange återställnings alternativ** och klicka på **Nästa** igen för att gå vidare till skärmen **Välj återställnings typ** . 

   > [!NOTE]
   > VMware-arbetsbelastningar stöder inte aktivering av begränsning av nätverks bandbredd.

1. På sidan **Välj återställnings typ** väljer du om du vill återställa till den ursprungliga instansen eller en ny plats och klickar sedan på **Nästa**.

   - Om du väljer **Återställ till ursprunglig instans**behöver du inte göra några fler val i guiden. Data för den ursprungliga instansen används.

   - Om du väljer **Återställ som virtuell dator på en värd**anger du informationen för **ESXi-värden, resurspool, mappar**och **sökväg**på skärmen **Ange mål** .

   ![Välj återställnings typ](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. På sidan **Sammanfattning** granskar du inställningarna och klickar på **Återställ** för att starta återställnings processen. 

   Skärmen återställnings status visar förloppet för återställnings åtgärden.

### <a name="restore-an-individual-file-from-a-vm"></a>Återställa en enskild fil från en virtuell dator

Du kan återställa enskilda filer från en skyddad VM-återställnings punkt. Den här funktionen är endast tillgänglig för virtuella Windows Server-datorer. Att återställa enskilda filer liknar att återställa hela den virtuella datorn, förutom att du bläddrar till VMDK och letar reda på de filer som du vill ha innan du påbörjar återställnings processen. 

> [!NOTE]
> Att återställa en enskild fil från en virtuell dator är bara tillgängligt för virtuella Windows-datorer och disk återställnings punkter.

1. Klicka på **återställnings** visning i Azure Backup Server administratörskonsol.

1. Använd fönstret **Bläddra** , bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visas tillgängliga återställnings punkter i rutan återställnings punkter för.

   ![Tillgängliga återställnings punkter](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. I rutan **återställnings punkter för** använder du kalendern för att välja det datum som innehåller önskade återställnings punkt (er). Beroende på hur säkerhets kopierings principen har kon figurer ATS kan datum ha fler än en återställnings punkt. 

1. När du har valt den dag då återställnings punkten togs, se till att du har valt rätt **återställnings tid**. 

   > [!NOTE]
   > Om det valda datumet har flera återställnings punkter väljer du återställnings punkten genom att välja den i list rutan återställnings tid. 

   När du har valt återställnings punkten visas listan över återställnings bara objekt i **Sök vägs** fönstret.  

1. Du hittar de filer som du vill återställa genom att dubbelklicka på objektet i kolumnen **återställnings Bart objekt** i rutan **sökväg** och välja den eller de mappar som du vill återställa. Om du vill markera flera objekt trycker du på **CTRL** -tangenten när du markerar varje objekt. Använd **Sök vägs** fönstret för att söka i listan över filer eller mappar som visas i kolumnen **återställnings Bart objekt** .
    
   > [!NOTE]
   > **Sök listan nedan** söker inte i undermappar. Om du vill söka i undermappar dubbelklickar du på mappen. Använd **upp** -knappen för att flytta från en underordnad mapp till den överordnade mappen. Du kan välja flera objekt (filer och mappar), men de måste finnas i samma överordnade mapp. Du kan inte återställa objekt från flera mappar i samma återställnings jobb.

   ![Granska val av återställning](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. När du har valt objekt för återställning går du till menyfliksområdet Administratörskonsol verktyg och klickar på **Återställ** för att öppna **återställnings guiden**. I återställnings guiden visar sidan **Granska val av återställning** de valda objekten som ska återställas.

1. Gör något av följande på skärmen **Ange återställnings alternativ** :

   - Klicka på **ändra** för att aktivera begränsning av nätverks bandbredd.  I dialog rutan begränsa väljer du **Aktivera begränsning av nätverks bandbredds användning** för att aktivera det. När du har aktiverat konfigurerar du **inställningarna** och **arbets schemat**.
    
   - Klicka på **Nästa** om du vill lämna nätverks begränsningen inaktive rad.

1. På skärmen **Välj återställnings typ** klickar du på **Nästa**. Du kan bara återställa dina filer eller mappar till en nätverksmapp.

1. På skärmen **Ange mål** klickar du på **Bläddra** för att hitta en nätverks plats för dina filer eller mappar. Azure Backup Server skapar en mapp där alla återställda objekt kopieras. Mappnamnet har prefixet MABS_day-månad-år. När du väljer en plats för de återställda filerna eller mappen, anges information för den platsen (mål, mål Sök väg och tillgängligt utrymme).

   ![Ange plats för att återställa filer](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. På skärmen **Ange återställnings alternativ** väljer du vilken säkerhets inställning som ska användas. Du kan välja att ändra nätverks bandbreddens användnings begränsning, men begränsningen är inaktive rad som standard. Dessutom är **San-återställning** och **meddelanden** inte aktiverade.

1. På sidan **Sammanfattning** granskar du inställningarna och klickar på **Återställ** för att starta återställnings processen. Skärmen **återställnings status** visar förloppet för återställnings åtgärden.

## <a name="next-steps"></a>Nästa steg

Information om fel sökning av problem när du konfigurerar säkerhets kopieringar finns i fel söknings guiden för Azure Backup Server.



> [!div class="nextstepaction"]
> [Fel söknings guide för Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
