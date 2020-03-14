---
title: Säkerhetskopiera virtuella VMware-datorer med Azure Backup Server
description: I den här artikeln lär du dig hur du använder Azure Backup Server för att säkerhetskopiera virtuella VMware-datorer som körs på en VMware vCenter/ESXi-Server.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: df85cba42118a2e814a4a1c8338f3927e4d75f36
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273480"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Säkerhetskopiera virtuella VMware-datorer med Azure Backup Server

Den här artikeln beskriver hur du säkerhetskopierar virtuella VMware-datorer som körs på VMware ESXi värdar/vCenter Server till Azure med Azure Backup Server.

Den här artikeln förklarar hur du:

- Konfigurera en säker kanal så att Azure Backup Server kan kommunicera med VMware-servrar via HTTPS.
- Konfigurera ett VMware-konto som Azure Backup Server använder för att få åtkomst till VMware-servern.
- Lägg till kontoautentiseringsuppgifter till Azure Backup.
- Lägg till vCenter-eller ESXi-servern i Azure Backup Server.
- Konfigurera en skydds grupp som innehåller de virtuella VMware-datorer som du vill säkerhetskopiera, ange inställningar för säkerhets kopiering och Schemalägg säkerhets kopieringen.

## <a name="before-you-start"></a>Innan du börjar

- Kontrol lera att du använder en version av vCenter/ESXi som stöds för säkerhets kopiering. Se support mat ris [här](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix).
- Kontrol lera att du har konfigurerat Azure Backup Server. Om du inte gör det måste du [göra det](backup-azure-microsoft-azure-backup.md) innan du börjar. Du bör köra Azure Backup Server med de senaste uppdateringarna.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter Server

Azure Backup Server kommunicerar som standard med VMware-servrar via HTTPS. Om du vill konfigurera HTTPS-anslutningen laddar du ned VMware Certificate Authority-certifikatet (CA) och importerar det på Azure Backup Server.

### <a name="before-you-begin"></a>Innan du börjar

- Om du inte vill använda HTTPS kan du [inaktivera HTTPS-certifikat validering för alla VMware-servrar](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Du ansluter vanligt vis från en webbläsare på den Azure Backup Server datorn till vCenter/ESXi-servern med hjälp av vSphere-webbklienten. Första gången du gör detta är anslutningen inte säker och kommer att visa följande.
- Det är viktigt att förstå hur Azure Backup Server hanterar säkerhets kopieringar.
  - Det första steget Azure Backup Server säkerhetskopierar data till den lokala disk lagringen. Azure Backup Server använder en lagringspool, en uppsättning diskar och volymer där Azure Backup Server lagrar disk återställnings punkter för skyddade data. Lagringspoolen kan vara direktansluten lagring (DAS), en Fiber Channel SAN-enhet eller en iSCSI-lagringsenhet eller SAN. Det är viktigt att se till att du har tillräckligt med lagrings utrymme för lokal säkerhets kopiering av dina virtuella VMware-Datadata.
  - Azure Backup Server säkerhetskopierar sedan från den lokala disk lagringen till Azure.
  - [Få hjälp](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) med att räkna ut hur mycket lagrings utrymme du behöver. Informationen är för DPM men kan även användas för Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurera certifikatet

Konfigurera en säker kanal enligt följande:

1. I webbläsaren på Azure Backup Server anger du webb adressen för webb klienten vSphere. Om inloggnings sidan inte visas kontrollerar du inställningarna för anslutning och webbläsare.

    ![vSphere-webbklient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. På sidan för vSphere webb klient inloggning klickar du på **Hämta betrodda certifikat från rot certifikat utfärdare**.

    ![Hämta certifikat för betrodd rot certifikat utfärdare](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. En fil med namnet **Download** har laddats ned. Beroende på din webbläsare får du ett meddelande som frågar om du vill öppna eller spara filen.

    ![Hämta CA-certifikat](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Spara filen på Azure Backup Server dator med fil namns tillägget. zip.

5. Högerklicka på **Download. zip** > **extrahera alla**. Zip-filen extraherar dess innehåll till mappen **certifikat** , som innehåller:
   - Rot certifikat filen med ett tillägg som börjar med en numrerad sekvens som. 0 och. 1.
   - CRL-filen har ett tillägg som börjar med en sekvens som. R0 eller. R1. CRL-filen är associerad med ett certifikat.

    ![Hämtade certifikat](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. I mappen **certifikat** högerklickar du på rot certifikat filen > **byter namn**.

    ![Byt namn på rot certifikat](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Ändra rot certifikatets tillägg till. CRT och bekräfta. Fil ikonen ändras till en som representerar ett rot certifikat.

8. Högerklicka på rot certifikatet och välj **Installera certifikat**på popup-menyn.

9. I **guiden Importera certifikat**väljer du **lokal dator** som mål för certifikatet och klickar sedan på **Nästa**. Bekräfta om du tillfrågas om du vill tillåta ändringar i datorn.

    ![Välkommen till guiden](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. På sidan **certifikat Arkiv** väljer du **Placera alla certifikat i följande Arkiv**och klickar sedan på **Bläddra** för att välja certifikat arkivet.

    ![Certifikat lagring](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. I **Välj certifikat Arkiv**väljer du **betrodda rot certifikat utfärdare** som målmapp för certifikaten. Klicka sedan på **OK**.

    ![Målmapp för certifikat](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. När **du har slutfört guiden Importera certifikat**kontrollerar du mappen och klickar sedan på **Slutför**.

    ![Verifiera att certifikatet finns i rätt mapp](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. När certifikat importen har bekräftats loggar du in på vCenter Server för att bekräfta att anslutningen är säker.

### <a name="disable-https-certificate-validation"></a>Inaktivera verifiering av HTTPS-certifikat

Om du har säkra gränser i din organisation och inte vill använda HTTPS-protokollet mellan VMware-servrar och Azure Backup Server datorn inaktiverar du HTTPS på följande sätt:

1. Kopiera och klistra in följande text i en. txt-fil.

```text
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```

2. Spara filen på den Azure Backup Server datorn med namnet **DisableSecureAuthentication. reg**.

3. Dubbelklicka på filen för att aktivera register posten.

## <a name="create-a-vmware-role"></a>Skapa en VMware-roll

Azure Backup Server behöver ett användar konto med behörighet att komma åt v-Center-servern/ESXi-värden. Skapa en VMware-roll med vissa behörigheter och associera sedan ett användar konto med rollen.

1. Logga in på vCenter Server (eller ESXi-värden om du inte använder vCenter Server).
2. I **navigerings** panelen klickar du på **Administration**.

    ![Administration](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. I **administrations** > **roller**klickar du på ikonen Lägg till roll (symbolen +).

    ![Lägg till roll](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. I **skapa roll** > **roll namn**anger du *BackupAdminRole*. Roll namnet kan vara vad du vill, men det bör vara igenkännligt för rollens syfte.

5. Välj behörigheterna som sammanfattas i tabellen nedan och klicka sedan på **OK**.  Den nya rollen visas i listan på **roll** panelen.
   - Klicka på ikonen bredvid den överordnade etiketten för att expandera den överordnade och Visa de underordnade privilegierna.
   - Om du vill välja VirtualMachine-privilegier måste du gå flera nivåer till den överordnade underordnade hierarkin.
   - Du behöver inte markera alla underordnade behörigheter inom en överordnad behörighet.

    ![Hierarki för överordnad underordnad behörighet](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Roll behörigheter

| **Privilegier för vCenter 6,5 och över användar konto**        | **Behörigheter för vCenter 6,0-användar konto**               | **Behörigheter för vCenter 5,5-användar konto** |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------- |
| Data lager. AllocateSpace                                      |                                                           |                                             |
| Data lager. browse-datalager                                   | Data lager. AllocateSpace                                   | Nätverk. tilldela                              |
| Data lager för fil åtgärder på låg nivå                          | Global. Hantera anpassade attribut                           | Data lager. AllocateSpace                     |
| Data lager kluster. Konfigurera ett datatstore-kluster             | Globalt. Ange anpassat attribut                               | VirtualMachine.Config.ChangeTracking        |
| Global. inaktivera metoder                                       | Värd. local-åtgärder. Skapa virtuell dator              | VirtualMachine.State.RemoveSnapshot         |
| Globala. Aktivera metoder                                        | Nätverks. Tilldela nätverk                                   | VirtualMachine.State.CreateSnapshot         |
| Globala. licenser                                              | Klusterresursen. Tilldela en virtuell dator till en resurspool         | VirtualMachine.Provisioning.DiskRandomRead  |
| Global. log-händelse                                             | Virtuell dator. Konfiguration. Lägg till ny disk                | VirtualMachine.Interact.PowerOff            |
| Global. Hantera anpassade attribut                              | Virtuell dator. Konfiguration. Avancerat                    | VirtualMachine.Inventory.Create             |
| Globalt. Ange anpassat attribut                                  | Virtuell dator. Konfiguration. spårning av disk ändring        | VirtualMachine.Config.AddNewDisk            |
| Nätverk. tilldela nätverk                                       | Virtuell dator. Konfiguration. värd USB-enhet             | VirtualMachine.Config.HostUSBDevice         |
| Klusterresursen. Tilldela en virtuell dator till en resurspool            | Virtuell dator. Konfiguration. fråga efter filer som inte ägs         | VirtualMachine.Config.AdvancedConfig        |
| Virtuell dator. Konfiguration. Lägg till ny disk                   | Virtuell dator. Konfiguration. swapfile placering          | VirtualMachine.Config.SwapPlacement         |
| Virtuell dator. Konfiguration. Avancerat                       | Virtuell dator. Interaktion. Stäng av                     | Global.ManageCustomFields                   |
| Virtuell dator. Konfiguration. spårning av disk ändring           | Virtuell dator. Hantering. Skapa ny                     |                                             |
| Virtuell dator. Konfiguration. disk lån                     | Virtuell dator. Etablering. Tillåt disk åtkomst            |                                             |
| Virtuell dator. Konfiguration. utöka virtuell disk            | Virtuell dator. Etablerings. Tillåt skrivskyddad disk åtkomst |                                             |
| Virtuell dator. Gäst åtgärder. ändringar i gäst åtgärden | Virtuell dator. Ögonblicks bilds hantering. Skapa ögonblicks bild       |                                             |
| Virtuell dator. Gäst verksamhet. körning av gäst åtgärds program | Virtuell dator. Ögonblicks bilds hantering. Ta bort ögonblicks bild       |                                             |
| Virtuell dator. Gäst verksamhet. frågor om gäst åtgärder     |                                                           |                                             |
| Virtuell dator. Interaktion. Enhets anslutning              |                                                           |                                             |
| Virtuell dator. Interaktion. Hantering av gäst operativ system med VIX-API |                                                           |                                             |
| Virtuell dator. Inventering. register                          |                                                           |                                             |
| Virtuell dator. Inventering. ta bort                            |                                                           |                                             |
| Virtuell dator. Etablering. Tillåt disk åtkomst              |                                                           |                                             |
| Virtuell dator. Etablering. Tillåt skrivskyddad åtkomst till disk    |                                                           |                                             |
| Virtuell dator. Etablering. Tillåt nedladdning av virtuell dator |                                                           |                                             |
| Virtuell dator. Ögonblicks bilds hantering. Skapa en ögonblicksbild        |                                                           |                                             |
| Virtuell dator. Ögonblicks bilds hantering. Ta bort ögonblicks bild         |                                                           |                                             |
| Virtuell dator. Ögonblicks bilds hantering. Återgå till ögonblicks bild      |                                                           |                                             |
| vApp. Lägg till virtuell dator                                     |                                                           |                                             |
| vApp. tilldela resurspool                                    |                                                           |                                             |
| vApp. avregistrera                                              |                                                           |                                             |

## <a name="create-a-vmware-account"></a>Skapa ett VMware-konto

1. I panelen vCenter Server **Navigator** klickar **du på användare och grupper**. Om du inte använder vCenter Server skapar du kontot på lämplig ESXi-värd.

    ![Alternativet användare och grupper](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Panelen **vCenter-användare och-grupper** visas.

2. I panelen **vCenter-användare och-grupper** väljer du fliken **användare** och klickar sedan på ikonen Lägg till användare (symbolen +).

    ![panelen vCenter-användare och grupper](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. I dialog rutan **ny användare** lägger du till användar informationen > **OK**. I den här proceduren är användar namnet BackupAdmin.

    ![Dialog rutan ny användare](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Om du vill associera användar kontot med rollen går du till panelen **överblick** och klickar på **globala behörigheter**. I panelen **globala behörigheter** väljer du fliken **Hantera** och klickar sedan på ikonen Lägg till (symbolen +).

    ![Panelen globala behörigheter](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. I **global behörighet rot – Lägg till behörighet**, klickar du på **Lägg till** för att välja användaren eller gruppen.

    ![Välj användare eller grupp](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. I **Välj användare/grupper**väljer du **BackupAdmin** > **Lägg till**. I **användare**används formatet *domän \ användar namn* för användar kontot. Om du vill använda en annan domän väljer du den i listan **domän** . Klicka på **OK** för att lägga till de valda användarna i dialog rutan **Lägg till behörighet** .

    ![Lägg till BackupAdmin-användare](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. I den **tilldelade rollen**väljer du **BackupAdminRole** > **OK**i list rutan.

    ![Tilldela användare till roll](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

På fliken **Hantera** i panelen **globala behörigheter** visas det nya användar kontot och den associerade rollen i listan.

## <a name="add-the-account-on-azure-backup-server"></a>Lägg till kontot på Azure Backup Server

1. Öppna Azure Backup Server. Om du inte hittar ikonen på Skriv bordet öppnar du Microsoft Azure Backup från listan appar.

    ![Azure Backup Server ikon](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. I Azure Backup Server-konsolen klickar du på **hantering** >  **produktions servrar** > **Hantera VMware**.

    ![Azure Backup Server-konsol](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. I dialog rutan **Hantera autentiseringsuppgifter** klickar du på **Lägg till**.

    ![Dialog rutan Azure Backup Server Hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. I **Lägg till autentiseringsuppgift**anger du ett namn och en beskrivning för den nya autentiseringsuppgiften och anger det användar namn och lösen ord som du definierade på VMware-servern. Namnet, *contoso vCenter-autentiseringsuppgiften* används för att identifiera autentiseringsuppgifterna i den här proceduren. Om VMware-servern och Azure Backup Server inte finns i samma domän anger du domänen i användar namnet.

    ![Dialog rutan Azure Backup Server Lägg till autentiseringsuppgift](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Klicka på **Lägg** till för att lägga till den nya autentiseringsuppgiften.

    ![Dialog rutan Azure Backup Server Hantera autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Lägg till vCenter Server

Lägg till vCenter Server i Azure Backup Server.

1. I Azure Backup Server-konsolen klickar du på **hantering** > **produktions servrar** > **Lägg till**.

    ![Öppna guiden för att lägga till produktions server](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. I **guiden för att lägga till produktions server** > sidan **Välj typ av produktions server** väljer du **VMware-servrar**och klickar sedan på **Nästa**.

    ![Guiden för att lägga till produktions server](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. Ange FQDN eller IP-adressen för VMware-servern i **Välj datorer**  **Server namn/IP-adress**. Om alla ESXi-servrar hanteras av samma vCenter anger du vCenter-namnet. Annars lägger du till ESXi-värden.

    ![Ange VMware-Server](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. I **SSL-port**anger du den port som används för att kommunicera med VMware-servern. 443 är standard porten, men du kan ändra den om din VMware-Server lyssnar på en annan port.

5. I **Ange autentiseringsuppgift**väljer du de autentiseringsuppgifter som du skapade tidigare.

    ![Ange autentiseringsuppgift](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klicka på **Lägg** till för att lägga till VMware-servern i listan servrar. Klicka sedan på **Nästa**.

    ![Lägg till VMWare-Server och autentiseringsuppgift](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. På sidan **Sammanfattning** klickar du på **Lägg** till för att lägga till VMware-servern i Azure Backup Server. Den nya servern läggs till omedelbart, ingen agent behövs på VMware-servern.

    ![Lägg till VMware-Server i Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Kontrol lera inställningarna på sidan **Slutför** .

   ![Sidan Slutför](./media/backup-azure-backup-server-vmware/summary-screen.png)

Om du har flera ESXi-värdar som inte hanteras av vCenter Server eller om du har flera instanser av vCenter Server måste du köra guiden igen för att lägga till servrarna.

## <a name="configure-a-protection-group"></a>Konfigurera en skydds grupp

Lägg till virtuella VMware-datorer för säkerhets kopiering. Skydds grupper samlar in flera virtuella datorer och tillämpar samma data lagrings-och säkerhets kopierings inställningar för alla virtuella datorer i gruppen.

1. I Azure Backup Server-konsolen klickar du på **skydd**> **ny**.

    ![Öppna guiden Skapa ny skydds grupp](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klicka på **Nästa**på Välkomst sidan **Skapa ny skydds grupp** .

    ![Dialog rutan skapa ny skydds grupp guide](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. På sidan **Välj typ av skydds grupp** väljer du **servrar** och klickar sedan på **Nästa**. Sidan **Välj grupp medlemmar** visas.

1. I **Välj grupp medlemmar**väljer du de virtuella datorer (eller VM-mappar) som du vill säkerhetskopiera. Klicka sedan på **Nästa**.

    - När du väljer en mapp, eller virtuella datorer eller mappar i mappen, väljs även för säkerhets kopiering. Du kan avmarkera mappar och virtuella datorer som du inte vill säkerhetskopiera.
1. Om en virtuell dator eller mapp redan säkerhets kopie ras kan du inte välja den. Detta säkerställer att dubbla återställnings punkter inte skapas för en virtuell dator.

    ![Välj grupp medlemmar](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. På sidan **Välj data skydds metod** anger du ett namn på skydds gruppen och skydds inställningarna. Om du vill säkerhetskopiera till Azure ställer du in kortsiktigt skydd på **disk** och aktiverar onlineskydd. Klicka sedan på **Nästa**.

    ![Välj data skydds metod](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. I **Ange kortsiktiga mål**anger du hur länge du vill behålla säkerhetskopierade data till disk.
   - I **kvarhållningsintervall**anger du hur många dagar disk återställnings punkter ska behållas.
   - I **Synkroniseringsfrekvens**anger du hur ofta disk återställnings punkter ska tas.
       - Om du inte vill ange ett intervall för säkerhets kopiering kan du kontrol lera **precis innan en återställnings punkt** så att en säkerhets kopiering körs precis innan varje återställnings punkt schemaläggs.
       - Kortsiktiga säkerhets kopieringar är fullständiga säkerhets kopieringar och inte stegvisa.
       - Klicka på **ändra** om du vill ändra de tidpunkter/datum då kortsiktiga säkerhets kopieringar sker.

         ![Ange kortsiktiga mål](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Granska disk utrymmet för VM-säkerhetskopieringarna i **Granska diskallokering**. för de virtuella datorerna.

   - De rekommenderade disk tilldelningarna baseras på det kvarhållningsintervall du angav, typ av arbets belastning och storleken på skyddade data. Gör de ändringar som krävs och klicka sedan på **Nästa**.
   - **Data storlek:** Storlek på data i skydds gruppen.
   - **Disk utrymme:** Den rekommenderade mängden disk utrymme för skydds gruppen. Om du vill ändra den här inställningen bör du allokera totalt utrymme som är något större än det belopp som du uppskattar att varje data källa växer.
   - **Samplacera data:** Om du aktiverar samplacering kan flera data källor i skyddet mappas till en enda replik och återställnings punkt volym. Samplacering stöds inte för alla arbets belastningar.
   - **Utöka automatiskt:** Om du aktiverar den här inställningen och data i den skyddade gruppen utökar den inledande allokeringen, försöker Azure Backup Server öka disk storleken med 25 procent.
   - **Information om lagringspool:** Visar status för lagringspoolen, inklusive total och återstående disk storlek.

    ![Granska diskallokering](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. På sidan **Välj metod för skapande av replik** anger du hur du vill att den första säkerhets kopieringen ska utföras och klickar sedan på **Nästa**.
   - Standardvärdet sker **automatiskt över nätverket** och **nu**.
   - Om du använder standardvärdet rekommenderar vi att du anger en tid med låg belastning. Välj **senare** och ange dag och tid.
   - Överväg att replikera data offline med hjälp av flyttbara media för stora mängder data eller mindre än optimala nätverks förhållanden.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. I **alternativ för konsekvens kontroll**väljer du hur och när du vill automatisera konsekvens kontroller. Klicka sedan på **Nästa**.
      - Du kan köra konsekvens kontroller när replik data blir inkonsekventa eller enligt ett angivet schema.
      - Om du inte vill konfigurera automatiska konsekvens kontroller kan du köra en manuell kontroll. Det gör du genom att högerklicka på skydds gruppen > **utföra konsekvens kontroll**.

1. På sidan **Ange online skydds data** väljer du de virtuella datorer eller VM-mappar som du vill säkerhetskopiera. Du kan välja medlemmar individuellt eller klicka på **Välj alla** för att välja alla medlemmar. Klicka sedan på **Nästa**.

    ![Ange skydds data online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. På sidan **Ange schema för onlinesäkerhetskopiering** anger du hur ofta du vill säkerhetskopiera data från lokal lagring till Azure.

    - Moln återställnings punkter för data kommer att genereras enligt schemat. Klicka sedan på **Nästa**.
    - När återställnings punkten har skapats överförs den till Recovery Services valvet i Azure.

    ![Ange schemat för onlinesäkerhetskopiering](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. På sidan **Ange princip för kvarhållning av online** anger du hur länge du vill behålla återställnings punkterna som skapas med säkerhets kopieringar per dag/vecka/månad/år till Azure. Klicka sedan på **Nästa**.

    - Det finns ingen tids gräns för hur länge du kan lagra data i Azure.
    - Den enda begränsningen är att du inte kan ha mer än 9999 återställnings punkter per skyddad instans. I det här exemplet är den skyddade instansen VMware-servern.

    ![Ange bevarande princip online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. På sidan **Sammanfattning** granskar du inställningarna och klickar sedan på **Skapa grupp**.

    ![Skydds grupp medlem och inställnings Sammanfattning](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Om du vill säkerhetskopiera vSphere 6,7 gör du följande:

- Aktivera TLS 1,2 på DPM-servern

>[!NOTE]
>VMWare 6,7 och tidigare hade TLS aktiverat som kommunikations protokoll.

- Ange register nycklarna enligt följande:

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

## <a name="next-steps"></a>Nästa steg

Information om fel sökning av problem när du konfigurerar säkerhets kopieringar finns i [fel söknings guiden för Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
