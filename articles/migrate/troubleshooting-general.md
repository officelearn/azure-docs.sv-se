---
title: Felsöka Azure Migrate problem | Microsoft Docs
description: Innehåller en översikt över kända problem i Azure Migrates tjänsten, samt fel söknings tips för vanliga fel.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 7fea6d16c8846909a8ce9bb33aae74ce343018fa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142326"
---
# <a name="troubleshoot-azure-migrate"></a>Felsöka Azure Migrate

[Azure Migrate](migrate-services-overview.md) innehåller en uppsättning verktyg för utvärdering och migrering samt oberoende program varu leverantörer (ISV) för tredje part. Den här artikeln hjälper dig att felsöka fel med Azure Migrate, Azure Migrate Server utvärdering och Azure Migrate Server-migrering.

## <a name="azure-migrate-project-issues"></a>Azure Migrate projekt problem

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>Jag kan inte hitta mitt befintliga Azure Migrate-projekt.

Det finns [två versioner](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) av Azure Migrate. Använd någon av följande metoder beroende på vilken version du skapade projektet:

* Om du letar efter ett projekt som skapats med den tidigare versionen (tidigare erfarenhet) av Azure Migrate, följer du de här stegen för att hitta projektet:

    1. Gå till [Azure Portal](https://portal.azure.com)och sök efter **Azure Migrate**.
    2. På instrument panelen Azure Migrate visas en banderoll som visar hur du kommer åt äldre projekt. Du ser bara den här texten om du har skapat ett projekt med den gamla upplevelsen. Välj banderollen.

       ![Komma åt befintliga projekt](./media/troubleshooting-general/access-existing-projects.png)

    3. Nu visas en lista över befintliga projekt som har skapats med den tidigare versionen av Azure Migrate.

* Om du letar efter ett projekt som skapats med den aktuella versionen (ny upplevelse) följer du dessa steg för att hitta projektet:

    1. Gå till [Azure Portal](https://portal.azure.com)och sök efter **Azure Migrate**.
    2. På instrument panelen för Azure Migrate går du till sidan **servrar** i den vänstra rutan och väljer **ändra** i det övre högra hörnet.

       ![Växla till ett befintligt Azure Migrate-projekt](./media/troubleshooting-general/switch-project.png)

    3. Välj lämplig prenumeration och Azure Migrate projekt.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>Hur gör jag för att skapa ett andra Azure Migrate-projekt?

Följ dessa steg om du vill skapa ett nytt Azure Migrate-projekt:

1. Gå till [Azure Portal](https://portal.azure.com)och sök efter **Azure Migrate**.
2. På instrument panelen för Azure Migrate går du till sidan **servrar** i den vänstra rutan och väljer **ändra** i det övre högra hörnet.

   ![Ändra Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

3. Om du vill skapa ett nytt projekt väljer du **Klicka här**.

   ![Skapa ett andra Azure Migrate-projekt](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Vilka Azure-geografiska områden Azure Migrate stöd för?

Se listorna för [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) och för [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Hur gör jag för att ta bort Azure Migrate projekt och associerade Log Analytics arbets ytor?

När du tar bort ett Azure Migrate-projekt raderas migreringsjobbet *och* metadata om identifierade datorer. Men om du har kopplat en Log Analytics arbets yta till Server bedömnings verktyget tas inte arbets ytan Log Analytics bort automatiskt. (Samma Log Analytics arbets yta kan användas för flera användnings fall.) Om du också vill ta bort arbets ytan Log Analytics måste du göra det manuellt:

1. Bläddra till Log Analytics arbets ytan som är kopplad till projektet.
     * Om du inte har tagit bort migreringsjobbet ännu kan du hitta länken till arbets ytan från översikts sidan Server utvärdering i avsnittet Essentials.

       ![LA-arbetsyta](./media/troubleshooting-general/loganalytics-workspace.png)

     * Om du redan har tagit bort migreringsjobbet väljer du **resurs grupper** i den vänstra rutan i Azure Portal. Gå till resurs gruppen där arbets ytan skapades och bläddra sedan till den.
2. Följ instruktionerna i [ta bort en Azure Log Analytics-arbetsyta med Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace).

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>Det gick inte att skapa ett migreringsjobb med felet "förfrågningar måste innehålla användar identitets rubriker".

Det här problemet kan inträffa för användare som inte har åtkomst till den Azure Active Directory (Azure AD) av organisationen. När en användare läggs till i en Azure AD-klient för första gången får användaren en e-postinbjudan att ansluta till klienten. Användarna måste godkänna inbjudan för att kunna läggas till i klienten. Om du inte kan se e-postmeddelandet kontaktar du en användare som redan har åtkomst till klienten och ber dem att skicka inbjudan till dig igen med hjälp av stegen som anges i [skicka inbjudningar igen till gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

När e-postinbjudan tas emot måste du öppna e-postmeddelandet och välja länken för att acceptera inbjudan. Sedan måste du logga ut från Azure Portal och logga in igen. (Att uppdatera webbläsaren fungerar inte.) Sedan kan du börja skapa migreringsjobbet.

## <a name="appliance-issues"></a>Problem med installationen

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>Det gick inte att distribuera en Azure Migrate-apparat för VMware med en angiven manifest fil: Ogiltig OVF-manifest post "fel.

1. Kontrol lera att filen med ägg Azure Migrates apparaten laddas ned korrekt genom att kontrol lera dess hash-värde. Vägledning finns i [förbereda virtuella VMware-datorer för utvärdering och migrering till Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Om hash-värdet inte matchar laddar du ned den och försöker distribuera igen.
2. Om distributionen fortfarande Miss lyckas och du använder VMware vSphere-klienten för att distribuera OVF-filen kan du prova att distribuera den via vSphere-webbklienten. Om distributionen fortfarande Miss lyckas kan du prova att använda en annan webbläsare.
3. Om du använder vSphere-webbklienten och försöker distribuera den på vCenter Server 6,5 eller 6,7 kan du försöka distribuera de här enheterna direkt på ESXi-värden genom att följa dessa steg:
   - Anslut till ESXi-värden direkt (i stället för vCenter Server) genom att använda webb klienten (https://<*värdens IP-adress*>/UI).
   - Gå till **Start** > **inventering**.
   - Välj **fil** > **distribuera OVF-mall**. Bläddra till de ägg och slutför distributionen.
4. Kontakta Azure Migrate support om distributionen fortfarande Miss lyckas.

### <a name="the-appliance-cant-connect-to-the-internet"></a>Enheten kan inte ansluta till Internet.

Detta kan inträffa när datorn som du använder ligger bakom en proxyserver. Se till att du anger autentiseringsuppgifter för auktorisering om proxyn behöver dem.
Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutningar, måste du lägga till dessa obligatoriska URL: er i en lista över tillåtna:

- [Server utvärdering för VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Server utvärdering för Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [Server migrering för VMware (utan agent)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [Server-migrering för VMware (agent-baserad)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Server-migrering för Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Om du använder en avlyssnings-proxy för att ansluta till Internet, måste du importera proxy-certifikatet till den virtuella datorns dator. Du kan importera proxyagenten genom att följa stegen i [Azure Migrate](https://docs.microsoft.com/azure/migrate/concepts-collector)-installationen.

### <a name="error-802-date-and-time-synchronization-error"></a>Fel 802: Fel vid synkronisering av datum och tid.

Server klockan kanske inte är synkroniserad med den aktuella tiden med mer än fem minuter. Ändra klockan på den insamlade datorn så att den matchar den aktuella tiden enligt följande:

1. Öppna en administratörs kommando tolk på den virtuella datorn.
2. Kör **w32tm/TZ**för att kontrol lera tids zonen.
3. Kör **w32tm/resync**för att synkronisera tiden.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>Anslutningen misslyckades. Fel: UnableToConnectToServer.

Du kanske inte kan ansluta till vCenter Server *servername*. com: 9443. Fel informationen indikerar att det inte finns någon slut punkt som lyssnar på https://*servername*. com: 9443/SDK som kan godkänna meddelandet.

I den här situationen kontrollerar du om du kör den senaste versionen av insamlings enheten. Om du inte gör det uppgraderar du installationen till den [senaste versionen](https://docs.microsoft.com/azure/migrate/concepts-collector).

Om problemet fortfarande uppstår i den senaste versionen kan insamlings datorn inte matcha det angivna vCenter Server namnet, eller så kan den angivna porten vara fel. Om porten inte anges försöker insamlaren som standard att ansluta till port nummer 443.

1. Pinga *servername*. com från insamlings datorn.
2. Om steg 1 Miss lyckas försöker du ansluta till vCenter-servern med hjälp av IP-adressen.
3. Identifiera rätt portnummer för att ansluta till vCenter.
4. Kontrol lera att vCenter-servern är igång och körs.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Installationen kanske inte har registrerats till Azure Migrate-projektet (fel-ID: 60052).

Det här felet uppstår om det Azure-konto som används för att registrera installationen inte har tillräcklig behörighet. Se till att Azure-användarkontot som används för att registrera installationen har minst deltagar behörighet för prenumerationen. [Lär dig mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) om de Azure-roller och behörigheter som krävs.

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Installationen kanske inte har registrerats till Azure Migrate-projektet (fel-ID: 60039).

Det Azure Migrate projekt som valts för att registrera installationen kanske inte hittas. I så fall Miss lyckas registreringen. Gå till Azure Portal och kontrol lera om projektet finns i resurs gruppen. Om projektet inte finns skapar du ett nytt Azure Migrate-projekt i resurs gruppen och registrerar enheten igen. [Lär dig mer](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) om att skapa ett nytt Azure Migrate-projekt.

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>En Azure Key Vault hanterings åtgärd misslyckades (fel-ID: 60030, 60031).

Kontrol lera att Azure-användarkontot som används för att registrera installationen har minst deltagar behörighet för prenumerationen. Kontrol lera också att kontot har åtkomst till nyckel valvet som anges i fel meddelandet och försök sedan igen. Kontakta Microsoft-supporten om problemet kvarstår. [Lär dig mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) om de Azure-roller och behörigheter som krävs.

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>Det gick inte att initiera identifieringen på grund av ett fel. Åtgärden kunde inte utföras för den angivna listan över värdar eller kluster (fel-ID: 60028).

Det gick inte att starta identifieringen på värdarna som anges i felet på grund av ett problem med att komma åt eller hämta information om virtuella datorer. Resten av värdarna har lagts till. Lägg till de värdar som visas i fel meddelandet igen med hjälp av alternativet **Lägg till värd** . Om det finns ett verifierings fel kan du läsa igenom reparations vägledningen för att åtgärda felen och sedan försöka **Spara och starta identifierings** alternativet igen.

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>En Azure AD-åtgärd misslyckades. Felet uppstod när Azure AD-programmet skapades eller uppdaterades (fel-ID: 60025).

Det här felet inträffar när ett annat Azure-konto används för att starta identifieringen än det konto som användes för att registrera apparaten. Gör något av följande:
1. Se till att det användar konto som initierar identifieringen är samma som det som används för att registrera enheten.
1. Ge AAD-appen åtkomst behörighet till det andra användar konto som identifierings åtgärden misslyckades för.
1. Ta bort resurs gruppen som du skapade tidigare för Azure Migrate projekt och skapa en ny resurs grupp för att starta igen.

[Läs mer](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) om program behörigheter för AAD.

## <a name="discovery-issues"></a>Identifieringsproblem

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Jag startade identifiering men ser inte de identifierade virtuella datorerna på Azure Portal. Panelerna **Server utvärdering** och **Server migrering** visar status "identifiering pågår".
När du har startat identifieringen från installationen kan du vänta lite tid innan de identifierade datorerna visas på Azure Portal. Det tar ungefär 15 minuter för en VMware-identifiering och cirka 2 minuter per tillagd värd för Hyper-V-identifiering. Om du fortsätter att se "identifiering pågår" även efter dessa vänte perioder väljer du **Uppdatera** på fliken **servrar** . Detta ska visa antalet identifierade servrar i panelerna Server bedömning och Server migrering.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>Jag använder en installation som kontinuerligt identifierar min lokala miljö, men de virtuella datorerna som tas bort i min lokala miljö visas fortfarande i portalen.

Det tar upp till 30 minuter innan identifierings data som samlas in av produkten visas i portalen. Om du inte ser uppdaterad information ens efter 30 minuter uppdaterar du data genom att följa dessa steg:

1. Gå till **servrar** > **Azure Migrate Server utvärdering**och välj **Översikt**.
2. Under **Hantera**väljer du **agenthälsa**
3. Välj **Uppdatera agent**. Du ser det här alternativet under listan över agenter.
4. Vänta tills uppdaterings åtgärden har slutförts. Nu bör du se aktuell information om dina virtuella datorer.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Jag ser inte den senaste informationen om de lokala virtuella datorerna på portalen.

Det tar upp till 30 minuter innan identifierings data som samlas in av produkten visas på portalen. Om du inte ser uppdaterad information ens efter 30 minuter uppdaterar du data genom att följa dessa steg:

1. Gå till **servrar** > **Azure Migrate Server utvärdering**och välj **Översikt**.
2. Under **Hantera**väljer du **agenthälsa**
3. Välj **Uppdatera agent**. Du ser det här alternativet under listan över agenter.
4. Vänta tills uppdaterings åtgärden har slutförts. Nu bör du se aktuell information om dina virtuella datorer.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Det går inte att ansluta till en värd eller ett kluster eftersom Server namnet inte kan matchas. WinRM-felkod: 0x803381B9 (fel-ID: 50004).
Det här felet uppstår om Azure DNS tjänsten för installationen inte kan matcha klustret eller värd namnet som du har angett. Om du ser det här felet i klustret kan du försöka med att ange det fullständiga domän namnet (FQDN) för klustret.

Du kan också se det här felet för värdar i ett kluster. I det här fallet kan enheten ansluta till klustret, men klustret returnerar värdnamn som inte är FQDN.

Lös problemet genom att uppdatera hosts-filen på-enheten genom att lägga till en mappning av IP-adress och värdnamn:
1. Öppna anteckningar som administratör. Öppna sedan filen C:\Windows\System32\Drivers\etc\hosts.
2. Lägg till IP-adressen och värd namnet i en rad. Upprepa detta för varje värd eller kluster där du ser det här felet.
3. Spara och Stäng värdfilen.
4. Du kan kontrol lera om enheten kan ansluta till värdarna med hjälp av appen för hantering av appar. Efter 30 minuter bör du kunna se den senaste informationen för de här värdarna på Azure Portal.


## <a name="assessment-issues"></a>Utvärderingsproblem

### <a name="azure-readiness-issues"></a>Problem med Azure-beredskap

Problem | Åtgärd
--- | ---
Starttypen stöds inte | Azure har inte stöd för virtuella datorer med en EFI-starttyp. Vi rekommenderar att du konverterar start typen till BIOS innan du kör en migrering. <br/><br/>Du kan använda migrering av Azure Migrate Server för att hantera migrering av sådana virtuella datorer. Den kommer att konvertera start typen för den virtuella datorn till BIOS under migreringen.
Villkorligt stöd för Windows OS | Operativ systemets slutdatum har passerat och det krävs ett anpassat support avtal (CSA) för [support i Azure](https://aka.ms/WSosstatement). Överväg att uppgradera operativ systemet innan du migrerar till Azure.
Windows-operativsystem som inte stöds | Azure stöder endast [valda Windows OS-versioner](https://aka.ms/WSosstatement). Överväg att uppgradera datorns operativ system innan du migrerar till Azure.
Villkorligt godkänt Linux OS | Azure har endast godkänt [valda Linux OS-versioner](../virtual-machines/linux/endorsed-distros.md). Överväg att uppgradera datorns operativ system innan du migrerar till Azure.
Linux-OS som inte stöds | Datorn kan starta i Azure, men Azure ger inget operativ system stöd. Överväg att uppgradera operativ systemet till en [godkänd Linux-version](../virtual-machines/linux/endorsed-distros.md) innan du migrerar till Azure.
Okänt operativ system | Operativ systemet för den virtuella datorn angavs som "Övrigt" i vCenter Server. Det här beteendet blockerar Azure Migrate från att verifiera den virtuella datorns Azure-beredskap. Kontrol lera att datorns operativ system [stöds](https://aka.ms/azureoslist) av Azure innan du migrerar datorn.
OS-bitar som inte stöds | Virtuella datorer med ett 32-bitars operativ system kan starta i Azure, men vi rekommenderar att du uppgraderar operativ systemet på den virtuella datorn till 64-bitars innan du migrerar till Azure.
Kräver en Microsoft Visual Studio-prenumeration | Datorn kör ett Windows-klient-OS som bara stöds via en Visual Studio-prenumeration.
Den virtuella datorn hittades inte för lagrings prestanda som krävs | Lagrings prestandan (indata/utdata per sekund [IOPS] och data flöde) som krävs för datorn överskrider stöd för virtuella Azure-datorer. Minska lagrings kraven för datorn innan du migrerar.
Det gick inte att hitta den virtuella datorn för den nödvändiga nätverks prestandan | Nätverks prestandan (in/ut) som krävs för datorn överskrider stöd för virtuella Azure-datorer. Minska nätverks kraven för datorn.
Den virtuella datorn hittades inte på den angivna platsen | Använd en annan målplats innan migreringen.
En eller flera olämpliga diskar | En eller flera diskar som är anslutna till den virtuella datorn uppfyller inte kraven för Azure. Azure Migrate: Server utvärderingen stöder för närvarande inte Ultra SSD diskar och utvärderar diskarna baserat på disk gränserna för Premium Managed disks (32 TB).  För varje disk som är ansluten till den virtuella datorn ser du till att storleken på disken är < 64 TB (stöds av Ultra SSD diskar), om inte, minskar du disk storleken innan du migrerar till Azure eller använder flera diskar i Azure och tar bort [dem tillsammans](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) för att få högre lagrings gränser. Kontrol lera att prestandan (IOPS och data flödet) som krävs för varje disk stöds av Azure- [hanterade virtuella dator diskar](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).
Ett eller flera olämpliga nätverkskort. | Ta bort oanvända nätverkskort från datorn innan migreringen.
Antalet diskar överskrider gränsen | Ta bort oanvända diskar från datorn innan migreringen.
Diskstorleken överskrider gränsen | Azure Migrate: Server utvärderingen stöder för närvarande inte Ultra SSD diskar och utvärderar diskarna baserat på Premium disk gränser (32 TB). Azure har dock stöd för diskar med upp till 64 TB-storlek (stöds av Ultra SSD diskar). Minska diskar till mindre än 64 TB innan migrering eller Använd flera diskar i Azure och dela upp [dem](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) för att få högre lagrings gränser.
Disken är inte tillgänglig på den angivna platsen | Kontrol lera att disken finns på mål platsen innan du migrerar.
Disken är inte tillgänglig för den angivna redundansen | Disken bör använda den redundans lagrings typ som definierats i utvärderings inställningarna (LRS som standard).
Det gick inte att fastställa diskens lämplighet på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att hitta den virtuella datorn med nödvändiga kärnor och minne | Azure kunde inte hitta en lämplig VM-typ. Minska minnet och antalet kärnor för den lokala datorn innan du migrerar.
Det gick inte att fastställa VM-lämplighet på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för en eller flera diskar på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för ett eller flera nätverkskort på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Jag kan inte ange Enterprise-avtal (EA) som ett Azure-erbjudande i utvärderings egenskaperna.
Azure Migrate Server-utvärderingen stöder för närvarande inte Enterprise-avtal (EA)-baserade priser. Undvik den här begränsningen genom att använda **betala per** användning som Azure-erbjudande och Använd egenskapen **rabatt** för att ange en anpassad rabatt som du får. [Mer information om hur du kan anpassa en utvärdering](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Varför markerar Server utvärderingen mina virtuella Linux-datorer med villkoret klar?
Det finns ett känt mellanrum i Server utvärderingen som gör att den inte kan identifiera den lägre versionen av Linux-operativsystem som är installerad på de lokala virtuella datorerna (till exempel för RHEL 6,10 identifieras endast RHEL 6 som operativ system version) av Server utvärderingen. Eftersom Azure bara godkänner vissa versioner av Linux är de virtuella Linux-datorerna för närvarande markerade som villkorligt klara i Server utvärderingen. Du kan ta reda på om Linux-operativsystemet som körs på den lokala virtuella datorn har godkänts i Azure genom att granska [support dokumentationen för Azure Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros) När du har verifierat den godkända distributionen kan du ignorera den här varningen.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Varför har den VM-SKU som rekommenderas av Server utvärderingen fler kärnor och mer minne än vad som har allokerats lokalt?
Vilken VM-SKU som rekommenderas i Server Assessment beror på utvärderingsegenskaperna. Du kan skapa två typer av utvärderingar i Server utvärderingen: *Prestanda-* och *som lokalt*. För prestandabaserade utvärderingar beaktar Server utvärderingen användnings data för de lokala virtuella datorerna (CPU, minne, disk och nätverks användning) för att fastställa rätt mål-SKU för virtuella datorer för dina lokala virtuella datorer. För prestandabaserade storleks ändringar beaktas dessutom den praktiska faktorn för att fastställa effektiv användning. För lokal storleks sortering beaktas inte prestanda data och en SKU för ett mål rekommenderas, baserat på vad som är allokerat lokalt.

Anta till exempel att det finns en lokal virtuell dator med 4 kärnor och 8 GB minne med 50% processor användning och 50% minnes användning, och en bekvämlighets faktor på 1,3 anges i utvärderingen. Om storleks kriteriet för utvärderingen är **lokalt**, rekommenderas en Azure VM-SKU med 4 kärnor och 8 GB minne.

Anta dock att storleks kriteriet är prestanda beroende. Baserat på effektiv processor-och minnes användning (50% av 4 kärnor * 1,3 = 2,6 kärnor och 50% av 8 GB minne * 1,3 = 5,3-GB minne), så rekommenderas billigaste VM-SKU: n för fyra kärnor (närmsta antal kärnor) och 8 GB minne (närmaste minnes storlek som stöds). [Läs mer om storleksändring i Server Assessment](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Varför rekommenderas den disk-SKU som rekommenderas av Server utvärderingen större än vad som har allokerats lokalt?
Disk storlek i Server utvärderingen är beroende av två bedömnings egenskaper: storleks kriterier och lagrings typ. Om storleks kriteriet är **prestanda beroende** och lagrings typen är inställd på **Automatisk**, anses diskens IOPS-och data flödes värden som identifierar mål disk typen (standard HDD, standard SSD eller Premium diskar). En disk-SKU från disk typen rekommenderas och den här rekommendationen tar hänsyn till storleks kraven för den lokala disken. Om storleks kriteriet är **prestanda baserat**och lagrings typen är **Premium**rekommenderas en SKU för Premium-diskar i Azure baserat på IOPS, data flöde och storleks krav för den lokala disken. Samma logik används för att utföra disk storlek när storleks kriteriet är **lokalt** och lagrings typen är **standard HDD**, **standard SSD**eller **Premium**.

Om du till exempel har en lokal disk med 32 GB minne, men den aggregerade läsnings-och skriv-IOPS för disken är 800 IOPS, rekommenderar Server utvärderingen en Premium disk typ (på grund av de högre IOPS-kraven) och rekommenderar sedan en disk-SKU som stöder den nödvändiga IOPS och storlek. Den bästa matchningen i det här exemplet är P15 (256 GB, 1100 IOPS). Även om den storlek som krävs av den lokala disken var 32 GB, rekommenderar Server utvärderingen en större disk på grund av det höga IOPS-kravet för den lokala disken.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Varför visar min utvärderings rapport "PercentageOfCoresUtilizedMissing" eller "PercentageOfMemoryUtilizedMissing" för vissa virtuella datorer?
De här problemen rapporteras när Azure Migrate-enheten inte kan samla in prestanda data för de lokala virtuella datorerna. Detta kan inträffa om de virtuella datorerna är avstängda under tiden när du skapar utvärderingen (senast en dag/en vecka/en månad). Enheten kan inte samla in prestanda data för en virtuell dator när den är avstängd. Om endast minnes räknarna saknas och du försöker utvärdera virtuella Hyper-V-datorer, kontrollerar du om du har dynamiskt minne aktiverat på de här virtuella datorerna. Det finns ett känt problem där en Azure Migrate-apparat inte kan samla in minnes användnings data för virtuella datorer som inte har dynamiskt minne aktiverat. Det här problemet påverkar endast virtuella Hyper-V-datorer, inte virtuella VMware-datorer. Om någon av prestanda räknarna saknas går Azure Migrate Server utvärderingen tillbaka till allokerade kärnor och minne, och det rekommenderar en motsvarande VM-storlek.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Ingår OS-licens kostnaden för den virtuella datorn i beräknings kostnaden beräknad av Server utvärderingen?
Server utvärderingen betraktar för närvarande bara OS-licensens kostnad för Windows-datorer. OS-licensens kostnader för Linux-datorer anses inte för närvarande.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Vilken effekt påverkar prestanda historik och percentils användning på storleks rekommendationerna?
Dessa egenskaper gäller endast för prestandabaserade storleks ändringar. Server Assessment samlar kontinuerligt in prestandadata för lokala datorer och använder dem för att rekommendera VM-SKU:n och disk-SKU:n i Azure. Prestanda data samlas in av Server utvärdering enligt följande:
- Azure Migrates apparaten registrerar kontinuerligt den lokala miljön för att samla in användnings data i real tid var 20: e sekund för virtuella VMware-datorer och var 30: e sekund för virtuella Hyper-V-datorer.
- Installations programmet samlar in de 20 andra och 30 sekunderna exemplen för att skapa en enda data punkt för var tionde minut. Om du vill skapa en enskild data punkt väljer du det högsta värdet från alla exempel för 20-sekunder och 30 sekunder och skickar det sedan till Azure.
- När du skapar en utvärdering i Server Assessment, baserat på precentilvärdet för prestandavaraktighet och prestandahistorik, identifieras det representativa användningsvärdet. Om prestanda historiken t. ex. är en vecka och percentilen är 95, sorterar Azure Migrate alla exempel punkter på 10 minuter under de senaste 1 veckorna i stigande ordning och väljer sedan 95-percentilen som det representativa värdet.
Värdet 95 percentil ser till att du ignorerar eventuella avvikande värden, som kan inkluderas om du väljer 99-percentilen. Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden, bör du välja den 99 percentilen för percentils användning.

## <a name="dependency-visualization-issues"></a>Problem med beroende visualisering

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Jag kan inte hitta funktionen för beroende visualisering för Azure Government projekt.

Azure Migrate är beroende av Tjänstkarta för funktionerna för beroende visualisering. Eftersom Tjänstkarta för närvarande inte är tillgängligt i Azure Government är den här funktionen inte tillgänglig i Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Jag har installerat Microsoft Monitoring Agent (MMA) och beroende agenten på mina lokala virtuella datorer, men beroendena visas nu i Azure Migrate-portalen.

När du har installerat agenterna tar Azure Migrate vanligt vis 15-30 minuter att Visa beroenden i portalen. Om du har väntat i mer än 30 minuter ser du till att MMA kan kommunicera med OMS-arbetsytan genom att följa dessa steg.

För virtuell Windows-dator:
1. Gå till kontroll panelen och starta Microsoft Monitoring Agent.
2. På fliken **Azure Log Analytics (OMS)** i dialog rutan **Egenskaper för Microsoft Monitoring Agent** kontrollerar du att arbets ytans **status** är grön.
3. Om statusen inte är grön kan du försöka ta bort arbets ytan och lägga till den igen till MMA.

      ![Dialog rutan Egenskaper för MMA](./media/troubleshooting-general/mma-status.png)

För virtuella Linux-datorer kontrollerar du att installations kommandona för MMA och beroende agenten lyckades.

### <a name="what-operating-systems-does-mma-support"></a>Vilka operativ system stöder MMA?

 [Här är en lista över Windows-operativsystem som stöds av MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Och [här är en lista över Linux-operativsystem som stöds av MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>Vilka operativ system stöder beroende agenten?

[Här är en lista över [Windows-och Linux-operativsystem som Azure Monitor for VMS stöder](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Jag kan inte visualisera beroenden i Azure Migrate i mer än en timmes varaktighet.
I Azure Migrate kan du visualisera beroenden i upp till en timmes varaktighet. Även om Azure Migrate ger dig möjlighet att gå tillbaka till ett visst datum under den senaste månaden, är den längsta varaktigheten som du kan visualisera beroenden en timme.

Du kan till exempel använda funktionen tids varaktighet i beroende kartan för att Visa beroenden för igår, men du kan bara visa dem för en timmes period. Du kan dock använda Azure Monitor loggar för att [fråga beroende data](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre varaktighet.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Jag kan inte visualisera beroenden för grupper som har fler än 10 virtuella datorer.
Du kan [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) som har upp till 10 virtuella datorer, om du har en grupp med fler än 10 virtuella datorer, rekommenderar vi att du delar upp gruppen i mindre grupper och visualiserar beroendena.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Jag har installerat agenter och använde beroende visualiseringen för att skapa grupper. Nu efter redundansväxlingen, visar datorerna "installera agent"-åtgärd i stället för "Visa beroenden".
* Efter en planerad eller oplanerad redundansväxling är lokala datorer inaktiverade och motsvarande datorer är i Azure. De här datorerna hämtar en annan MAC-adress. De kan hämta en annan IP-adress baserat på om användaren väljer att behålla en lokal IP-adress.

  Om både MAC-och IP-adresser skiljer sig Azure Migrate associerar inte lokala datorer med Tjänstkarta beroende data. I stället uppmanas användaren att installera agenter i stället för att Visa beroenden.
* Efter testningen av redundans är de lokala datorerna fortfarande aktiverade som förväntat. Likvärdiga datorer som är förändrade i Azure får en annan MAC-adress och kan förvärva olika IP-adresser. Om inte användaren blockerar utgående Azure Monitor logg trafik från de här datorerna associerar Azure Migrate inte de lokala datorerna med några Tjänstkarta beroende data och ber användarna att installera agenter i stället för att Visa beroenden.

## <a name="collect-azure-portal-logs"></a>Samla in Azure Portal loggar

**Hur gör jag för att samla in Azure Portal nätverks trafik loggar?**

1. Öppna webbläsaren, gå [till portalen](https://portal.azure.com)och logga in.
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
