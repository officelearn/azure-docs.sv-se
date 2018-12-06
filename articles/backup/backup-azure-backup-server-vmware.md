---
title: Säkerhetskopiera VMware-servrar med Azure Backup Server
description: Säkerhetskopiera en VMware vCenter/ESXi-servrar till Azure eller disk med hjälp av Azure Backup Server. Den här artikeln innehåller steg = för-steg-anvisningar för att säkerhetskopiera (eller skydda) VMware-arbetsbelastningar.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: e39e5d12610164ca4a1372830cf25ea203fd382c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968842"
---
# <a name="back-up-a-vmware-server-to-azure"></a>Säkerhetskopiera en VMware-server till Azure

Den här artikeln förklarar hur du konfigurerar Azure Backup Server för att skydda VMware-server-arbetsbelastningar. Den här artikeln förutsätter att du redan har Azure Backup Server installerat. Om du inte har Azure Backup Server är installerat, se [förbereda säkerhetskopiering av arbetsbelastningar med Azure Backup Server](backup-azure-microsoft-azure-backup.md).

Azure Backup Server kan säkerhetskopiera eller att skydda VMware vCenter serverversion 6.5, 6.0 och 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter-servern

Azure Backup Server kommunicerar med varje vCenter-servern via en HTTPS-kanal som standard. Om du vill aktivera säker kommunikation, rekommenderar vi att du installerar certifikatet VMware certifikatutfärdaren (CA) på Azure Backup Server. Om du inte kräver säker kommunikation och föredrar att inaktivera HTTPS-krav, se [inaktivera säker kommunikationsprotokoll](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Importera det betrodda certifikatet på Azure Backup Server för att skapa en säker anslutning mellan Azure Backup Server och vCenter-servern.

Normalt använder du en webbläsare på Azure Backup Server-dator för att ansluta till vCenter-servern via vSphere-webbklienten. Första gången du använder Azure Backup Server webbläsaren för att ansluta till vCenter-servern, anslutningen är inte säker. Följande bild visar osäker anslutning.

![Exempel på osäker anslutning till VMware-server](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Ladda ned de betrodda rot-CA-certifikat för att åtgärda problemet och skapa en säker anslutning.

1. Ange Webbadressen i webbläsaren på Azure Backup Server till vSphere-webbklienten. VSphere-webbklienten inloggningssidan visas.

    ![vSphere-webbklienten](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    Längst ned i information för administratörer och utvecklare att leta upp den **Download betrodda rotcertifikatutfärdare** länk.

    ![Länk för att ladda ned de betrodda rot-CA-certifikat](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Om du inte ser sidan vSphere-webbklienten inloggning, Kontrollera proxyinställningarna för din webbläsare.

2. Klicka på **Download betrodda rotcertifikatutfärdare**.

    VCenter-servern hämtar en fil till din lokala dator. Filnamnet har namnet **hämta**. Beroende på din webbläsare får du ett meddelande som frågar om du vill öppna eller spara filen.

    ![Hämta meddelande när certifikat hämtas](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Spara filen till en plats på Azure Backup Server. När du sparar filen, lägger du till .zip-filnamnstillägg.

    Filen är en .zip-fil som innehåller information om certifikat. Du kan använda verktyg för extrahering med filnamnstillägget .zip.

4. Högerklicka på **download.zip**, och välj sedan **extrahera alla** att extrahera innehållet.

    ZIP-filen extraherar innehållet till en mapp med namnet **certifikat**. Två typer av filer visas i mappen certifikat. Filen med rotcertifikatet har ett tillägg som börjar med en numrerad sekvens som.0 och.1.

    CRL-filen har ett tillägg som börjar med en sekvens som .r0 eller .r1. CRL-filen är associerad med ett certifikat.

    ![Ladda ned filen som extraheras lokalt ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. I den **certifikat** mapp, högerklickar du på filen med rotcertifikatet, och klicka sedan på **Byt namn på**.

    ![Byt namn på rotcertifikat ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Ändra det rotcertifikatet tillägget till .crt. När du blir tillfrågad om du är säker på att du vill ändra tillägget klickar du på **Ja** eller **OK**. Annars kan ändra du filens avsedda funktion. Ikonen för filen ändras till en ikon som representerar ett rotcertifikat.

6. Högerklicka på rotcertifikatet och popup-menyn, Välj **installera certifikat**.

    Den **guiden Importera certifikat** dialogrutan visas.

7. I den **guiden Importera certifikat** dialogrutan **lokal dator** som mål för den certifikatet och sedan på **nästa** att fortsätta.

    ![Certifikatet lagringsalternativ för mål ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Om du blir tillfrågad om du vill tillåta ändringar till datorn klickar du på **Ja** eller **OK**, att alla ändringar.

8. På den **Certificate Store** väljer **placera alla certifikat i nedanstående arkiv**, och klicka sedan på **Bläddra** välja certifikatarkivet.

    ![Placera certifikat i en specifik storage punkt](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    Den **Välj certifikatet Store** dialogrutan visas.

    ![Mappen storage-certifikathierarkin](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Välj **betrodda rotcertifikatutfärdare** som målmapp för certifikat och klicka sedan på **OK**.

    ![Certifikatet målmapp](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    Den **betrodda rotcertifikatutfärdare** mappen bekräftas som certifikatarkivet. Klicka på **Nästa**.

    ![Certifikat-mappen](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. På den **du har slutfört guiden Importera certifikat** sidan, kontrollera att certifikatet finns i den önskade mappen och klicka sedan på **Slutför**.

    ![Kontrollera att certifikatet är i rätt mapp](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    En dialogruta, har importera lyckad certifikat bekräftats.

11. Logga in på vCenter-servern att kontrollera att anslutningen är säker.

  Om det går inte att genomföra Importera certifikat och du kan inte upprätta en säker anslutning, i dokumentationen VMware vSphere på [Skaffa servercertifikat](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Om du har säkert gränser i din organisation och inte vill aktivera HTTPS-protokollet, kan du använda följande procedur för att inaktivera säker kommunikation.

### <a name="disable-secure-communication-protocol"></a>Inaktivera säker kommunikation-protokollet

Om din organisation inte kräver HTTPS-protokollet, kan du använda följande steg för att inaktivera HTTPS. Skapa en registernyckel som ignorerar standardbeteendet för att inaktivera standardbeteendet.

1. Kopiera och klistra in följande text i en txt-fil.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Spara filen till Azure Backup Server-datorn. För filnamnet, använder du DisableSecureAuthentication.reg.

3. Dubbelklicka på filen för att aktivera registerposten.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Skapa en roll och användare på vCenter-servern

På vCenter Server är en roll en fördefinierad uppsättning behörigheter. En vCenter Server-administratören skapar rollerna. Om du vill tilldela behörigheter kombinationer administratören av användarkonton med en roll. Skapa en roll med specifika privilegier för att etablera nödvändiga autentiseringsuppgifter för säkerhetskopiering av vCenter Server-datorn, och sedan associerar användarkonton med rollen.

Azure Backup Server använder ett användarnamn och lösenord för att autentisera med vCenter-servern. Azure Backup Server använder dessa autentiseringsuppgifter som autentisering för alla säkerhetskopieringsåtgärder.

Lägga till en vCenter-serverrollen och dess behörigheter för en säkerhetskopiering administratör:

1. Logga in till vCenter-servern och sedan i vCenter Server **Navigator** panelen, klickar du på **Administration**.

    ![Administrationsalternativ för vCenter Server Navigator panelen](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. I **Administration** Välj **roller**, och sedan i den **roller** panelen klickar du på ikonen Lägg till roll (den symbolen +).

    ![Lägg till roll](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    Den **skapa roll** dialogrutan visas.

    ![Skapa roll](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. I den **skapa roll** i dialogrutan den **rollnamn** anger *BackupAdminRole*. Rollnamnet kan vara vad du vill, men det bör vara att känna igen för rollens ändamål.

4. Välj behörigheterna för rätt version av vCenter och klicka sedan på **OK**. I följande tabell visas behörigheter som krävs för vCenter 6.0 och vCenter 5.5.

  När du väljer behörigheten, klicka på ikonen bredvid den överordnade etiketten Expandera överordnat och visa behörigheter som underordnade. För att välja behörigheter som VirtualMachine, måste du börja överordnad-underordnad hierarki med flera nivåer. Du behöver inte välja alla underordnade privilegier i en överordnad-behörighet.

  ![Hierarkin behörighet överordnad-underordnad](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  När du klickar på **OK**, den nya rollen visas i listan på panelen roller.

|Behörigheter för vCenter 6.0 och 6.5| Behörigheter för vCenter 5.5|
|----------------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Skapa ett användarkonto för vCenter-Server och behörigheter

Skapa ett användarkonto när roll med behörigheter som har konfigurerats. Användarkontot har ett namn och lösenord som innehåller de autentiseringsuppgifter som används för autentisering.

1. Skapa ett användarkonto i vCenter Server **Navigator** panelen, klickar du på **användare och grupper**.

    ![Alternativ för användare och grupper](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Den **vCenter användare och grupper** visas panelen.

    ![vCenter-panelen för användare och grupper](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. I den **vCenter användare och grupper** fönstret väljer den **användare** fliken och klicka sedan på ikonen Lägg till användare (den symbolen +).

    Den **ny användare** dialogrutan visas.

3. I den **ny användare** dialogrutan rutan, lägga till information om användaren och klicka sedan på **OK**. I det här är användarnamnet BackupAdmin.

    ![Dialogrutan Ny användare](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Det nya användarkontot visas i listan.

4. Associera användarkonton med rollen i den **Navigator** panelen, klickar du på **globala behörigheter**. I den **globala behörigheter** fönstret väljer den **hantera** fliken och klicka sedan på ikonen Lägg till (den symbolen +).

    ![Globala behörigheter panelen](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    Den **globala behörigheter Root – Lägg till behörighet** dialogrutan visas.

5. I den **globala behörighet Root – Lägg till behörighet** dialogrutan klickar du på **Lägg till** att välja den användare eller grupp.

    ![Välj användare eller grupp](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    Den **Välj användare/grupper** dialogrutan visas.

6. I den **Välj användare/grupper** dialogrutan väljer du **BackupAdmin** och klicka sedan på **Lägg till**.

    I **användare**, *domän\användarnamn* format används för användarkontot. Om du vill använda en annan domän väljer du det från den **domän** lista.

    ![Lägg till BackupAdmin användare](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Klicka på **OK** att lägga till de valda användarna att den **Lägg till behörighet** dialogrutan.

7. Nu när du har identifierat användaren, tilldelar du användaren rollen. I **tilldelas rollen**, från den nedrullningsbara listan, Välj **BackupAdminRole**, och klicka sedan på **OK**.

    ![Tilldela användare till rollen](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  På den **hantera** fliken i den **globala behörigheter** panelen, det nya användarkontot och den associera rollen visas i listan.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Upprätta autentiseringsuppgifter för vCenter-Server på Azure Backup Server

Innan du lägger till VMware-servern på Azure Backup Server, installera [uppdatering 1 för Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Dubbelklicka på ikonen i Azure Backup Server-desktop för att öppna Azure Backup Server.

    ![Azure Backup Server-ikon](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Om du inte hittar ikonen på skrivbordet, öppnar du Azure Backup Server i listan över installerade appar. Azure Backup Server-appnamnet kallas för Microsoft Azure Backup.

2. I Azure Backup Server-konsolen klickar du på **Management**, klickar du på **produktionsservrar**, och klickar sedan på verktygsfliken **hantera VMware**.

    ![Azure Backup Server-konsolen](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    Den **hantera autentiseringsuppgifter** dialogrutan visas.

    ![Dialogrutan för Azure Backup Server hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. I den **hantera autentiseringsuppgifter** dialogrutan klickar du på **Lägg till** att öppna den **Lägg till autentiseringsuppgift** dialogrutan.

4. I den **Lägg till autentiseringsuppgift** dialogrutan anger du ett namn och en beskrivning för de nya autentiseringsuppgifter. Ange sedan användarnamn och lösenord. Namn, *Contoso Vcenter credential* används för att identifiera autentiseringsuppgifterna i nästa procedur. Använd samma användarnamn och lösenord som används för vCenter-servern. Om vCenter Server och Azure Backup Server inte finns i samma domän, i **användarnamn**, ange domänen.

    ![Dialogrutan för Azure Backup Server Lägg till autentiseringsuppgift](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Klicka på **Lägg till** att lägga till de nya autentiseringsuppgifter i Azure Backup Server. De nya autentiseringsuppgifter visas i listan i den **hantera autentiseringsuppgifter** dialogrutan.

    ![Dialogrutan för Azure Backup Server hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Stäng den **hantera autentiseringsuppgifter** dialogrutan klickar du på den **X** i det övre högra hörnet.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Lägg till vCenter-servern i Azure Backup Server

Guiden lägga till produktionsserver används för att lägga till vCenter-servern i Azure Backup Server.

Slutför följande procedur för att öppna guiden lägga till produktionsserver:

1. I Azure Backup Server-konsolen klickar du på **Management**, klickar du på **produktionsservrar**, och klicka sedan på **Lägg till**.

    ![Guiden lägga till Open produktionsserver](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    Den **guiden lägga till produktionsserver** dialogrutan visas.

    ![Guiden lägga till produktionsserver](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. På den **väljer produktion servertyp** väljer **VMware-servrar**, och klicka sedan på **nästa**.

3. I **serverns namn/IP-adress**, ange den fullständigt kvalificerade domännamnet (FQDN) eller IP-adressen för VMware-servern (värd ESXi-servern). Om alla ESXi-servrar hanteras av samma vCenter, kan du använda vCenter-namn.

    ![Ange VMware-server FQDN eller IP-adress](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. I **SSL-porten**, ange den port som används för att kommunicera med VMware-servern. Använd port 443, som är standardporten, om du inte vet att en annan port krävs.

5. I **ange autentiseringsuppgifter**, Välj den autentiseringsuppgift som du skapade tidigare.

    ![Ange autentiseringsuppgift](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klicka på **Lägg till** att lägga till VMware-servern i listan över **har lagts till VMware-servrar**, och klicka sedan på **nästa** att flytta till nästa sida i guiden.

    ![Lägg till VMWare-servern och autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. I den **sammanfattning** klickar du på **Lägg till** att lägga till den angivna VMware-servern i Azure Backup Server.

    ![Lägg till VMware-server i Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Säkerhetskopian av VMware-servern är en agentlös säkerhetskopiering och den nya servern läggs omedelbart. Den **Slutför** sidan visas resultaten.

  ![Sidan Slutför](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Upprepa föregående steg i det här avsnittet för att lägga till flera instanser av vCenter-Server på Azure Backup Server.

När du lägger till vCenter-servern på Azure Backup Server är nästa steg att skapa en skyddsgrupp. Skyddsgruppen anger olika detaljinformation för kort och lång sikt kvarhållning och det kan du definiera och tillämpa principen för säkerhetskopiering. Säkerhetskopieringspolicyn är schemat för när säkerhetskopieras och vad som säkerhetskopieras.


## <a name="configure-a-protection-group"></a>Konfigurera en skyddsgrupp

Om du inte har använt System Center Data Protection Manager eller Azure Backup Server innan du läser [planera för säkerhetskopiering till disk](https://technet.microsoft.com/library/hh758026.aspx) att förbereda maskinvarumiljön. När du har kontrollerat att du har rätt lagring kan du använda guiden Skapa ny Skyddsgrupp för att lägga till VMware-datorer.

1. I Azure Backup Server-konsolen klickar du på **Protection**, och på verktygsfliken **New** att öppna guiden Skapa ny Skyddsgrupp.

    ![Öppna guiden Skapa ny Skyddsgrupp](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    Den **Skapa ny Skyddsgrupp** dialogruta visas.

    ![Skapa ny Skyddsgrupp dialogruta](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Klicka på **nästa** att gå vidare till den **Välj typ av skyddsgrupp** sidan.

2. På den **typ av Välj Skyddsgrupp** väljer **servrar** och klicka sedan på **nästa**. Den **Välj gruppmedlemmar** visas.

3. På den **Välj gruppmedlemmar** sidan, tillgängliga medlemmar och de markerade medlemmarna visas. Välj de medlemmar som du vill skydda och klicka sedan på **nästa**.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    När du väljer en medlem, om du markerar en mapp som innehåller andra mappar eller virtuella datorer, markeras också dessa mappar och virtuella datorer. Inkludering av mappar och virtuella datorer i den överordnade mappen kallas mappnivå skydd. Ta bort en mapp eller en virtuell dator genom att avmarkera kryssrutan.

    Om en virtuell dator eller en mapp som innehåller en virtuell dator är redan skyddas i Azure, kan du välja den virtuella datorn igen. Det vill säga när en virtuell dator skyddas till Azure kan skyddas det inte igen, vilket förhindrar att duplicerade återställningspunkter skapas för en virtuell dator. Om du vill se vilka Azure Backup Server-instans skyddar redan medlem pekar du på medlemmen som ska se namnet på servern som skyddar.

4. På den **Välj Dataskyddsmetod** anger du ett namn för skyddsgruppen. Kortsiktigt skydd (till disk) och online-skydd har markerats. Om du vill använda onlineskydd (till Azure), måste du använda kortsiktigt skydd till disk. Klicka på **nästa** att fortsätta till intervallet för kortsiktigt skydd.

    ![Välj dataskyddsmetod](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. På den **ange kortsiktiga mål** sidan för **Kvarhållningsintervall**, ange hur många dagar som du vill behålla återställningspunkter som är *lagrade till disk*. Om du vill ändra tiden och dagar när återställningspunkter skapas, klickar du på **ändra**. Kortsiktiga återställningspunkter finns fullständiga säkerhetskopior. De är inte inkrementella säkerhetskopieringar. När du är nöjd med kortvariga mål klickar du på **nästa**.

    ![Ange kortvariga mål](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. På den **granska diskallokering** granskar och om det behövs ändrar du hur mycket diskutrymme för de virtuella datorerna. De rekommenderade diskallokering baseras på det kvarhållandeintervall som angetts i den **ange kortsiktiga mål** sidan, vilken typ av arbetsbelastning och storleken på skyddade data (som identifieras i steg 3).  

  - **Datastorlek:** datastorleken i skyddsgruppen.
  - **Diskutrymme:** rekommenderade mängden ledigt diskutrymme för skyddsgruppen. Om du vill ändra den här inställningen allokerar du ett totalt utrymme som är något större än det belopp som du uppskattar att varje datakälla växer.
  - **Samordna data:** om du aktiverar samplacering kan flera datakällor i skyddet kan du mappa till en enda replik och återställningspunktvolym. Samordning stöds inte för alla arbetsbelastningar.
  - **Väx automatiskt:** om du aktiverar den här inställningen om datan i skyddsgruppen blir större än den ursprungliga allokeringen kan System Center Data Protection Manager försöker öka diskstorleken med 25 procent.
  - **Information om lagringspool:** visar status för lagringspoolen, inklusive total och återstående diskstorlek.

    ![Granska diskallokering](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    När du är nöjd med utrymmesallokeringen **nästa**.

7. På den **väljer Replikskapandemetod** anger du hur du vill skapa den ursprungliga kopian eller replik av skyddade data på Azure Backup Server.

    Standardvärdet är **automatiskt över nätverket** och **nu**. Om du använder standard, rekommenderar vi att du anger en tid med låg belastning. Välj **senare** och ange en dag och tidpunkt.

    Överväg att replikera data offline med hjälp av flyttbara media för stora mängder data eller mindre än optimala nätverksförhållanden.

    När du har gjort dina val klickar du på **nästa**.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. På den **alternativ för konsekvenskontroll** sidan, Välj hur och när du vill automatisera konsekvenskontroller. Du kan köra en konsekvenskontroll när replikdata blir inkonsekvent, eller enligt ett schema.

    Om du inte vill konfigurera automatiska konsekvenskontroller kan köra du en manuell kontroll. Högerklicka på skyddsgruppen i området i Azure Backup Server-konsolen och välj sedan **utför konsekvenskontroll**.

    Klicka på **nästa** att flytta till nästa sida.

9. På den **ange Onlineskyddsdata** väljer du en eller flera datakällor som du vill skydda. Du kan välja medlemmarna individuellt eller klicka på **Markera alla** att välja alla medlemmar. När du har valt medlemmar klickar du på **nästa**.

    ![Ange onlineskyddsdata](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. På den **Ange schema för onlinesäkerhetskopiering** anger schemat för att skapa återställningspunkter från säkerhetskopian på disk. När återställningspunkten har skapats, överförs den till Recovery Services-valv i Azure. När du är nöjd med onlinesäkerhetskopieringsschemat **nästa**.

    ![Ange onlinesäkerhetskopieringsschema](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. På den **ange bevarandeprincip** kan ange hur länge du vill behålla säkerhetskopierade data i Azure. När principen har definierats, klickar du på **nästa**.

    ![Ange princip för onlinebevarande](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Det finns ingen tidsbegränsning för hur länge du kan behålla data i Azure. När du lagrar recovery point data i Azure kan är den ena begränsningen att du inte kan ha fler än 9999 återställningspunkter per skyddad instans. I det här exemplet är den skyddade instansen VMware-servern.

12. På den **sammanfattning** sidan Granska informationen för skyddsgruppmedlemmar och inställningar och klickar sedan på **Skapa grupp**.

    ![Medlem i skyddsgruppen och inställningen sammanfattning](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Nästa steg
Om du använder Azure Backup Server för att skydda VMware-arbetsbelastningar kan du kanske vill använda Azure Backup Server för att skydda en [Microsoft Exchange server](./backup-azure-exchange-mabs.md), ett [Microsoft SharePoint-servergruppen](./backup-azure-backup-sharepoint-mabs.md), eller en [SQL Server-databas](./backup-azure-sql-mabs.md).

Information om problem med att registrera agenten konfigurerar skyddsgruppen eller säkerhetskopiera jobb, finns [felsöka Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
