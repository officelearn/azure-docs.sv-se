---
title: Säkerhetskopiera VMware-servrar med Azure Backup Server
description: Använda Azure Backup Server för att säkerhetskopiera en VMware vCenter/ESXi-servrar till Azure eller disk. Den här artikeln innehåller steg för steg-instruktion om att säkerhetskopiera (eller skydda) = VMware-arbetsbelastningar.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: 9cf3c9d5df11e19045cd47a41d7ab9ac93bdf700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605431"
---
# <a name="back-up-a-vmware-server-to-azure"></a>Säkerhetskopiera en VMware-server till Azure

Den här artikeln förklarar hur du konfigurerar Azure Backup-Server för att skydda VMware-serverarbetsbelastningar. Den här artikeln förutsätter att du redan har Azure Backup-Server installerad. Om du inte har Azure Backup-Server installerad, se [förbereda säkerhetskopiering av arbetsbelastningar med Azure Backup Server](backup-azure-microsoft-azure-backup.md).

Azure Backup-Server kan säkerhetskopiera eller att skydda VMware vCenter serverversion 6.5, 6.0 och 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter-servern

Azure Backup-servern kommunicerar med varje vCenter-servern via en HTTPS-kanal som standard. Om du vill aktivera säker kommunikation, rekommenderar vi att du installerar certifikatet VMware certifikatutfärdare (CA) på Azure Backup Server. Om du inte kräver säker kommunikation och föredrar att inaktivera kravet på HTTPS, se [inaktivera säker kommunikationsprotokoll](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Importera det betrodda certifikatet på Azure Backup Server för att skapa en säker anslutning mellan Azure Backup Server och vCenter-servern.

Normalt använder du en webbläsare på datorn med Azure Backup Server för att ansluta till vCenter-servern via vSphere webbklienten. Första gången du använder Azure Backup Server webbläsaren för att ansluta till vCenter-servern, anslutningen är inte säker. Följande bild visar osäker anslutning.

![Exempel på osäker anslutning till VMware-server](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Ladda ned de betrodda rotcertifikatutfärdare för att åtgärda problemet och skapa en säker anslutning.

1. Ange Webbadressen till vSphere webbklienten i webbläsaren på Azure Backup Server. VSphere webbklienten inloggningssidan visas.

    ![vSphere Webbklient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    Längst ned i informationen för administratörer och utvecklare att leta upp den **Download betrodda rotcertifikatutfärdarcertifikat** länk.

    ![Länk för att ladda ned de betrodda rotcertifikatutfärdare](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Om du inte ser inloggningssidan vSphere Webbklient, kontrollera i webbläsarens proxyinställningar.

2. Klicka på **Download betrodda rotcertifikatutfärdarcertifikat**.

    VCenter-servern hämtar en fil på den lokala datorn. Filnamnet heter **hämta**. Beroende på din webbläsare får du ett meddelande som frågar om du vill öppna eller spara filen.

    ![Hämta meddelande när certifikat hämtas](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Spara filen på en plats på Azure Backup Server. När du sparar filen, kan du lägga till filnamnstillägget ZIP.

    Filen är en .zip-fil som innehåller information om certifikat. Du kan använda extrahering verktyg med filnamnstillägget .zip.

4. Högerklicka på **download.zip**, och välj sedan **extrahera alla** extrahera innehållet.

    ZIP-filen extraherar innehållet till en mapp med namnet **certifikat**. Två typer av filer som visas i mappen certifikat. På rotcertifikatfilen har ett tillägg som börjar med en numrerad sekvens som.0 och.1.
    
    CRL-filen har ett tillägg som börjar med en sekvens som .r0 eller .r1. CRL-filen är associerad med ett certifikat.

    ![Hämta filen extraheras lokalt ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. I den **certifikat** mappen, högerklicka på rotcertifikatfilen och klicka på **Byt namn på**.

    ![Byt namn på rotcertifikat ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Ändra det rotcertifikat tillägget till .crt. När du tillfrågas om du inte vill du ändra filnamnstillägget Klicka **Ja** eller **OK**. Annars kan ändra du filens avsedda funktion. Ikon för filen ändras till en ikon som representerar ett rotcertifikat.

6. Högerklicka på rotcertifikatet och popup-menyn, Välj **installera certifikat**.

    Den **guiden Importera certifikat** dialogrutan visas.

7. I den **guiden Importera certifikat** dialogrutan **lokal dator** som mål för certifikat och klicka sedan på **nästa** att fortsätta.

    ![Certifikatet lagringsalternativ för mål ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Om du tillfrågas om du vill tillåta ändringar till datorn klickar du på **Ja** eller **OK**, för alla ändringar.

8. På den **certifikatarkiv** väljer **placera alla certifikat i nedanstående arkiv**, och klicka sedan på **Bläddra** välja certifikatarkivet.

    ![Placera certifikat i en specifik lagring punkt](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    Den **Välj certifikatarkiv** dialogrutan visas.

    ![Mappen certifikathierarkin för lagring](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Välj **betrodda rotcertifikatutfärdare** som målmapp för certifikat och klicka sedan på **OK**.

    ![Målmappen för certifikat](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    Den **betrodda rotcertifikatutfärdare** mappen bekräftas som certifikatarkivet. Klicka på **Nästa**.

    ![Certifikat-mappen](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. På den **Slutför guiden Importera certifikat** sidan, kontrollera att certifikatet finns i mappen önskade och klicka sedan på **Slutför**.

    ![Kontrollera att certifikatet är i rätt mapp](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    En dialogruta visas har importera lyckade certifikat bekräftats.

11. Logga in till vCenter-servern för att bekräfta att anslutningen är säker.

  Om det går inte att genomföra Importera certifikat och du kan inte upprätta en säker anslutning, VMware vSphere-dokumentationen finns på [Skaffa servercertifikat](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Om du har säker gränser inom din organisation och inte vill aktivera HTTPS-protokollet, kan du använda följande procedur för att inaktivera säker kommunikation.

### <a name="disable-secure-communication-protocol"></a>Inaktivera säker kommunikationsprotokollet

Om din organisation inte kräver HTTPS-protokollet, kan du använda följande steg för att inaktivera HTTPS. Skapa en registernyckel som ignorerar standardbeteendet för att inaktivera standardbeteendet.

1. Kopiera och klistra in följande text i en txt-fil.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Spara filen till Azure Backup Server-datorn. För filnamnet, använder du DisableSecureAuthentication.reg.

3. Dubbelklicka på filen för att aktivera registerposten.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Skapa en roll och användare på vCenter Server

En roll är en fördefinierad uppsättning behörigheter på vCenter Server. En vCenter Server-administratören skapar rollerna. Om du vill tilldela behörigheter par användarkonton med rollen som administratör. Skapa en roll med specifika privilegier för att upprätta nödvändiga autentiseringsuppgifter för att säkerhetskopiera vCenter Server-datorn, och sedan associera användarkonton med rollen.

Azure Backup-servern använder ett användarnamn och lösenord för att autentisera till vCenter-servern. Azure Backup-servern använder autentiseringsuppgifterna som autentisering för alla säkerhetskopieringsåtgärder.

Lägg till en vCenter-serverrollen och dess behörighet för en säkerhetskopiering administratör:

1. Logga in till vCenter-servern och sedan i vCenter Server **Navigator** klickar du på **Administration**.

    ![Administrationsalternativ vCenter Server Navigator Kontrollpanelen](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. I **Administration** Välj **roller**, och klicka sedan på den **roller** Kontrollpanelen klickar du på ikonen Lägg till roll (den symbolen +).

    ![Lägg till roll](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    Den **skapa roll** dialogrutan visas.

    ![Skapa en roll](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. I den **skapa roll** i dialogrutan den **rollnamn** ange *BackupAdminRole*. Rollnamnet kan vara vad du vill, men det bör vara att känna igen för rollens ändamål.

4. Välj behörigheter för rätt version av vCenter och klicka sedan på **OK**. I följande tabell visas behörigheter som krävs för vCenter 6.0 och vCenter 5.5.

  När du väljer behörigheter, klickar du på ikonen bredvid överordnade etiketten Expandera överordnat och visa behörigheterna som underordnade. Om du vill välja VirtualMachine behörigheter som du behöver gå flera nivåer i hierarkin för överordnad-underordnad. Du behöver inte markera alla underordnade privilegier i privilegium som överordnad.

  ![Privilegiet hierarkin överordnad-underordnad](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  När du klickar på **OK**, den nya rollen visas i listan på panelen roller.

|Behörigheter för vCenter 6.0| Behörigheter för vCenter 5.5|
|--------------------------|---------------------------|
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



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Skapa ett användarkonto för vCenter-servern och behörigheter

Skapa ett användarkonto när rollen med privilegier som har konfigurerats. Användarkontot har ett namn och lösenord, som innehåller de autentiseringsuppgifter som används för autentisering.

1. Skapa ett användarkonto i vCenter Server **Navigator** klickar du på **användare och grupper**.

    ![Alternativet för användare och grupper](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Den **vCenter användare och grupper** panelen visas.

    ![vCenter-panelen för användare och grupper](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. I den **vCenter användare och grupper** Kontrollpanelen, Välj den **användare** fliken och klicka sedan på ikonen Lägg till användare (i symbolen +).

    Den **ny användare** dialogrutan visas.

3. I den **ny användare** dialogrutan, Lägg till information om användaren och klicka sedan **OK**. Användarnamnet är BackupAdmin i den här proceduren.

    ![Dialogrutan Ny användare](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Det nya användarkontot visas i listan.

4. Associera användarkonton med rollen, i den **Navigator** klickar du på **globala behörigheter**. I den **globala behörigheter** Kontrollpanelen, Välj den **hantera** fliken och klicka sedan på ikonen Lägg till (den symbolen +).

    ![Globala behörigheter panelen](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    Den **globala behörigheter rot - Lägg till behörigheten** dialogrutan visas.

5. I den **globala behörighet rot - Lägg till behörigheten** dialogrutan klickar du på **Lägg till** att välja den användare eller grupp.

    ![Välj användare eller grupp](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    Den **Välj användare eller grupper** dialogrutan visas.

6. I den **Välj användare eller grupper** dialogrutan Välj **BackupAdmin** och klicka sedan på **Lägg till**.

    I **användare**, *domän\användarnamn* formatet används för användarkontot. Om du vill använda en annan domän, väljer du det från den **domän** lista.

    ![Lägg till BackupAdmin användare](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Klicka på **OK** att lägga till de valda användarna till den **lägga till behörigheten** dialogrutan.

7. Nu när du har identifierat användaren, tilldelar du användaren rollen. I **tilldelas rollen**, från listrutan, Välj **BackupAdminRole**, och klicka sedan på **OK**.

    ![Tilldela användare till rollen](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  På den **hantera** fliken i den **globala behörigheter** panelen, det nya användarkontot och tillhörande rolltjänster som visas i listan.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Upprätta vCenter serverautentiseringsuppgifter för Azure Backup Server

Innan du lägger till VMware server Azure Backup Server, installera [uppdatering 1 för Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Öppna Azure Backup Server Dubbelklicka på ikonen på Azure Backup Server-datorn.

    ![Azure Backup Server-ikon](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Om du inte hittar ikonen på skrivbordet, öppna Azure Backup Server i listan över installerade appar. Appnamnet Azure Backup Server kallas för Microsoft Azure Backup.

2. Azure Backup Server-konsolen, klicka på **Management**, klickar du på **produktionsservrar**, och klicka på verktygsfliken **hantera VMware**.

    ![Azure Backup Server-konsolen](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    Den **hantera autentiseringsuppgifter** dialogrutan visas.

    ![Dialogrutan för Azure Backup Server hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. I den **hantera autentiseringsuppgifter** dialogrutan klickar du på **Lägg till** att öppna den **lägga till autentiseringsuppgifter** dialogrutan.

4. I den **lägga till autentiseringsuppgifter** dialogrutan Ange ett namn och en beskrivning för den nya autentiseringsuppgiften. Ange användarnamn och lösenord. Namnet *Contoso Vcenter autentiseringsuppgifter* används för att identifiera autentiseringsuppgifterna i nästa procedur. Använd samma användarnamn och lösenord som används för vCenter-servern. Om vCenter Server och Azure Backup Server inte är i samma domän, i **användarnamn**, ange domänen.

    ![Azure Backup Server lägga till autentiseringsuppgifter i dialogrutan](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Klicka på **Lägg till** att lägga till de nya autentiseringsuppgifter i Azure Backup Server. De nya autentiseringsuppgifter som visas i listan i den **hantera autentiseringsuppgifter** dialogrutan.
    
    ![Dialogrutan för Azure Backup Server hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Stäng den **hantera autentiseringsuppgifter** dialogrutan klickar du på den **X** i det övre högra hörnet.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Lägg till vCenter-servern till Azure Backup-Server

Produktion Server tillägg guiden används för att lägga till vCenter-servern till Azure Backup-Server.

Slutför följande procedur för att öppna guiden för produktion Server tillägg:

1. Azure Backup Server-konsolen, klicka på **Management**, klickar du på **produktionsservrar**, och klicka sedan på **Lägg till**.

    ![Öppna produktion tillägg guiden](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    Den **produktion tillägg guiden** dialogrutan visas.

    ![Guiden för produktion Server-tillägg](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. På den **Välj produktionsservern typen** väljer **VMware-servrar**, och klicka sedan på **nästa**.

3. I **Server namn eller IP-adress**, ange det fullständigt kvalificerade domännamnet (FQDN) eller VMware-serverns IP-adress. Om alla ESXi-servrar hanteras av samma vCenter, kan du använda vCenter-namn.

    ![Ange VMware server FQDN eller IP-adress](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. I **SSL-Port**, ange den port som används för att kommunicera med VMware-servern. Använda port 443, som är standardporten, såvida du inte vet att en annan port krävs.

5. I **ange autentiseringsuppgifter**, Välj de autentiseringsuppgifter som du skapade tidigare.

    ![Ange autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klicka på **Lägg till** lägga till VMware-server i listan över **lägga till VMware-servrar**, och klicka sedan på **nästa** att flytta till nästa sida i guiden.

    ![Lägg till VMWare-server och autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. I den **sammanfattning** klickar du på **Lägg till** att lägga till den angivna VMware-servern till Azure Backup-Server.

    ![Lägga till VMware-server i Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  VMware server backup är en agentlös säkerhetskopiering och den nya servern läggs omedelbart. Den **Slutför** sidan visas resultaten.

  ![Sidan Slutför](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Upprepa föregående steg i det här avsnittet för att lägga till flera instanser av vCenter-servern till Azure Backup-Server.

När du lägger till vCenter-servern till Azure Backup Server, är nästa steg att skapa en skyddsgrupp. Skyddsgruppen anger detaljer för kortsiktig eller långsiktig kvarhållning och det är där du definierar och tillämpa principen för säkerhetskopiering. Principen för säkerhetskopiering är schema för när säkerhetskopiering sker och vad som säkerhetskopieras.


## <a name="configure-a-protection-group"></a>Konfigurera en skyddsgrupp

Om du inte har använt System Center Data Protection Manager eller Azure Backup Server innan du läser [planera för säkerhetskopiering till disk](https://technet.microsoft.com/library/hh758026.aspx) att förbereda maskinvarumiljön. När du har kontrollerat att du har rätt lagring kan du använda guiden Skapa ny Skyddsgrupp för att lägga till virtuella VMware-datorer.

1. Azure Backup Server-konsolen, klicka på **skydd**, och klicka på verktygsfliken **ny** att öppna guiden Skapa ny Skyddsgrupp.

    ![Öppna guiden Skapa ny Skyddsgrupp](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    Den **Skapa ny Skyddsgrupp** dialogruta visas.

    ![Dialogruta för guiden Skapa ny Skyddsgrupp](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Klicka på **nästa** att gå vidare till den **Välj typ av skyddsgrupp** sidan.

2. På den **typ av Välj Skyddsgrupp** väljer **servrar** och klicka sedan på **nästa**. Den **Välj gruppmedlemmar** visas.

3. På den **Välj gruppmedlemmar** sida, tillgängliga medlemmar och de markerade medlemmarna visas. Välj de medlemmar som du vill skydda och klicka sedan på **nästa**.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    När du väljer en medlem, om du markerar en mapp som innehåller andra mappar eller virtuella datorer, väljs även dessa mappar och virtuella datorer. Inkludering av mappar och virtuella datorer i den överordnade mappen kallas mappnivå skydd. Avmarkera kryssrutan om du vill ta bort en mapp eller en VM.

    Om en virtuell dator eller en mapp som innehåller en virtuell dator är redan skyddade till Azure kan välja du inte den virtuella datorn igen. Det vill säga när en virtuell dator skyddas i Azure, kan du inte skydda igen, som förhindrar att duplicerade återställningspunkter skapas för en virtuell dator. Om du vill se vilka Azure Backup Server-instansen redan skyddar en medlem, peka på medlemmen ska se namnet på servern som skyddar.

4. På den **Välj Dataskyddsmetod** anger du ett namn för skyddsgruppen. Kortsiktigt skydd (på disk) och online-skydd är markerade. Om du vill använda onlineskydd (för Azure) använder du kortvarigt skydd till disk. Klicka på **nästa** fortsätta till intervallet för kortsiktigt skydd.

    ![Välj dataskyddsmetod](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. På den **ange kortvariga mål** sidan för **Kvarhållningsintervall**, ange antalet dagar som du vill behålla återställningspunkter som är *lagrade till disk*. Om du vill ändra tid och dagar när återställningspunkter tas på **ändra**. Kortsiktig återställningspunkter är fullständiga säkerhetskopieringar. De är inte inkrementella säkerhetskopieringar. När du är nöjd med kortsiktiga mål klickar du på **nästa**.

    ![Ange kortsiktiga mål](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. På den **granska diskallokering** granskar och om det behövs ändrar du hur mycket diskutrymme för de virtuella datorerna. De rekommenderade diskallokering baseras på kvarhållningsintervallet som anges i den **ange kortvariga mål** sidan typ av arbetsbelastning och storleken på skyddade data (som identifieras i steg 3).  

  - **Datastorlek:** storleken på data i skyddsgruppen.
  - **Diskutrymme:** den rekommenderade mängden ledigt diskutrymme för skyddsgruppen. Om du vill ändra den här inställningen allokerar du ett totalt utrymme som är något större än vad du uppskattar att varje datakälla växer.
  - **Samordna data:** om du aktiverar samordning kan flera datakällor i skyddet kan mappa till en enda replik- och datapunktsvolym. Samordning stöds inte för alla arbetsbelastningar.
  - **Utöka automatiskt:** om du aktiverar den här inställningen om data i gruppen skyddade blir större än den ursprungliga fördelningen, System Center Data Protection Manager försöker öka diskstorleken på med 25 procent.
  - **Lagringspoolinformation:** visar status för lagringspoolen, inklusive total och återstående diskstorlek.

    ![Granska diskallokering](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    När du är nöjd med utrymmesallokeringen klickar du på **nästa**.

7. På den **Välj Replikskapandemetod** anger du hur du vill skapa den ursprungliga kopian eller replik av skyddade data på Azure Backup Server.

    Standardvärdet är **automatiskt via nätverket** och **nu**. Om du använder standardinställningarna, rekommenderar vi att du anger en tid. Välj **senare** och ange en dag och tid.

    Överväg att replikera data offline med hjälp av flyttbart medium för stora mängder data eller mindre än optimala nätverksförhållanden.

    När du har gjort dina val klickar du på **nästa**.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. På den **alternativ för konsekvenskontroll** sidan, Välj hur och när att automatisera konsekvenskontroller. Du kan köra en konsekvenskontroll när replikdata blir inkonsekvent, eller på ett schema.

    Om du inte vill konfigurera automatiska konsekvenskontroller, kan du köra en manuell kontroll. I området för Azure Backup Server-konsolen, högerklickar på skyddsgruppen och välj sedan **utför konsekvenskontroll**.

    Klicka på **nästa** att flytta till nästa sida.

9. På den **ange Onlineskyddsdata** markerar du en eller flera datakällor som du vill skydda. Du kan välja medlemmarna individuellt eller klicka på **Markera alla** att välja alla medlemmar. När du har valt medlemmar klickar du på **nästa**.

    ![Ange onlineskyddsdata](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. På den **Ange schema för onlinesäkerhetskopiering** Ange schema för att skapa återställningspunkter från säkerhetskopian på disk. När återställningspunkten har skapats, överförs de till i Azure Recovery Services-valvet. När du är nöjd med onlinesäkerhetskopieringsschemat klickar du på **nästa**.

    ![Ange onlinesäkerhetskopieringsschema](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. På den **ange bevarandeprincip** kan ange hur länge du vill behålla säkerhetskopierade data i Azure. När principen har definierats, klickar du på **nästa**.

    ![Ange bevarandeprincip](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Det finns ingen tidsbegränsning för hur länge du kan behålla data i Azure. När du lagrar återställningsdata punkt i Azure kan är endast gränsen att du inte kan ha fler än 9999 återställningspunkter per skyddad instans. I det här exemplet är skyddade instansen VMware-servern.

12. På den **sammanfattning** , granska informationen för skyddsgruppmedlemmar och inställningar, och klickar sedan på **Skapa grupp**.

    ![Skyddsgruppsmedlem och inställningen sammanfattning](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Nästa steg
Om du använder Azure Backup Server för att skydda VMware arbetsbelastningar, du kan vara intresserad av att använda Azure Backup Server för att skydda en [Microsoft Exchange server](./backup-azure-exchange-mabs.md), [Microsoft SharePoint-servergruppen](./backup-azure-backup-sharepoint-mabs.md), eller en [SQL Server-databas](./backup-azure-sql-mabs.md).

Mer information om problem med att registrera agenten konfigurera skyddsgruppen eller för att säkerhetskopiera jobb finns [felsöka Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
