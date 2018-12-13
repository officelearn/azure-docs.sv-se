---
title: Säkerhetskopiera virtuella VMware-datorer med Azure Backup Server
description: Använda Azure Backup Server för säkerhetskopiering av virtuella VMware-datorer som körs på en VMware vCenter/ESXi-server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: ee7ebb151653b611c652c072b8cb4c07754d9b68
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269710"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Säkerhetskopiera virtuella VMware-datorer med Azure Backup Server

Den här artikeln förklarar hur du säkerhetskopierar virtuella VMware-datorer som körs på VMware ESXi-värdar/vCenter-Server till Azure med hjälp av Azure Backup Server. 

Den här artikeln förklarar hur du:

- Ställa in en säker kanal så att Azure Backup Server kan kommunicera med VMware-servrar via HTTPS.
- Konfigurera ett VMware-konto som Azure Backup Server använder för att få åtkomst till VMware-servern.
- Lägg till autentiseringsuppgifterna för kontot i Azure Backup.
- Lägg till vCenter- eller ESXi-server på Azure Backup Server.
- Konfigurera en skyddsgrupp som innehåller VMware virtuella datorer som du vill säkerhetskopiera, ange inställningar för säkerhetskopiering och schemalägga säkerhetskopieringen.

## <a name="before-you-start"></a>Innan du börjar
- Kontrollera att du kör en version av vCenter/ESXi som stöds för säkerhetskopiering - version 6.5, 6.0 och 5.5. 
- Kontrollera att du har konfigurerat Azure Backup Server. Om du inte gjort [gör](backup-azure-microsoft-azure-backup.md) innan du börjar. Du bör köra Azure Backup Server med de senaste uppdateringarna.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter-servern

Som standard kommunicerar Azure Backup Server med VMware-servrar via HTTPS. Om du vill konfigurera HTTPS-anslutning, ladda ned certifikatet för VMware certifikatutfärdaren (CA) och importera det på Azure Backup Server. 


### <a name="before-you-start"></a>Innan du börjar

- Om du inte vill använda HTTPS kan du [inaktivera standardinställningen](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol).
- Du ansluter vanligtvis från en webbläsare på Azure Backup Server-datorn till vCenter/ESXi-servern med vSphere-webbklienten. Första gången du gör detta anslutningen är inte säker och visar följande.
- Det är viktigt att förstå hur Azure Backup Server hanterar säkerhetskopior.
    - Som ett första steg säkerhetskopierar Azure Backup Server data till lokalt diskutrymme. Azure Backup Server använder en lagringspool, en uppsättning diskar och volymer där Azure Backup Server lagrar diskåterställningspunkter för skyddade data. Lagringspoolen kan vara direktansluten lagring (DAS), en fiber channel SAN- eller iSCSI-lagringsenhet eller SAN. Det är viktigt att se till att du har tillräckligt med lagringsutrymme för lokala tillbaka dina VMware VM-data.
    - Azure Backup Server sedan säkerhetskopierar från lokalt diskutrymme till Azure.
    - [Få hjälp](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) att ta reda på hur mycket lagringsutrymme du behöver. Informationen är för DPM, men kan användas för Azure Backup Server för.

### <a name="set-up-the-certificate"></a>Ställ in certifikatet 

Konfigurera en säker kanal enligt följande:

1. Ange vSphere-Klientwebbadress i webbläsaren på Azure Backup Server. Om inloggningssidan inte visas, kontrollera proxy-inställningar för anslutning och webbläsare.

    ![vSphere-webbklienten](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Klicka på inloggningssidan för vSphere-webbklienten **Download betrodda rotcertifikatutfärdare**. 

    ![Hämta certifikat från betrodd rotcertifikatutfärdare](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. En fil med namnet **hämta** hämtas. Beroende på din webbläsare får du ett meddelande som frågar om du vill öppna eller spara filen.

    ![Hämta Certifikatutfärdarens certifikat](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Spara filen på Azure Backup Server-datorn med filtillägget .zip.

5. Högerklicka på **download.zip** > **extrahera alla**. ZIP-filen extraherar innehållet till den **certifikat** mappen som innehåller:
    - Filen med rotcertifikatet med ett tillägg som börjar med en numrerad sekvens som.0 och.1.
    - CRL-filen har ett tillägg som börjar med en sekvens som .r0 eller .r1. CRL-filen är associerad med ett certifikat.

    ![Hämtade certifikat](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. I den **certifikat** mapp, högerklickar du på filen med rotcertifikatet > **Byt namn på**.

    ![Byt namn på rotcertifikat ](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Ändra det rotcertifikatet tillägget till .crt och bekräfta. Filikonen ändras till ett som representerar ett rotcertifikat.

7. Högerklicka på rotcertifikatet och popup-menyn, Välj **installera certifikat**. 

8. I **guiden Importera certifikat**väljer **lokal dator** som mål för den certifikatet och sedan på **nästa**. Bekräfta om du blir tillfrågad om du vill tillåta ändringar på datorn.

    ![Välkommen till guiden](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)
 

9. På den **Certificate Store** väljer **placera alla certifikat i nedanstående arkiv**, och klicka sedan på **Bläddra** välja certifikatarkivet.

    ![Certifikatlagring](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. I **Välj certifikatet Store**väljer **betrodda rotcertifikatutfärdare** som målmapp för certifikat och klicka sedan på **OK**.

    ![Certifikatet målmapp](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. I **du har slutfört guiden Importera certifikat**, kontrollera mappen och klicka sedan på **Slutför**.

    ![Kontrollera att certifikatet är i rätt mapp](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    
12. Logga in på vCenter-servern för att kontrollera att anslutningen är säker när Importera certifikat har bekräftats.


  

### <a name="disable-default-https"></a>Inaktivera standard HTTPS

Om du har säkert gränser i din organisation och inte vill använda HTTPS-protokollet mellan VMware-servrar och Azure Backup Server-datorn, inaktivera HTTPS på följande sätt: u
1. Kopiera och klistra in följande text i en txt-fil.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. Spara filen på Azure Backup Server-datorn med namnet **DisableSecureAuthentication.reg**.

3. Dubbelklicka på filen för att aktivera registerposten.


## <a name="create-a-vmware-role"></a>Skapa en VMware-roll

Azure Backup Server behöver ett användarkonto med behörighet att komma åt v Center-servern/ESXi-värden. Skapa en VMware-roll med specifika privilegier och sedan associerar ett användarkonto med rollen.

1. Logga in på vCenter-Server (eller ESXi-värden om du inte använder vCenter-servern).
2. I den **Navigator** panelen, klickar du på **Administration**.

    ![Administration ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. I **Administration** > **roller**, klicka på ikonen Lägg till roll (den symbolen +).

    ![Lägg till roll](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    
4. I **skapa roll** > **rollnamn**, ange *BackupAdminRole*. Rollnamnet kan vara vad du vill, men det bör vara att känna igen för rollens ändamål.

5. Välj behörigheterna som sammanfattas i tabellen nedan och klicka sedan på **OK**.  Den nya rollen visas i listan i den **roller** panelen.
    - Klicka på ikonen bredvid den överordnade etiketten Expandera överordnat och visa behörigheter som underordnade.
    - För att välja behörigheter som VirtualMachine, måste du börja överordnad-underordnad hierarki med flera nivåer.
    - Du behöver inte välja alla underordnade privilegier i en överordnad-behörighet.

    ![Hierarkin behörighet överordnad-underordnad](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Rollbehörigheter
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField | 
Host.Local.CreateVM | Network.Assign 
Network.Assign | 
Resource.AssignVMToPool | 
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking 
VirtualMachine.Config.HostUSBDevice | 
VirtualMachine.Config.QueryUnownedFiles | 
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement 
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff 
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create 
VirtualMachine.Provisioning.DiskRandomAccess | 
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot 




## <a name="create-a-vmware-account"></a>Skapa ett VMware-konto

1. I vCenter Server **Navigator** panelen, klickar du på **användare och grupper**. Om du inte använder vCenter-servern, skapa kontot på lämplig ESXi-värden.

    ![Alternativ för användare och grupper](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Den **vCenter användare och grupper** visas panelen.


2. I den **vCenter användare och grupper** fönstret väljer den **användare** fliken och klicka sedan på ikonen Lägg till användare (den symbolen +).

     ![vCenter-panelen för användare och grupper](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. I **ny användare** dialogrutan lägger du till användarinformationen > **OK**. I det här är användarnamnet BackupAdmin.

    ![Dialogrutan Ny användare](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. Associera användarkonton med rollen i den **Navigator** panelen, klickar du på **globala behörigheter**. I den **globala behörigheter** fönstret väljer den **hantera** fliken och klicka sedan på ikonen Lägg till (den symbolen +).

    ![Globala behörigheter panelen](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. I **globala behörighet Root – Lägg till behörighet**, klickar du på **Lägg till** att välja den användare eller grupp.

    ![Välj användare eller grupp](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. I **Välj användare/grupper**, Välj **BackupAdmin** > **Lägg till**. I **användare**, *domän\användarnamn* format används för användarkontot. Om du vill använda en annan domän väljer du det från den **domän** lista. Klicka på **OK** att lägga till de valda användarna att den **Lägg till behörighet** dialogrutan.

    ![Lägg till BackupAdmin användare](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  I **tilldelas rollen**, från den nedrullningsbara listan, Välj **BackupAdminRole** > **OK**.

    ![Tilldela användare till rollen](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


På den **hantera** fliken i den **globala behörigheter** panelen, det nya användarkontot och den associera rollen visas i listan.


## <a name="add-the-account-on-azure-backup-server"></a>Lägga till kontot på Azure Backup Server


1. Öppna Azure Backup Server. Om du inte hittar ikonen på skrivbordet, öppnar du Microsoft Azure Backup från listan över appar.

    ![Azure Backup Server-ikon](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. I Azure Backup Server-konsolen klickar du på **Management** >  **produktionsservrar** > **hantera VMware**.

    ![Azure Backup Server-konsolen](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. I den **hantera autentiseringsuppgifter** dialogrutan klickar du på **Lägg till**.

    ![Dialogrutan för Azure Backup Server hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. I **Lägg till autentiseringsuppgift** , ange ett namn och en beskrivning för de nya autentiseringsuppgifter och ange användarnamnet och lösenordet som du definierade på VMware-servern. Namn, *Contoso Vcenter credential* används för att identifiera autentiseringsuppgifterna i den här proceduren. Om VMware-servern och Azure Backup Server inte är i samma domän kan du ange domänen i användarnamnet.

    ![Dialogrutan för Azure Backup Server Lägg till autentiseringsuppgift](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Klicka på **Lägg till** att lägga till de nya autentiseringsuppgifter.

    ![Dialogrutan för Azure Backup Server hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>Lägg till vCenter-servern 

Lägg till vCenter-servern i Azure Backup Server.


1. I Azure Backup Server-konsolen klickar du på **Management** > **produktionsservrar** > **Lägg till**.

    ![Guiden lägga till Open produktionsserver](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)
   

2. I **guiden lägga till produktionsserver** > **väljer produktion servertyp** väljer **VMware-servrar**, och klicka sedan på **nästa**.

     ![Guiden lägga till produktionsserver](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. I **Välj datorer****serverns namn/IP-adress**, anger du FQDN eller IP-adressen för VMware-servern.   Om alla ESXi-servrar hanteras av samma vCenter, anger du namnet på vCenter. Annars kan du lägga till ESXi-värden.

    ![Ange VMware-server](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. I **SSL-porten**, ange den port som används för att kommunicera med VMware-servern. 443 är standardporten, men du kan ändra det om VMware-servern lyssnar på en annan port.

5. I **ange autentiseringsuppgifter**, Välj den autentiseringsuppgift som du skapade tidigare.

    ![Ange autentiseringsuppgift](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klicka på **Lägg till** att lägga till VMware-servern i listan med hanteringsservrar. Klicka sedan på **Nästa**.

    ![Lägg till VMWare-servern och autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. I den **sammanfattning** klickar du på **Lägg till** lägga till VMware-server i Azure Backup Server. Den nya servern läggs omedelbart, krävs ingen agent på VMware-servern.

    ![Lägg till VMware-server i Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Kontrollera inställningarna på den **Slutför** sidan.

  ![Sidan Slutför](./media/backup-azure-backup-server-vmware/summary-screen.png)

Om du har flera ESXi-värdar som inte hanteras av vCenter-server, eller du har flera instanser av vCenter Server, måste du köra guiden för att lägga till servrar. 




## <a name="configure-a-protection-group"></a>Konfigurera en skyddsgrupp

Lägg till virtuella VMware-datorer för säkerhetskopiering. Skyddsgrupper samla flera virtuella datorer och använda samma kvarhållning av data och inställningar för säkerhetskopiering för alla virtuella datorer i gruppen. 


1. I Azure Backup Server-konsolen klickar du på **Protection**, > **New**.

    ![Öppna guiden Skapa ny Skyddsgrupp](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

2. I den **Skapa ny Skyddsgrupp** välkomstsidan i guiden klickar du på **nästa**.

    ![Skapa ny Skyddsgrupp dialogruta](./media/backup-azure-backup-server-vmware/protection-wizard.png)

3. På den **typ av Välj Skyddsgrupp** väljer **servrar** och klicka sedan på **nästa**. Den **Välj gruppmedlemmar** visas.

3. I **Välj gruppmedlemmar** > Välj de virtuella datorerna (eller virtuell dator mappar) som du vill säkerhetskopiera. Klicka sedan på **Nästa**.

    - När du markerar en mapp eller virtuella datorer eller mappar inuti den mappen som också har valts för säkerhetskopiering. Du kan avmarkera mappar eller virtuella datorer som du inte vill säkerhetskopiera.
- Om en virtuell dator eller en mapp redan säkerhetskopieras, kan du välja den. På så se till att dubbla återställningspunkter inte skapas för en virtuell dator. .

    ![Välj gruppmedlemmar](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


4. I **Välj Dataskyddsmetod** anger du ett namn för skyddsgruppen och inställningar. Tillbaka till Azure, ange skydd på kort sikt till **Disk** och aktiverar onlineskydd. Klicka sedan på **Nästa**.

    ![Välj dataskyddsmetod](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. I **ange kortsiktiga mål**, ange hur länge du vill behålla data som säkerhetskopieras till disk.
    - I **Kvarhållningsintervall**, ange hur många dagar som diskåterställningspunkter bör hållas. 
    - I **Synkroniseringsfrekvens**, anger du hur ofta återställningspunkter för disken skapas.
        - Om du inte vill ange en säkerhetskopiering intervall kan du kontrollera **precis innan en återställningspunkt** så att en säkerhetskopiering körs precis innan varje återställningspunkt schemaläggs.
        - Kortsiktig säkerhetskopiering är fullständiga säkerhetskopior som inte är inkrementell.
        - Klicka på **ändra** att ändra den gånger/datum när kortvariga säkerhetskopieringar sker.

    ![Ange kortvariga mål](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. I **granska diskallokering**, granska diskutrymme för VM-säkerhetskopieringar. för de virtuella datorerna.

    - De rekommenderade diskallokering baseras på kvarhållningsintervallet som du angav, vilken typ av arbetsbelastning och storleken på skyddade data. Gör eventuella ändringar och klicka sedan på **nästa**.
    -  **Datastorlek:** Storleken på data i skyddsgruppen.
    - **Diskutrymme:** Den rekommenderade mängden ledigt diskutrymme för skyddsgruppen. Om du vill ändra den här inställningen allokerar du ett totalt utrymme som är något större än det belopp som du uppskattar att varje datakälla växer.
    - **Samordna data:** Om du aktiverar samordning, mappa flera datakällor i skyddet till en enda replik och återställningspunktvolym. Samordning stöds inte för alla arbetsbelastningar.
    - **Väx automatiskt:** Om du aktiverar den här inställningen om datan i skyddsgruppen blir större än den ursprungliga allokeringen kan försöker Azure Backup Server öka diskstorleken med 25 procent.
    - **Information om lagringspool:** Visar status för lagringspoolen, inklusive total och återstående diskstorlek.

    ![Granska diskallokering](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

7. I **väljer Replikskapandemetod** anger du hur du vill ta den första säkerhetskopieringen och klicka sedan på **nästa**.
    - Standardvärdet är **automatiskt över nätverket** och **nu**.
    - Om du använder standard, rekommenderar vi att du anger en tid med låg belastning. Välj **senare** och ange en dag och tidpunkt.
    - Överväg att replikera data offline med hjälp av flyttbara media för stora mängder data eller mindre än optimala nätverksförhållanden.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. I **alternativ för konsekvenskontroll**, Välj hur och när du vill automatisera konsekvenskontroller. Klicka sedan på **Nästa**.
    - Du kan köra en konsekvenskontroll när replikdata blir inkonsekvent, eller enligt ett schema.
    - Om du inte vill konfigurera automatiska konsekvenskontroller kan köra du en manuell kontroll. Gör detta genom att högerklicka på skyddsgruppen > **utför konsekvenskontroll**.

9. I **ange Onlineskyddsdata** väljer du de virtuella datorer eller VM mappar som du vill säkerhetskopiera. Du kan välja medlemmarna individuellt eller klicka på **Markera alla** att välja alla medlemmar. Klicka sedan på **Nästa**.

    ![Ange onlineskyddsdata](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. På den **Ange schema för Online Backup** anger du hur ofta du vill säkerhetskopiera data från lokal lagring till Azure.

    - Återställningspunkter i molnet för data skapas enligt schemat. Klicka sedan på **Nästa**.
    - När återställningspunkten har skapats, överförs den till Recovery Services-valv i Azure. 
    
    ![Ange onlinesäkerhetskopieringsschema](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. På den **ange bevarandeprincip** kan ange hur länge du vill behålla återställningspunkter som skapats från säkerhetskopior varje dag/vecka/månad/år till Azure. Klicka sedan på **nästa**.

    - Det finns ingen tidsbegränsning för hur länge du kan behålla data i Azure.
    - Den ena begränsningen är att du inte kan ha fler än 9999 återställningspunkter per skyddad instans. I det här exemplet är den skyddade instansen VMware-servern.

    ![Ange princip för onlinebevarande](./media/backup-azure-backup-server-vmware/retention-policy.png)

   
12. På den **sammanfattning** sidan, granskar du inställningarna och klicka sedan på **Skapa grupp**.

    ![Medlem i skyddsgruppen och inställningen sammanfattning](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Nästa steg

För att felsöka problem när du konfigurerar säkerhetskopieringar, granska de [felsökningsguide för Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
