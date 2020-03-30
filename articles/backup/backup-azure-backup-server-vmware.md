---
title: Säkerhetskopiera virtuella virtuella datorer med Virtuella datorer med Azure Backup Server
description: I den här artikeln kan du läsa om hur du använder Azure Backup Server för att säkerhetskopiera virtuella datorer med VMware som körs på en VMware vCenter/ESXi-server.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: df85cba42118a2e814a4a1c8338f3927e4d75f36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273480"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Säkerhetskopiera virtuella virtuella datorer med Virtuella datorer med Azure Backup Server

I den här artikeln beskrivs hur du säkerhetskopierar virtuella virtuella datorer med VMware som körs på VMware ESXi-värdar/vCenter Server till Azure med Azure Backup Server.

I den här artikeln beskrivs hur du:

- Konfigurera en säker kanal så att Azure Backup Server kan kommunicera med VMware-servrar via HTTPS.
- Konfigurera ett VMware-konto som Azure Backup Server använder för att komma åt VMware-servern.
- Lägg till kontoautentiseringsuppgifterna i Azure Backup.
- Lägg till vCenter- eller ESXi-servern i Azure Backup Server.
- Konfigurera en skyddsgrupp som innehåller de virtuella datorer med VMware som du vill säkerhetskopiera, ange inställningar för säkerhetskopiering och schemalägga säkerhetskopian.

## <a name="before-you-start"></a>Innan du börjar

- Kontrollera att du kör en version av vCenter/ESXi som stöds för säkerhetskopiering. Se supportmatrisen [här](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix).
- Kontrollera att du har konfigurerat Azure Backup Server. Om du inte har gjort [det, gör det](backup-azure-microsoft-azure-backup.md) innan du börjar. Du bör köra Azure Backup Server med de senaste uppdateringarna.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Skapa en säker anslutning till vCenter-servern

Som standard kommunicerar Azure Backup Server med VMware-servrar via HTTPS. Om du vill konfigurera HTTPS-anslutningen hämtar du VMware Certificate Authority -certifikatet och importerar det på Azure Backup Server.

### <a name="before-you-begin"></a>Innan du börjar

- Om du inte vill använda HTTPS kan du [inaktivera HTTPS-certifikatvalidering för alla VMware-servrar](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Du ansluter vanligtvis från en webbläsare på Azure Backup Server-datorn till vCenter/ESXi-servern med hjälp av webbklienten vSphere. Första gången du gör detta är anslutningen inte säker och visar följande.
- Det är viktigt att förstå hur Azure Backup Server hanterar säkerhetskopior.
  - Som ett första steg Azure Backup Server säkerhetskopierar data till lokal disklagring. Azure Backup Server använder en lagringspool, en uppsättning diskar och volymer där Azure Backup Server lagrar diskåterställningspunkter för skyddade data. Lagringspoolen kan anslutas direkt (DAS), en SAN-kanal för fiberkanal eller iSCSI-lagringsenhet eller SAN. Det är viktigt att se till att du har tillräckligt med lagringsutrymme för lokal säkerhetskopiering av dina VIRTUELLA VMware-data.
  - Azure Backup Server säkerhetskopierar sedan från den lokala disklagringen till Azure.
  - [Få hjälp](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) med att ta reda på hur mycket lagringsutrymme du behöver. Informationen är för DPM men kan också användas för Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurera certifikatet

Konfigurera en säker kanal enligt följande:

1. Ange URL:en för vSphere Web Client i webbläsaren på Azure Backup Server. Om inloggningssidan inte visas kontrollerar du anslutnings- och webbläsarproxyinställningarna.

    ![vSphere webbklient](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Klicka på **Hämta betrodda rotcertifikat på inloggningssidan**för vSphere Web Client .

    ![Hämta certifikat för betrodd rotcertifikat](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. En fil med namnet **hämtas.** Beroende på din webbläsare får du ett meddelande som frågar om filen ska öppnas eller sparas.

    ![Ladda ned CA-certifikat](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Spara filen på Azure Backup Server-datorn med ett ZIP-tillägg.

5. Högerklicka **download.zip** > **Extrahera alla**. Zip-filen extraherar innehållet till **mappen certs,** som innehåller:
   - Rotcertifikatfilen med ett tillägg som börjar med en numrerad sekvens som 0,0 och 0,1.
   - CRL-filen har ett tillägg som börjar med en sekvens som .r0 eller .r1. CRL-filen är associerad med ett certifikat.

    ![Hämtade certifikat](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Högerklicka på rotcertifikatfilen i **mappen certs** > **Byt namn**.

    ![Byta namn på rotcertifikat](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Ändra rotcertifikatets tillägg till .crt och bekräfta. Filikonen ändras till en som representerar ett rotcertifikat.

8. Högerklicka på rotcertifikatet och välj **Installera certifikat på**popup-menyn .

9. I **guiden Importera certifikat**väljer du Lokal **dator** som mål för certifikatet och klickar sedan på **Nästa**. Bekräfta om du tillfrågas om du vill tillåta ändringar på datorn.

    ![Välkommen till guiden](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. På sidan **Certifikatarkiv** väljer du **Placera alla certifikat i följande butik**och klickar sedan på **Bläddra** för att välja certifikatarkivet.

    ![Lagring av certifikat](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. I **Välj certifikatarkiv**väljer du **Betrodda rotcertifikatutfärdare** som målmapp för certifikaten och klickar sedan på **OK**.

    ![Målmapp för certifikat](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Kontrollera mappen **när du slutför guiden Importera certifikat**och klicka sedan på **Slutför**.

    ![Kontrollera att certifikatet finns i rätt mapp](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. När certifikatimporten har bekräftats loggar du in på vCenter-servern för att bekräfta att anslutningen är säker.

### <a name="disable-https-certificate-validation"></a>Inaktivera validering av HTTPS-certifikat

Om du har säkra gränser inom organisationen och inte vill använda HTTPS-protokollet mellan VMware-servrar och Azure Backup Server-datorn inaktiverar du HTTPS enligt följande:

1. Kopiera och klistra in följande text i en TXT-fil.

```text
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```

2. Spara filen på Azure Backup Server-datorn med namnet **DisableSecureAuthentication.reg**.

3. Dubbelklicka på filen för att aktivera registerposten.

## <a name="create-a-vmware-role"></a>Skapa en VMware-roll

Azure Backup Server behöver ett användarkonto med behörighet att komma åt v-Center Server/ESXi-värd. Skapa en VMware-roll med specifika privilegier och associera sedan ett användarkonto med rollen.

1. Logga in på vCenter Server (eller ESXi-värden om du inte använder vCenter Server).
2. Klicka på **Administration**på panelen **Navigatör** .

    ![Administration](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Klicka på ikonen Lägg till roll i > **Administrationsroller**(symbolen +). **Administration**

    ![Lägg till roll](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. Ange *BackupAdminRole*i Namn **skapa rollroll** > **Role name**. Rollnamnet kan vara vad du vill, men det bör vara igenkännligt för rollens syfte.

5. Markera de privilegier som sammanfattas i tabellen nedan och klicka sedan på **OK**.  Den nya rollen visas i listan på panelen **Roller.**
   - Klicka på ikonen bredvid den överordnade etiketten om du vill expandera den överordnade etiketten och visa de underordnade privilegierna.
   - Om du vill välja VirtualMachine-behörigheter måste du gå flera nivåer till den överordnade underordnade hierarkin.
   - Du behöver inte välja alla underordnade privilegier inom ett överordnat privilegium.

    ![Överordnad underordnad behörighetshierarki](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Behörigheter för roll

| **Privilegier för vCenter 6.5 och högre användarkonto**        | **Privilegier för vCenter 6.0-användarkonto**               | **Privilegier för vCenter 5.5-användarkonto** |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------- |
| Datastore.AllocateSpace                                      |                                                           |                                             |
| Datastore.Bläddra datastore                                   | Datastore.AllocateSpace                                   | Nätverk.Tilldela                              |
| Filåtgärder på datastore.Låg nivå                          | Global.Hantera anpassade attribut                           | Datastore.AllocateSpace                     |
| Datalagerkluster. Konfigurera ett datatstore-kluster             | Anpassat attribut för Global.Set                               | VirtualMachine.Config.ChangeTracking        |
| Global.Inaktivera metoder                                       | Host.Local-åtgärder. Skapa virtuell dator              | VirtualMachine.state.removeSnapshot         |
| Global.Enable-metoder                                        | Nätverk. Tilldela nätverk                                   | VirtualMachine.state.CreateSnapshot         |
| Global.Licenser                                              | Resurs. Tilldela virtuell dator till resurspoolen         | VirtualMachine.provisioning.DiskRandomRead  |
| Global.Log-händelse                                             | Virtuell dator. Configuration.Lägg till ny disk                | VirtualMachine.Interact.PowerOff            |
| Global.Hantera anpassade attribut                              | Virtuell dator. Configuration.Advanced                    | VirtualMachine.inventory.Create             |
| Anpassat attribut för Global.Set                                  | Virtuell dator. Configuration.Disk ändringsspårning        | VirtualMachine.Config.AddNewDisk            |
| Network.Assign nätverk                                       | Virtuell dator. Configuration.Host USB-enhet             | VirtualMachine.Config.HostUSBDevice         |
| Resurs. Tilldela virtuell dator till resurspoolen            | Virtuell dator. Configuration.Query ej egna filer         | VirtualMachine.Config.AdvancedConfig        |
| Virtuell dator. Configuration.Lägg till ny disk                   | Virtuell dator. Configuration.Swapfile-placering          | VirtualMachine.Config.SwapPlacement         |
| Virtuell dator. Configuration.Advanced                       | Virtuell dator. Interaction.Power Off                     | Global.ManageCustomFields                   |
| Virtuell dator. Configuration.Disk ändringsspårning           | Virtuell dator. Inventering. Skapa ny                     |                                             |
| Virtuell dator. Configuration.Disk-lån                     | Virtuell dator. Etablering.Tillåt diskåtkomst            |                                             |
| Virtuell dator. Configuration.Utöka virtuell disk            | Virtuell dator. Etableringen. Tillåt skrivskyddad diskåtkomst |                                             |
| Virtuell dator. Ändringar av gästoperationer.Ändringar av gäståtgärder | Virtuell dator. Hantering av ögonblicksbilder. Skapa ögonblicksbild       |                                             |
| Virtuell dator. Gästoperationer.Körning av gästoperationsprogram | Virtuell dator. Hantering av ögonblicksbilder. Ta bort ögonblicksbild       |                                             |
| Virtuell dator. Gäståtgärder.Frågor om gäståtgärder     |                                                           |                                             |
| Virtuell dator . Interaktion. Enhetsanslutning              |                                                           |                                             |
| Virtuell dator . Interaktion. Hantering av gästoperativsystem av VIX API |                                                           |                                             |
| Virtuell dator . Invent.Registrera                          |                                                           |                                             |
| Virtuell dator . Inventory.Ta bort                            |                                                           |                                             |
| Virtuell dator . Etablering.Tillåt diskåtkomst              |                                                           |                                             |
| Virtuell dator . Etablering.Tillåt skrivskyddad diskåtkomst    |                                                           |                                             |
| Virtuell dator . Provisioning.Tillåt hämtning av virtuella datorer |                                                           |                                             |
| Virtuell dator . Hantering av ögonblicksbilder. Skapa en ögonblicksbild        |                                                           |                                             |
| Virtuell dator . Hantering av ögonblicksbilder. Ta bort ögonblicksbild         |                                                           |                                             |
| Virtuell dator . Hantering av ögonblicksbilder. Återgå till ögonblicksbild      |                                                           |                                             |
| vApp.Add virtuell dator                                     |                                                           |                                             |
| vApp.Tilldela resurspool                                    |                                                           |                                             |
| vApp.Avregistrera                                              |                                                           |                                             |

## <a name="create-a-vmware-account"></a>Skapa ett VMware-konto

1. Klicka på Användare och **grupper**på panelen **VCenter Server Navigator** . Om du inte använder vCenter Server skapar du kontot på lämplig ESXi-värd.

    ![Alternativ för användare och grupper](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Panelen **vCenter användare och grupper** visas.

2. På panelen **vCenter användare och grupper** väljer du fliken **Användare** och klickar sedan på ikonen Lägg till användare (symbolen +).

    ![vCenter användare och grupper panel](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Lägg till användarinformationen > **OK**i dialogrutan **Ny användare** . I den här proceduren är användarnamnet BackupAdmin.

    ![Dialogrutan Ny användare](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Om du vill associera användarkontot med rollen klickar du på **Globala behörigheter**på panelen **Navigatör.** Markera fliken **Hantera** på panelen **Globala behörigheter** och klicka sedan på ikonen Lägg till (symbolen + ).

    ![Panelen Globala behörigheter](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. I **Global behörighetsrot - Lägg till behörighet**klickar du på **Lägg** till för att välja användaren eller gruppen.

    ![Välj användare eller grupp](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. I **Välj användare/grupper**väljer du **BackupAdmin** > **Add**. I **Användare**används *domänens användarnamnsformat* för användarkontot. Om du vill använda en annan domän väljer du den i **listan Domän.** Klicka på **OK** om du vill lägga till de markerade användarna i dialogrutan **Lägg till behörighet.**

    ![Lägg till BackupAdmin-användare](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. I **Tilldelad roll**väljer du **BackupAdminRole** > **OK**i listan Tilldelad roll .

    ![Tilldela användaren till rollen](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

På fliken **Hantera** på panelen **Globala behörigheter** visas det nya användarkontot och den associerade rollen i listan.

## <a name="add-the-account-on-azure-backup-server"></a>Lägga till kontot på Azure Backup Server

1. Öppna Azure Backup Server. Om du inte hittar ikonen på skrivbordet öppnar du Microsoft Azure Backup från applistan.

    ![Ikon för Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Klicka på Hantera**Production Servers** > **VMware** **i** >  Azure Backup Server-konsolen.

    ![Serverkonsol för Azure Backup](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Klicka på **Lägg till**i dialogrutan **Hantera autentiseringsuppgifter.**

    ![Dialogrutan Hantera autentiseringsuppgifter för Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. I **Lägg till autentiseringsuppgifter**anger du ett namn och en beskrivning av den nya autentiseringsdatabasen och anger det användarnamn och lösenord som du har definierat på VMware-servern. Namnet *Contoso Vcenter-referensen* används för att identifiera autentiseringsuppgifterna i den här proceduren. Om VMware-servern och Azure Backup Server inte finns i samma domän anger du domänen i användarnamnet.

    ![Dialogrutan Lägg till autentiseringsuppgifter för Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Klicka på **Lägg** till om du vill lägga till den nya autentiseringsförfattningen.

    ![Dialogrutan Hantera autentiseringsuppgifter för Azure Backup Server](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Lägg till vCenter-servern

Lägg till vCenter Server i Azure Backup Server.

1. Klicka på **Hantera** > **produktionsservrar** > **i**Azure Backup Server.

    ![Guiden Öppna tilläggsguiden för produktionsserver](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Välj Sidan**Produktionsservertyp** i **additionsguiden** > För produktionsserver väljer du **VMware-servrar**och klickar sedan på **Nästa**.

    ![Tilläggsguiden för produktionsserver](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. I **Välj**  **datorservernamn/IP-adress**anger du VMware-serverns FQDN eller IP-adress. Om alla ESXi-servrar hanteras av samma vCenter anger du vCenter-namnet. Annars lägger du till ESXi-värden.

    ![Ange VMware-server](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. I **SSL-port**anger du porten som används för att kommunicera med VMware-servern. 443 är standardporten, men du kan ändra den om din VMware-server lyssnar på en annan port.

5. I **Ange autentiseringsuppgifter**markerar du den autentiseringsuppgifter som du skapade tidigare.

    ![Ange autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Klicka på **Lägg till** om du vill lägga till VMware-servern i serverlistan. Klicka sedan på **Nästa**.

    ![Lägg till VMWare-server och autentiseringsuppgifter](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Klicka på Lägg **till** på sidan **Sammanfattning.** Den nya servern läggs till omedelbart, ingen agent behövs på VMware-servern.

    ![Lägga till VMware-server i Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verifiera inställningarna på sidan **Slutför.**

   ![Sidan Avsluta](./media/backup-azure-backup-server-vmware/summary-screen.png)

Om du har flera ESXi-värdar som inte hanteras av vCenter-servern, eller om du har flera instanser av vCenter Server, måste du köra guiden igen för att lägga till servrarna.

## <a name="configure-a-protection-group"></a>Konfigurera en skyddsgrupp

Lägg till virtuella datorer med VMware för säkerhetskopiering. Skyddsgrupper samlar in flera virtuella datorer och tillämpar samma inställningar för datalagring och säkerhetskopiering på alla virtuella datorer i gruppen.

1. Klicka på **Skydd**i Azure Backup Server-konsolen > **Nytt**.

    ![Öppna guiden Skapa ny skyddsgrupp](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Klicka på **Nästa**på välkomstsidan för guiden Skapa ny **skyddsgrupp** .

    ![Dialogrutan Skapa ny skyddsgrupp](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. På sidan **Välj skydd grupptyp** väljer du **Servrar** och klickar sedan på **Nästa**. Sidan **Välj gruppmedlemmar** visas.

1. I **Välj gruppmedlemmar**väljer du de virtuella datorer (eller VM-mappar) som du vill säkerhetskopiera. Klicka sedan på **Nästa**.

    - När du väljer en mapp eller virtuella datorer eller mappar i den mappen markeras också för säkerhetskopiering. Du kan avmarkera mappar eller virtuella datorer som du inte vill säkerhetskopiera.
1. Om en virtuell dator eller mapp redan säkerhetskopieras kan du inte markera den. Detta säkerställer att dubbla återställningspunkter inte skapas för en virtuell dator.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. På sidan **Välj dataskyddsmetod** anger du ett namn för skyddsgruppen och skyddsinställningar. Om du vill säkerhetskopiera till Azure ställer du in kortsiktigt skydd **på Disk** och aktiverar onlineskydd. Klicka sedan på **Nästa**.

    ![Välj dataskyddsmetod](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. I **Ange kortsiktiga mål**anger du hur länge du vill hålla data säkerhetskopierade till disk.
   - Ange hur många dagar diskåterställningspunkter ska behållas i **kvarhållningsintervall.**
   - I **Synkroniseringsfrekvens**anger du hur ofta diskåterställningspunkter tas.
       - Om du inte vill ange ett säkerhetskopieringsintervall kan du kontrollera **Precis innan en återställningspunkt** så att en säkerhetskopia körs precis innan varje återställningspunkt schemaläggs.
       - Kortsiktiga säkerhetskopior är fullständiga säkerhetskopior och inte inkrementella.
       - Klicka på **Ändra** om du vill ändra tider/datum när kortsiktiga säkerhetskopior sker.

         ![Ange kortsiktiga mål](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. Granska **diskutrymmet i Granska diskallokering.** för de virtuella datorerna.

   - De rekommenderade diskallokeringarna baseras på det kvarhållningsintervall som du har angett, typen av arbetsbelastning och storleken på skyddade data. Gör de ändringar som krävs och klicka sedan på **Nästa**.
   - **Datastorlek:** Datastorlek i skyddsgruppen.
   - **Diskutrymme:** Den rekommenderade mängden diskutrymme för skyddsgruppen. Om du vill ändra den här inställningen bör du allokera det totala utrymmet som är något större än det belopp som du uppskattar varje datakälla växer.
   - **Colocate data:** Om du aktiverar samlokalisering kan flera datakällor i skyddet mappas till en enda replik- och återställningspunktvolym. Samlokalisering stöds inte för alla arbetsbelastningar.
   - **Växer automatiskt:** Om du aktiverar den här inställningen försöker Azure Backup Server öka diskstorleken med 25 procent om data i den skyddade gruppen ger efter för den ursprungliga allokeringen.
   - **Information om lagringspool:** Visar lagringspoolens status, inklusive total och återstående diskstorlek.

    ![Granska diskallokering](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Ange hur du vill ta den första säkerhetskopian i Välj **metod för replikgenerering** och klicka sedan på **Nästa**.
   - Standardvärdet är **automatiskt över nätverket** och **nu**.
   - Om du använder standardinställningen rekommenderar vi att du anger en lågtrafiktid. Välj **Senare** och ange en dag och tid.
   - För stora mängder data eller mindre än optimala nätverksvillkor bör du överväga att replikera data offline med hjälp av flyttbara media.

    ![Välj metod för att skapa repliker](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. I **Alternativ för konsekvenskontroll**väljer du hur och när konsekvenskontrollerna ska automatiseras. Klicka sedan på **Nästa**.
      - Du kan köra konsekvenskontroller när replikdata blir inkonsekventa eller enligt ett anlagd schema.
      - Om du inte vill konfigurera automatiska konsekvenskontroller kan du köra en manuell kontroll. Det gör du genom att högerklicka på skyddsgruppen > **utföra konsekvenskontroll**.

1. På sidan Ange data för **onlineskydd** väljer du de virtuella datorer eller vm-mappar som du vill säkerhetskopiera. Du kan välja medlemmarna individuellt eller klicka på **Markera alla** för att välja alla medlemmar. Klicka sedan på **Nästa**.

    ![Ange onlineskyddsdata](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. På sidan **Ange schema för onlinesäkerhetsuppställning** anger du hur ofta du vill säkerhetskopiera data från lokal lagring till Azure.

    - Molnåterställningspunkter för data genereras enligt schemat. Klicka sedan på **Nästa**.
    - När återställningspunkten har genererats överförs den till Recovery Services-valvet i Azure.

    ![Ange onlineschema för säkerhetskopiering](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. På sidan **Ange onlinekvarhållningsprincip** anger du hur länge du vill behålla återställningspunkterna som skapas från dagliga/veckovisa/månatliga/årliga säkerhetskopior till Azure. klicka sedan på **Nästa**.

    - Det finns ingen tidsgräns för hur länge du kan behålla data i Azure.
    - Den enda gränsen är att du inte kan ha mer än 9999 återställningspunkter per skyddad instans. I det här exemplet är den skyddade instansen VMware-servern.

    ![Ange bevarandeprincip online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Granska inställningarna på sidan **Sammanfattning** och klicka sedan på **Skapa grupp**.

    ![Medlem i skyddsgrupp och inställningssammanfattning](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7 VMWare vSphere 6.7 VMWare vSphere 6.7 VMWare

Så här säkerhetskopierar du vSphere 6.7:

- Aktivera TLS 1.2 på DPM-server

>[!NOTE]
>VMWare 6.7 och framåt hade TLS aktiverat som kommunikationsprotokoll.

- Ange registernycklarna på följande sätt:

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

Om du vill felsöka problem när du konfigurerar säkerhetskopior läser du [felsökningsguiden för Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
