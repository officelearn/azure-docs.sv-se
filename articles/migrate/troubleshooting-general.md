---
title: Felsöka Azure Migrate problem | Microsoft Docs
description: Innehåller en översikt över kända problem i Azure Migrates tjänsten, samt fel söknings tips för vanliga fel.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 16e0fe51137ce7e96cf98c0a8acf7c490e9087b3
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232421"
---
# <a name="troubleshoot-azure-migrate"></a>Felsöka Azure Migrate

[Azure Migrate](migrate-services-overview.md) innehåller en uppsättning verktyg för utvärdering och migrering samt oberoende program varu leverantörer (ISV) för tredje part. Den här artikeln hjälper dig att felsöka problem med Azure Migrate, Azure Migrate Server utvärdering och Azure Migrate Server-migrering.


## <a name="find-a-project"></a>Hitta ett projekt

Det finns [två versioner](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) av Azure Migrate.


Om du har skapat Azure Migrate-projektet i den aktuella versionen av Azure Migrate gör du följande:

1. Sök efter **Azure Migrate**i [Azure Portal](https://portal.azure.com).
2. I Azure Migrate instrument panelens >- **servrar**väljer du **ändra** i det övre högra hörnet.

    ![Växla till ett befintligt Azure Migrate-projekt](./media/troubleshooting-general/switch-project.png)

3. Välj lämplig prenumeration och Azure Migrate projekt.


Om du har skapat projektet i den tidigare versionen av Azure Migrate gör du följande:

1. Sök efter **Azure Migrate**i [Azure Portal](https://portal.azure.com).
2. Om du har skapat ett projekt i den tidigare versionen av Azure Migrate instrument panelen visas en banderoll som refererar till äldre projekt. Välj banderollen.

    ![Komma åt befintliga projekt](./media/troubleshooting-general/access-existing-projects.png)

3. Granska listan över gammalt projekt.


## <a name="create-additional-projects"></a>Skapa ytterligare projekt

Skapa ett nytt Azure Migrate projekt enligt följande:

1. Sök efter **Azure Migrate**i [Azure Portal](https://portal.azure.com).
2. På Azure Migrate instrument panelens >- **servrar**väljer du **ändra** i det övre högra hörnet.

   ![Ändra Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

3. Om du vill skapa ett nytt projekt väljer du **Klicka här**.

   ![Skapa ett andra Azure Migrate-projekt](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Granska stödda geografiska områden

Granska stödda geografiska områden för [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) och [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="delete-projectsworkspaces"></a>Ta bort projekt/arbets ytor

Observera följande när du tar bort Azure Migrate projekt och Log Analytics arbets ytor:

- När du tar bort ett Azure Migrate-projekt raderas projektet *och* metadata om identifierade datorer.
- Om du har kopplat en Log Analytics arbets yta till Server Assessment-verktyget tas arbets ytan bort automatiskt.
- Samma Log Analytics arbets yta kan användas för flera scenarier.
- Om du vill ta bort arbets ytan Log Analytics måste du göra det manuellt.


### <a name="delete-a-project"></a>Ta bort ett projekt

Ta bort ett projekt i den aktuella versionen av Azure Migrate:

1. Öppna Azure-resurs gruppen där projektet skapades.
2. På sidan resurs grupp väljer du **Visa dolda typer**.
3. Välj det migrerade projektet som du vill ta bort. Resurs typen är Microsoft. Migrate/migrateprojects och tar bort den.

Ta bort ett projekt i den äldre versionen av Azure Migrate: 

1. Öppna Azure-resurs gruppen där projektet skapades.
2. Välj det migrerade projektet som du vill ta bort. Resurs typen är migreringsjobb och tar bort den.


### <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Bläddra till Log Analytics arbets ytan som är kopplad till projektet.
* Om du inte har tagit bort det Azure Migrate projektet kan du hitta länken till arbets ytan i **Essentials** > **Server Assessment**.
       ![LA-arbetsyta](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it. 


## <a name="error-requests-must-contain-user-identity-headers"></a>Fel "begär Anden måste innehålla användar identitets rubriker"

När du skapar ett projekt kan det här felet tyda på att du inte har åtkomst till den Azure Active Directory (Azure AD)-innehavaren av organisationen.

- När du har lagt till en Azure AD-klient för första gången får du en e-postinbjudan om att ansluta till klienten.
- Acceptera inbjudan för att lägga till klienten.
    - Om du inte kan se e-postmeddelandet kan du kontakta en användare med åtkomst till klienten och be dem att [Skicka inbjudan](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) till dig igen.
    - När du har tagit emot e-postinbjudan öppnar du den och väljer länken för att acceptera inbjudan. Logga sedan ut från Azure Portal och logga in igen. (att uppdatera webbläsaren fungerar inte.) Sedan kan du börja skapa migreringsjobbet.


## <a name="error-invalid-ovf-manifest-entry"></a>Fel "Ogiltig OVF-manifest post"

Om du får felet "den angivna manifest filen är ogiltig: Ogiltig OVF-manifest post, gör följande:

1. Kontrol lera att filen med ägg Azure Migrates apparaten laddas ned korrekt genom att kontrol lera dess hash-värde. [Läs mer](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Om hash-värdet inte matchar laddar du ned den och försöker distribuera igen.
2. Om distributionen fortfarande Miss lyckas och du använder VMware vSphere-klienten för att distribuera OVF-filen kan du prova att distribuera den via vSphere-webbklienten. Om distributionen fortfarande Miss lyckas kan du prova att använda en annan webbläsare.
3. Om du använder vSphere-webbklienten och försöker distribuera den på vCenter Server 6,5 eller 6,7 försöker du att distribuera de ägg som finns direkt på ESXi-värden:
   - Anslut till ESXi-värden direkt (i stället för vCenter Server) med webb klienten (https://<*värdens IP-adress*>/UI).
   - I **hem** > **inventering**väljer du mall för **fil** > **distribution OVF**. Bläddra till de ägg och slutför distributionen.
4. Kontakta Azure Migrate support om distributionen fortfarande Miss lyckas.

## <a name="appliance-cant-connect-to-the-internet"></a>Enheten kan inte ansluta till Internet

Detta kan inträffa om installations datorn finns bakom en proxyserver.

- Se till att du anger autentiseringsuppgifter för auktorisering om proxyn behöver dem.
- Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutningar lägger du till dessa URL: er i en lista över tillåtna:

    - [URL: er för VMware-utvärdering](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [URL: er för Hyper-V-utvärdering](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [URL: er för migrering av VMware-agent](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [URL: er för VMware agent-baserad migrering](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [URL: er för Hyper-V-migrering](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Om du använder en avlyssnings-proxy för att ansluta till Internet importerar du det till den virtuella datorns dator med [de här stegen](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="errordatetime-synchronization"></a>Fel: datum/tid-synkronisering

Ett fel vid synkronisering av datum och tid (802) anger att Server klockan kanske inte är synkroniserad med den aktuella tiden med mer än fem minuter. Ändra klockan på den insamlade datorn så att den matchar den aktuella tiden:

1. Öppna en administratörs kommando tolk på den virtuella datorn.
2. Kör **w32tm/TZ**för att kontrol lera tids zonen.
3. Kör **w32tm/resync**för att synkronisera tiden.


## <a name="error-unabletoconnecttoserver"></a>Fel: UnableToConnectToServer

Om du får det här anslutnings felet kanske du inte kan ansluta till vCenter Server *servername*. com: 9443. Fel informationen indikerar att det inte finns någon slut punkt som lyssnar på https://*servername*. com: 9443/SDK som kan godkänna meddelandet.

- Kontrol lera om du kör den senaste versionen av installationen. Om du inte gör det uppgraderar du installationen till den [senaste versionen](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Om problemet fortfarande uppstår i den senaste versionen kanske installationen inte kan lösa det angivna vCenter Server namnet, eller så kan den angivna porten vara fel. Om porten inte anges försöker insamlaren som standard att ansluta till port nummer 443.

    1. Pinga *servername*. com från enheten.
    2. Om steg 1 Miss lyckas försöker du ansluta till vCenter-servern med hjälp av IP-adressen.
    3. Identifiera rätt port nummer för att ansluta till vCenter Server.
    4. Kontrol lera att vCenter Server är igång.


## <a name="error-appliance-might-not-be-registered"></a>Fel: Installationen kanske inte är registrerad

- Fel 60052, "installationen kanske inte har registrerats till Azure Migrate projektet" inträffar om det Azure-konto som används för att registrera installationen har otillräcklig behörighet. 
    - Se till att Azure-användarkontot som används för att registrera installationen har minst deltagar behörighet för prenumerationen.
    - [Lär dig mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) om de Azure-roller och behörigheter som krävs.
- Fel 60039, "installationen kanske inte kan registreras Azure Migrate projektet" kan inträffa om registreringen Miss lyckas eftersom det Azure Migrate-projekt som används för att registrera installationen inte kan hittas.
    - I Azure Portal och kontrol lera om projektet finns i resurs gruppen.
    - Om projektet inte finns skapar du ett nytt Azure Migrate-projekt i resurs gruppen och registrerar enheten igen. [Lär dig hur du](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) skapar ett nytt projekt.

## <a name="error-key-vault-management-operation-failed"></a>Fel: Key Vault hanterings åtgärden misslyckades

Om du får felet 60030 eller 60031 kan du göra följande för att utföra en Azure Key Vault hanterings åtgärd: 
- Kontrol lera att Azure-användarkontot som används för att registrera installationen har minst deltagar behörighet för prenumerationen.
- Kontrol lera att kontot har åtkomst till nyckel valvet som anges i fel meddelandet och försök sedan igen.
- Kontakta Microsoft-supporten om problemet kvarstår.
- [Lär dig mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) om de Azure-roller och behörigheter som krävs.

## <a name="fix-discovery-couldnt-be-initiated"></a>Löser Det gick inte att initiera identifiering

Fel 60028: "Det gick inte att initiera identifieringen på grund av ett fel. Åtgärden kunde inte utföras för den angivna listan över värdar eller kluster "anger att identifiering inte kunde startas på värdarna som anges i felet på grund av ett problem med att komma åt eller hämta information om virtuella datorer. Resten av värdarna har lagts till.

- Lägg till värdarna i listan i fel igen med alternativet **Lägg till värd** .
- Om det finns ett verifierings fel kan du läsa igenom reparations vägledningen för att åtgärda felen och sedan försöka **Spara och starta identifierings** alternativet igen.

## <a name="fix-azure-ad-operation-failed-60025"></a>Löser Azure AD-åtgärden misslyckades (60025)

Fel 60025: "En Azure AD-åtgärd misslyckades. Felet inträffade när Azure AD-programmet skapades eller uppdaterades när Azure-användarkontot som används för att initiera identifieringen skiljer sig från det konto som används för att registrera installationen. Gör något av följande:

- Se till att det användar konto som initierar identifieringen är samma som det som används för att registrera enheten.
- Ange åtkomst behörighet för Azure Active Directory program till det användar konto som identifierings åtgärden misslyckades för.
- Ta bort resurs gruppen som skapades tidigare för det Azure Migrate projektet. Skapa en annan resurs grupp för att starta igen.
- [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) om Azure Active Directory program behörigheter.


## <a name="discovered-vms-not-in-portal"></a>Identifierade virtuella datorer som inte är i portalen

Observera följande om du startar identifieringen så att **Server utvärderingen** och **Server migrering** visar att **identifiering pågår**, men ännu inte ser de virtuella datorerna i portalen: 

- Efter att ha påbörjat identifiering från installationen tar det cirka 15 minuter för en identifiering av VMware VM och runt två minuter för varje tillagd värd för identifiering av virtuella Hyper-V-datorer.
- Om du fortsätter att se **identifiering pågår** även efter dessa vänte perioder väljer du **Uppdatera** på fliken **servrar** . Detta ska visa antalet identifierade servrar i **Server utvärderingen** och **Server migrering**.


## <a name="deleted-vms-in-the-portal"></a>Borttagna virtuella datorer i portalen

Tänk på följande om du har distribuerat en installation som kontinuerligt identifierar din lokala miljö, men borttagna virtuella datorer fortfarande visas i portalen:  

- Det tar upp till 30 minuter innan identifierings data som samlas in av produkten visas i portalen.
- Om du inte ser uppdaterad information efter 30 minuter uppdaterar du data genom att följa dessa steg:

    1. I **servrar** > **Azure Migrate Server utvärdering**väljer du **Översikt**.
    2. Under **Hantera**väljer du **agenthälsa**
    3. Välj **Uppdatera agent**. 
    1. Vänta tills uppdaterings åtgärden har slutförts. Nu bör du se uppdaterad information.

## <a name="vm-information-isnt-in-the-portal"></a>VM-informationen finns inte i portalen

- Det tar upp till 30 minuter innan identifierings data som samlas in av produkten visas i portalen.
- Om du inte ser uppdaterad information efter 30 minuter uppdaterar du data genom att följa dessa steg:

    1. I **servrar** > **Azure Migrate Server utvärdering**väljer du **Översikt**.
    2. Under **Hantera**väljer du **agenthälsa**
    3. Välj **Uppdatera agent**. 
    1. Vänta tills uppdaterings åtgärden har slutförts. Nu bör du se uppdaterad information.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Löser Det går inte att ansluta till värd eller kluster

Fel 50004: "Det går inte att ansluta till en värd eller ett kluster eftersom Server namnet inte kan matchas. WinRM-felkod: 0x803381B9 "kan inträffa om Azure DNS tjänsten för installationen inte kan matcha klustret eller värd namnet som du har angett.

- Om du ser det här felet i klustret, FQDN.
- Du kan också se det här felet för värdar i ett kluster. Detta anger att enheten kan ansluta till klustret, men klustret returnerar värdnamn som inte är FQDN. Lös problemet genom att uppdatera hosts-filen på-enheten genom att lägga till en mappning av IP-adress och värdnamn:
    1. Öppna anteckningar som administratör.
    2. Öppna filen C:\Windows\System32\Drivers\etc\hosts.
    3. Lägg till IP-adressen och värd namnet i en rad. Upprepa detta för varje värd eller kluster där du ser det här felet.
    4. Spara och Stäng värdfilen.
    5. Kontrol lera om enheten kan ansluta till värdarna med appen för hantering av appar. Efter 30 minuter bör du se den senaste informationen för de här värdarna i Azure Portal.



## <a name="fix-assessment-readiness"></a>Korrigera utvärderings beredskap

Åtgärda problem med utvärderings beredskap enligt följande:

**Ge** | **Fix**
--- | ---
Starttypen stöds inte | Azure har inte stöd för virtuella datorer med en EFI-starttyp. Vi rekommenderar att du konverterar start typen till BIOS innan du kör en migrering. <br/><br/>Du kan använda migrering av Azure Migrate Server för att hantera migrering av sådana virtuella datorer. Den kommer att konvertera start typen för den virtuella datorn till BIOS under migreringen.
Villkorligt Windows-operativsystem som stöds | Operativ systemet har passerat sitt slutdatum och måste ha ett anpassat support avtal (CSA) för [support i Azure](https://aka.ms/WSosstatement). Överväg att uppgradera innan du migrerar till Azure.
Windows-operativsystem som inte stöds | Azure stöder endast [valda Windows OS-versioner](https://aka.ms/WSosstatement). Överväg att uppgradera datorn innan du migrerar till Azure.
Villkorligt godkänt Linux OS | Azure har endast godkänt [valda Linux OS-versioner](../virtual-machines/linux/endorsed-distros.md). Överväg att uppgradera datorn innan du migrerar till Azure.
Linux-OS som inte stöds | Datorn kan starta i Azure, men Azure tillhandahåller inget stöd för operativ system. Överväg att uppgradera till en [godkänd Linux-version](../virtual-machines/linux/endorsed-distros.md) innan du migrerar till Azure.
Okänt operativ system | Operativ systemet för den virtuella datorn angavs som "Övrigt" i vCenter Server. Det här beteendet blockerar Azure Migrate från att verifiera den virtuella datorns Azure-beredskap. Kontrol lera att operativ systemet [stöds](https://aka.ms/azureoslist) av Azure innan du migrerar datorn.
Bit versionen stöds inte | Virtuella datorer med ett 32-bitars operativ system kan starta i Azure, men vi rekommenderar att du uppgraderar till 64-bit innan du migrerar till Azure.
Kräver en Microsoft Visual Studio-prenumeration | Datorn kör ett Windows-klient operativ system som bara stöds via en Visual Studio-prenumeration.
Den virtuella datorn hittades inte för lagrings prestanda som krävs | Lagrings prestandan (indata/utdata per sekund [IOPS] och data flöde) som krävs för datorn överskrider stöd för virtuella Azure-datorer. Minska lagrings kraven för datorn innan du migrerar.
Det gick inte att hitta den virtuella datorn för den nödvändiga nätverks prestandan | Nätverks prestandan (in/ut) som krävs för datorn överskrider stöd för virtuella Azure-datorer. Minska nätverks kraven för datorn.
Den virtuella datorn hittades inte på den angivna platsen | Använd en annan målplats innan migreringen.
En eller flera olämpliga diskar | En eller flera diskar som är anslutna till den virtuella datorn uppfyller inte kraven för Azure. En<br/><br/> Azure Migrate: Server utvärderingen stöder för närvarande inte Ultra SSD diskar och utvärderar diskarna baserat på disk gränserna för Premium Managed disks (32 TB).<br/><br/> För varje disk som är ansluten till den virtuella datorn ser du till att storleken på disken är < 64 TB (stöds av Ultra SSD diskar).<br/><br/> Om den inte är det kan du minska disk storleken innan du migrerar till Azure, eller använda flera diskar i Azure och [Stripa dem](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) för att få högre lagrings gränser. Kontrol lera att prestandan (IOPS och data flödet) som krävs för varje disk stöds av Azure- [hanterade virtuella dator diskar](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).
Ett eller flera olämpliga nätverkskort. | Ta bort oanvända nätverkskort från datorn innan migreringen.
Antalet diskar överskrider gränsen | Ta bort oanvända diskar från datorn innan migreringen.
Diskstorleken överskrider gränsen | Azure Migrate: Server utvärderingen stöder för närvarande inte Ultra SSD diskar och utvärderar diskarna baserat på Premium disk gränser (32 TB).<br/><br/> Azure har dock stöd för diskar med upp till 64 TB-storlek (stöds av Ultra SSD diskar). Minska diskar till mindre än 64 TB innan migrering, eller Använd flera diskar i Azure och dela upp [dem](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) för att få högre lagrings gränser.
Disken är inte tillgänglig på den angivna platsen | Kontrol lera att disken finns på mål platsen innan du migrerar.
Disken är inte tillgänglig för den angivna redundansen | Disken bör använda den redundans lagrings typ som definierats i utvärderings inställningarna (LRS som standard).
Det gick inte att fastställa diskens lämplighet på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att hitta den virtuella datorn med nödvändiga kärnor och minne | Azure kunde inte hitta en lämplig VM-typ. Minska minnet och antalet kärnor för den lokala datorn innan du migrerar.
Det gick inte att fastställa VM-lämplighet på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för en eller flera diskar på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för ett eller flera nätverkskort på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.

## <a name="cant-add-enterprise-agreement-ea-in-an-assessment"></a>Det går inte att lägga till Enterprise-avtal (EA) i en utvärdering

Azure Migrate Server-utvärderingen stöder för närvarande inte priser för Enterprise-avtal (EA). Undvik den här begränsningen genom att använda **betala per** användning som Azure-erbjudande och Använd egenskapen **rabatt** för att ange en anpassad rabatt som du får. [Lär dig](https://aka.ms/migrate/selfhelp/eapricing) hur du anpassar en utvärdering.

## <a name="linux-vms-are-conditionally-ready"></a>Virtuella Linux-datorer är "villkorligt redo"

Server utvärderingen markerar virtuella Linux-datorer som "villkorligt redo" på grund av ett känt avstånd i Server utvärderingen.

- Luckan förhindrar att den lägre versionen av Linux OS som är installerad på lokala virtuella datorer identifieras.
- För RHEL 6,10 identifieras till exempel bara RHEL 6 som operativ system version för för närvarande.
-  Eftersom Azure bara godkänner vissa versioner av Linux är de virtuella Linux-datorerna för närvarande markerade som villkorligt klara i Server utvärderingen.
- Du kan avgöra om Linux-operativsystemet som körs på den lokala virtuella datorn har godkänts i Azure genom att granska [Azure Linux](https://aka.ms/migrate/selfhost/azureendorseddistros)-supporten.
-  När du har verifierat den godkända distributionen kan du ignorera den här varningen.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Azure-SKU: er överskrider lokal storleks ändring

Azure Migrate Server utvärderingen kan rekommendera Azure VM SKU: er med fler kärnor och minne än den aktuella lokala allokeringen baserat på typen av utvärdering:


- Den virtuella datorns SKU-rekommendation beror på utvärderings egenskaperna.
- Detta påverkas av den typ av utvärdering som du utför i Server utvärderingen: *Prestanda-baserade*eller *lokalt*. 
- För prestandabaserade utvärderingar beaktar Server utvärderingen användnings data för de lokala virtuella datorerna (CPU, minne, disk och nätverks användning) för att fastställa rätt mål-SKU för virtuella datorer för dina lokala virtuella datorer. Den lägger också till en bekvämlighets faktor när du fastställer effektiv användning.
- För lokal storleks sortering beaktas inte prestanda data och SKU: n rekommenderas för lokal allokering.

För att visa hur detta kan påverka rekommendationerna, tar vi ett exempel:

Vi har en lokal virtuell dator med fyra kärnor och åtta GB minne, med 50% processor användning och 50% minnes användning och en angiven bekvämlighets faktor på 1,3.

-  Om utvärderingen är **lokalt**, rekommenderas en Azure VM-SKU med 4 kärnor och 8 GB minne.
- Om utvärderingen är Prestandaoptimerad, baserat på effektiv processor-och minnes användning (50% av 4 kärnor * 1,3 = 2,6 kärnor och 50% av 8 GB minne * 1,3 = 5,3-GB minne), billigaste VM-SKU: n för fyra kärnor (närmaste antal kärnor som stöds) och 8 GB minne (närmaste stöd minnes storlek) rekommenderas. 
- [Läs mer](concepts-assessment-calculation.md#sizing) om utvärderings storlek.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure disk-SKU: er större än lokalt

Azure Migrate Server-utvärderingen kan rekommendera en större disk baserat på typen av utvärdering.
- Disk storlek i Server utvärderingen är beroende av två bedömnings egenskaper: storleks kriterier och lagrings typ.
- Om storleks kriteriet är **prestanda baserat**och lagrings typen är inställd på **Automatisk**, betraktas IOPS-och data flödes värden för disken när mål disk typen identifieras (standard HDD, standard SSD eller Premium). En disk-SKU från disk typen rekommenderas och rekommendationen tar hänsyn till storleks kraven för den lokala disken. 
- Om storleks kriteriet är **prestanda baserat**och lagrings typen är **Premium**rekommenderas en SKU för Premium-diskar i Azure baserat på IOPS, data flöde och storleks krav för den lokala disken. Samma logik används för att utföra disk storlek när storleks kriteriet är **lokalt** och lagrings typen är **standard HDD**, **standard SSD**eller **Premium**.

Om du till exempel har en lokal disk med 32 GB minne, men den aggregerade läsnings-och skriv-IOPS för disken är 800 IOPS, rekommenderar Server utvärderingen en Premium disk (på grund av de högre IOPS-kraven) och rekommenderar sedan en disk-SKU som stöder r ektion IOPS och storlek. Den bästa matchningen i det här exemplet är P15 (256 GB, 1100 IOPS). Även om den storlek som krävs av den lokala disken var 32 GB, rekommenderar Server utvärderingen en större disk på grund av det höga IOPS-kravet för den lokala disken.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Löser Procent andel använda kärnor eller minne som saknas

Server utvärderings rapporter "PercentageOfCoresUtilizedMissing" eller "PercentageOfMemoryUtilizedMissing" när Azure Migrate-enheten inte kan samla in prestanda data för de relevanta lokala virtuella datorerna.

- Detta kan inträffa om de virtuella datorerna är avstängda under utvärderings tiden. Enheten kan inte samla in prestanda data för en virtuell dator när den är avstängd.
- Om endast minnes räknarna saknas och du försöker utvärdera virtuella Hyper-V-datorer, kontrollerar du om du har dynamiskt minne aktiverat på de här virtuella datorerna. Det finns ett känt problem för virtuella Hyper-V-datorer, där en Azure Migrate-apparat inte kan samla in minnes användnings data för virtuella datorer som inte har dynamiskt minne aktiverat.
- Om någon av prestanda räknarna saknas går Azure Migrate Server utvärderingen tillbaka till allokerade kärnor och minne, och det rekommenderar en motsvarande VM-storlek.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>Ingår licens kostnaderna för VM-operativsystem i kostnads utvärderingen?

Azure Migrate Server utvärderingen betraktar för närvarande endast operativ Systems licens kostnaden för Windows-datorer. Licens kostnader för Linux-datorer anses inte för närvarande.

## <a name="performance-history-and-percentile-use"></a>Prestanda historik och percentils användning

Dessa egenskaper gäller endast för prestandabaserade storleks ändringar i Azure Migrate Server-utvärdering.

Server Assessment samlar kontinuerligt in prestandadata för lokala datorer och använder dem för att rekommendera VM-SKU:n och disk-SKU:n i Azure. Prestanda data samlas in av Server utvärdering enligt följande:
- Azure Migrates apparaten registrerar kontinuerligt den lokala miljön för att samla in användnings data i real tid var 20: e sekund för virtuella VMware-datorer och var 30: e sekund för virtuella Hyper-V-datorer.
- Enheten samlar in de 20 eller 30 sekunderna exemplen för att skapa en enda data punkt för var tionde minut. Om du vill skapa en enskild data punkt väljer du det högsta värdet från alla exempel för 20-sekunder och 30 sekunder och skickar det sedan till Azure.
- När du skapar en utvärdering i Server Assessment, baserat på precentilvärdet för prestandavaraktighet och prestandahistorik, identifieras det representativa användningsvärdet. Om prestanda historiken t. ex. är en vecka och percentilen är 95, sorterar Azure Migrate alla exempel punkter på 10 minuter under de senaste 1 veckorna i stigande ordning och väljer sedan 95-percentilen som det representativa värdet.
- Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du väljer 99-percentilen.
- Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden, bör du välja den 99 percentilen för percentils användning.



## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Jag kan inte hitta beroende visualisering för Azure Government

Azure Migrate är beroende av Tjänstkarta för funktionerna för beroende visualisering. Eftersom Tjänstkarta för närvarande inte är tillgängligt i Azure Government är den här funktionen inte tillgänglig i Azure Government.

## <a name="dependencies-dont-show-after-installing-agents"></a>Beroenden visas inte när agenter har installerats


När du har installerat beroende visualiserings agenter på lokala virtuella datorer tar Azure Migrate vanligt vis 15-30 minuter att Visa beroenden i portalen. Om du har väntat i mer än 30 minuter ser du till att Microsoft Monitoring Agent (MMA) kan ansluta till Log Analytics-arbetsytan.

För virtuella Windows-datorer:
1. Starta MMA på kontroll panelen.
2. I **egenskaperna** > för Microsoft Monitoring Agent**Azure Log Analytics (OMS)** kontrollerar du att arbets ytans **status** är grön.
3. Om statusen inte är grön kan du försöka ta bort arbets ytan och lägga till den igen till MMA.

      ![Dialog rutan Egenskaper för MMA](./media/troubleshooting-general/mma-status.png)

För virtuella Linux-datorer måste du kontrol lera att installations kommandona för MMA och beroende agenten lyckades.

## <a name="supported-mma-os"></a>MMA OS som stöds

 Granska de [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)-och [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) -operativsystem som stöds.

## <a name="supported-dependency-agent-os"></a>Operativ system som stöds beroende agent

Granska de [Windows-och Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) -operativsystem som stöds.

## <a name="dependencies-for-more-than-an-hour"></a>Beroenden i mer än en timme

Även om Azure Migrate ger dig möjlighet att gå tillbaka till ett visst datum under den senaste månaden, är den längsta varaktigheten som du kan visualisera beroenden en timme.

Du kan t. ex. använda funktionen tids varaktighet i beroende kartan för att Visa beroenden för igår, men du kan bara visa dem under en timmes period.

Du kan dock använda Azure Monitor loggar för att [fråga beroende data](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre varaktighet.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Det går inte att visualisera beroenden för grupper med fler än 10 virtuella datorer

I Azure Migrate Server utvärdering kan du [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) med upp till 10 virtuella datorer. För större grupper rekommenderar vi att du delar upp de virtuella datorerna i mindre grupper för att visualisera beroenden.

## <a name="machines-show-install-agent-not-view-dependencies"></a>Datorer visar "installera agent" inte "Visa beroenden"

När du har migrerat datorer med beroende visualisering aktive rad till Azure kan datorerna Visa åtgärden "installera agent" i stället för "Visa beroenden" på följande sätt:


- När migreringen till Azure är inaktive rad stängs lokala datorer av och motsvarande virtuella datorer är i Azure. De här datorerna hämtar en annan MAC-adress.
- Datorerna kan också ha en annan IP-adress, baserat på om du har bevarat den lokala IP-adressen eller inte.
- Om både MAC-och IP-adresser skiljer sig från lokala datorer associerar Azure Migrate inte lokala datorer med Tjänstkarta beroende data. I det här fallet visas alternativet för att installera agenten i stället för att Visa beroenden.
- Efter en testmigrering till Azure förblir lokala datorer aktiverade som förväntat. Likvärdiga datorer som är förändrade i Azure får en annan MAC-adress och kan förvärva olika IP-adresser. Om du inte blockerar utgående Azure Monitor logg trafik från dessa datorer, kommer Azure Migrate inte att associera de lokala datorerna med några Tjänstkarta beroende data, och därför visar alternativet att installera agenter i stället för att Visa beroenden.


## <a name="collect-network-traffic-logs-in-portal"></a>Samla in nätverks trafik loggar i portalen

Samla in loggar enligt följande:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Tryck på F12 för att starta Utvecklarverktyg. Om det behövs avmarkerar du **navigerings inställningen rensa poster** .
3. Välj fliken **nätverk** och börja samla in nätverks trafik:
   - I Chrome väljer du **bevara logg**. Inspelningen bör starta automatiskt. En röd cirkel visar att trafiken fångas. Om den röda cirkeln inte visas väljer du den svarta cirkeln för att starta.
   - I Microsoft Edge och Internet Explorer ska inspelningen starta automatiskt. Om den inte är det väljer du den gröna uppspelnings knappen.
4. Försök att återskapa felet.
5. När du har påträffat felet under inspelningen, stoppa inspelningen och spara en kopia av den inspelade aktiviteten:
   - I Chrome högerklickar du på och väljer **Spara som har innehåll**. Den här åtgärden komprimerar och exporterar loggarna som en. har-fil.
   - I Microsoft Edge eller Internet Explorer väljer du alternativet **Exportera insamlad trafik** . Den här åtgärden komprimerar och exporterar loggen.
6. Välj fliken **konsol** för att kontrol lera om det finns varningar eller fel. Så här sparar du konsol loggen:
   - I Chrome högerklickar du på valfri plats i konsol loggen. Välj **Spara som**, för att exportera och zippa loggen.
   - I Microsoft Edge eller Internet Explorer högerklickar du på felen och väljer **Kopiera alla**.
7. Stäng Utvecklarverktyg.
