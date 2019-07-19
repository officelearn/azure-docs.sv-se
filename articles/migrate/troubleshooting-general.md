---
title: Felsöka Azure Migrate problem | Microsoft Docs
description: Innehåller en översikt över kända problem i Azure Migrates tjänsten och fel söknings tips för vanliga fel.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: raynew
ms.openlocfilehash: 0e2a8f269a98babc17f36ceff209ee2f057e6911
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302311"
---
# <a name="troubleshoot-azure-migrate"></a>Felsöka Azure Migrate

[Azure Migrate](migrate-services-overview.md) ger en uppsättning Microsoft-verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV). Det här dokumentet innehåller information om fel sökning av fel med Azure Migrate Azure Migrate: Server utvärdering och Azure Migrate: Server-migrering.

## <a name="azure-migrate-project-issues"></a>Azure Migrate projekt problem

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>Jag kan inte hitta mitt befintliga Azure Migrate-projekt.

Det finns [två versioner](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) av Azure Migrate. Följ stegen nedan för att hitta projektet, beroende på vilken version du skapade projektet:

1. Om du letar efter ett projekt som skapats med tidigare-versionen (tidigare erfarenhet) av Azure Migrate följer du stegen nedan för att hitta projektet.

    1. Gå till [Azure Portal](https://portal.azure.com)och sök efter **Azure Migrate**.
    2. På instrument panelen Azure Migrate visas en banderoll som talar om hur du kommer åt äldre projekt. Den här banderollen visas bara om du har skapat ett projekt med den gamla upplevelsen. Klicka på banderollen.

    ![Komma åt befintliga projekt](./media/troubleshooting-general/access-existing-projects.png)

    3. Nu visas en lista över befintliga projekt som skapats med den tidigare versionen av Azure Migrate.

2. Om du letar efter ett projekt som skapats med den aktuella versionen (ny upplevelse) följer du stegen nedan för att hitta projektet.

    1. Gå till [Azure Portal](https://portal.azure.com)och sök efter **Azure Migrate**.
    2. På instrument panelen för Azure Migrate går du till sidan **servrar** i den vänstra rutan och väljer **ändra** i det övre högra hörnet:

    ![Växla till ett befintligt Azure Migrate-projekt](./media/troubleshooting-general/switch-project.png)

    3. Välj lämplig **prenumeration** och **migrera projekt**.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>Jag kan inte skapa ett andra Azure Migrate-projekt.

Följ stegen nedan för att skapa ett nytt Azure Migrate-projekt.

1. Gå till [Azure Portal](https://portal.azure.com)och sök efter **Azure Migrate**.
2. På instrument panelen för Azure Migrate går du till sidan **servrar** i den vänstra rutan och väljer **ändra** i det övre högra hörnet:

   ![Ändra Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

3. Om du vill skapa ett nytt projekt väljer du **Klicka här** som visas på skärm bilden nedan:

   ![Skapa ett andra Azure Migrate-projekt](./media/troubleshooting-general/create-new-project.png)

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Borttagning av Azure Migrate projekt och tillhör ande Log Analytics arbets yta

När du tar bort ett Azure Migrate-projekt raderas migreringsjobbet tillsammans med metadata om identifierade datorer. Men om du har kopplat en Log Analytics arbets yta till Server Assessment-verktyget, tas inte den Log Analytics-arbetsytan bort automatiskt. Detta beror på att samma Log Analytics arbets yta kan användas för flera användnings fall. Om du vill ta bort arbets ytan Log Analytics måste du göra det manuellt.

1. Bläddra till Log Analytics arbets ytan som är kopplad till projektet.
     1. Om du inte har tagit bort migreringsjobbet ännu kan du hitta länken till arbets ytan från översikts sidan Server utvärdering i avsnittet Essentials.

     ![LA-arbetsyta](./media/troubleshooting-general/loganalytics-workspace.png)

     2. Om du redan har tagit bort migreringsjobbet väljer du **resurs grupper** i det vänstra fönstret i Azure Portal. Gå till resurs gruppen där arbets ytan skapades och bläddra sedan till den.
2. Följ anvisningarna [i den här artikeln](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) för att ta bort arbets ytan.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Det gick inte att skapa ett migreringsjobb med fel *förfrågningar måste innehålla användar identitets rubriker*

Det här problemet kan inträffa för användare som inte har åtkomst till den Azure Active Directory (Azure AD) av organisationen. När en användare läggs till i en Azure AD-klient för första gången får han/hon en e-postinbjudan att ansluta till klienten. Användarna måste gå till e-postmeddelandet och acceptera inbjudan för att kunna lägga till klienten. Om du inte kan se e-postmeddelandet går du till en användare som redan har åtkomst till klienten och ber dem att skicka inbjudan igen till dig med hjälp av de steg som anges [här](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

När e-postinbjudan tas emot måste du öppna e-postmeddelandet och klicka på länken i e-postmeddelandet för att acceptera inbjudan. När detta är gjort måste du logga ut från Azure Portal och logga in igen, så fungerar inte uppdateringen av webbläsaren. Du kan sedan försöka skapa migreringsjobbet.

## <a name="appliance-issues"></a>Problem med installationen

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Det gick inte att distribuera Azure Migrate-enheten för VMware. fel meddelande: Den angivna manifestfilen är ogiltig: Ogiltig OVF-manifest post.

1. Kontrol lera att den Azure Migrate installationen av ägg filen har laddats ned korrekt genom att kontrol lera dess hash-värde. Se [artikeln](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance) för att kontrollera hash-värdet. Om hash-värdet inte matchar laddar du ned den och försöker distribuera igen.
2. Om det fortfarande inte fungerar, och du använder VMware vSphere-klienten för att distribuera OVT, provar du att distribuera den via vSphere-webbklienten. Om det fortfarande inte fungerar kan du prova att använda en annan webbläsare.
3. Om du använder vSphere-webbklienten och försöker distribuera den på vCenter Server 6,5 eller 6,7 kan du försöka distribuera de här datafilerna direkt på ESXi-värden genom att följa stegen nedan:
   - Anslut till ESXi-värden direkt (i stället för vCenter Server) med hjälp av webb klienten (https://<*värdens IP-adress*>/UI).
   - Gå till **Start** > **inventering**.
   - Klicka på **fil** > **distribution OVF-mall**. Bläddra till de ägg och slutför distributionen.
4. Kontakta Azure Migrate support om distributionen fortfarande Miss lyckas.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>Enheten kan inte ansluta till Internet

Detta kan inträffa när datorn som du använder ligger bakom en proxyserver. Se till att du anger autentiseringsuppgifter för auktorisering om proxyn behöver en sådan.
Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutningar, måste du lägga till dessa obligatoriska URL: er i en lista över tillåtna:

Scenario | URL-lista
--- | ---
Server utvärdering för VMware | [Här](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Server utvärdering för Hyper-V | [Här](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
Server migrering för VMware (utan agent) | [Här](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
Server-migrering för VMware (agent-baserad) | [Här](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Server-migrering för Hyper-V | [Här](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Om du använder en avlyssnings-proxy för att ansluta till Internet, måste du importera proxykontot till den virtuella datorns dator. Du kan importera proxy-certifikatet med hjälp av stegen som beskrivs [här](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Fel 802: Fel vid synkronisering av datum och tid

Server klockan kan vara utanför synkroniseringen med den aktuella tiden med mer än fem minuter. Ändra klockan på den insamlade datorn så att den matchar den aktuella tiden enligt följande:

1. Öppna en administratörs kommando tolk på den virtuella datorn.
2. Om du vill kontrol lera tids zonen kör du w32tm/TZ.
3. Kör w32tm/resync. för att synkronisera tiden


### <a name="error-unabletoconnecttoserver"></a>Fel UnableToConnectToServer

Det går inte att ansluta till vCenter-servern ”Servername.com:9443” på grund av felet: Det fanns inte någon slutpunkt som lyssnade på https://Servername.com:9443/sdk som kunde acceptera meddelandet.

Kontrol lera om du kör den senaste versionen av insamlings enheten, om inte, uppgradera installationen till den [senaste versionen](https://docs.microsoft.com/azure/migrate/concepts-collector).

Om problemet fortfarande uppstår i den senaste versionen kan det bero på att insamlings datorn inte kan lösa det vCenter Server namn som angetts eller att den angivna porten är felaktig. Om porten inte anges försöker insamlaren som standard att ansluta till port numret 443.

1. Försök att pinga Servername.com från insamlings datorn.
2. Om steg 1 misslyckas kan du försöka ansluta till vCenter-servern via en IP-adress.
3. Identifiera rätt portnummer för att ansluta till vCenter.
4. Kontrollera slutligen om vCenter-servern är igång.

## <a name="discovery-issues"></a>Identifierings problem

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migrate-tiles-show-a-status-of-discovery-in-progress"></a>Jag har startat identifiering men jag ser inte de identifierade virtuella datorerna på Azure Portal. Panelerna Server utvärdering och Server migrering visar statusen "identifiering pågår"
När du har påbörjat identifieringen från installationen kan du ange en tid för de identifierade datorerna som ska visas på Azure Portal. Det tar ungefär 15 minuter för en VMware-identifiering och cirka 2 minuter per tillagd värd för Hyper-V-identifiering. Om du fortsätter att se "identifiering pågår" även efter den här tiden klickar du på **Uppdatera** på fliken **servrar** . Detta ska visa antalet identifierade servrar i panelerna Server bedömning och Server migrering.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Jag använder den här enheten för att kontinuerligt identifiera min lokala miljö, men de virtuella datorer som tas bort i min lokala miljö visas fortfarande i portalen.

Det tar upp till 30 minuter för identifierings data som samlas in av installations programmet för att avspeglas i portalen. Om du inte ser uppdaterad information ens efter 30 minuter, så skicka en uppdatering av data med hjälp av följande steg:

1. I servrar > Azure Migrate: Server utvärdering, klicka på **Översikt**.
2. Under **Hantera**klickar du på **agenthälsa**
3. Klicka på alternativet för att **uppdatera agenten**. Det här alternativet visas under listan över agenter.
4. Vänta tills uppdaterings åtgärden har slutförts. Kontrol lera att du kan se aktuell information om dina virtuella datorer.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Jag har inte den senaste informationen om de lokala virtuella datorerna på portalen

Det tar upp till 30 minuter för identifierings data som samlas in av installations programmet för att avspeglas i portalen. Om du inte ser uppdaterad information ens efter 30 minuter, så skicka en uppdatering av data med hjälp av följande steg:

1. I servrar > Azure Migrate: Server utvärdering, klicka på **Översikt**.
2. Under **Hantera**klickar du på **agenthälsa**
3. Klicka på alternativet för att **uppdatera agenten**. Det här alternativet visas under listan över agenter.
4. Vänta tills uppdaterings åtgärden har slutförts. Nu bör du se aktuell information om dina virtuella datorer.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Det gick inte att ansluta till värdarna eller klustret eftersom det inte går att matcha Server namnet. WinRM-felkod: 0x803381B9 (fel-ID: 50004)
Det här felet uppstår om DNS-installationen inte kan matcha klustret eller värd namnet som du har angett. Om du ser det här felet i klustret kan du försöka med att ange det fullständiga domän namnet för klustret. 

Du kan också se det här felet för värdar i ett kluster. I det här fallet kan enheten ansluta till klustret. Men klustret har returnerat värd namnen som inte är fullständigt kvalificerade domän namn. 

Lös problemet genom att uppdatera hosts-filen på-enheten genom att lägga till en mappning av IP-adressen och värd namnen.
1. Öppna anteckningar som administratörs användare. Öppna filen C:\Windows\System32\Drivers\etc\hosts.
2. Lägg till IP-adressen och värd namnet i en rad. Upprepa detta för varje värd eller kluster där du ser det här felet.
3. Spara och Stäng hosts-filen.
4. Du kan kontrol lera om installationen kan ansluta till värdarna med appen för hantering av appar. Efter 30 minuter bör du kunna se den senaste informationen på de här värdarna på Azure Portal. 


## <a name="assessment-issues"></a>Utvärderings problem

### <a name="azure-readiness-issues"></a>Problem med Azure-beredskap

Problem | Åtgärd
--- | ---
Start typen stöds inte | Azure stöder inte virtuella datorer med EFI-starttyp. Vi rekommenderar att du konverterar start typen till BIOS innan du kör en migrering. <br/><br/>Du kan använda Azure Migrate Server-migrering för att göra migreringen av sådana virtuella datorer eftersom den kommer att konvertera start typen för den virtuella datorn till BIOS under migreringen.
Villkorligt stöd för Windows OS | Operativ systemets slutdatum har passerat och det krävs ett anpassat support avtal (CSA) för [support i Azure](https://aka.ms/WSosstatement), Överväg att uppgradera operativ systemet innan du migrerar till Azure.
Windows-operativsystem som inte stöds | Azure har endast stöd för [utvalda Windows OS-versioner](https://aka.ms/WSosstatement). Överväg att uppgradera datorns operativ system innan du migrerar till Azure.
Villkorligt godkänt Linux OS | Azure godkänner bara [valda Linux OS-versioner](../virtual-machines/linux/endorsed-distros.md), Överväg att uppgradera datorns operativ system innan du migrerar till Azure.
Avsignerat Linux OS | Datorn kan starta i Azure, men det finns inget stöd för operativ systemet i Azure. Överväg att uppgradera operativ systemet till en [godkänd Linux-version](../virtual-machines/linux/endorsed-distros.md) innan du migrerar till Azure
Okänt operativ system | Operativ systemet för den virtuella datorn angavs som "Övrigt" i vCenter Server, eftersom Azure Migrate inte kan identifiera Azure-beredskap för den virtuella datorn. Kontrol lera att operativ systemet som körs i datorn [stöds](https://aka.ms/azureoslist) av Azure innan du migrerar datorn.
OS-bitness stöds inte | Virtuella datorer med 32-bitars operativ system kan starta i Azure, men vi rekommenderar att du uppgraderar operativ systemet på den virtuella datorn från 32-bit till 64-bit innan du migrerar till Azure.
Kräver Visual Studio-prenumeration. | Datorn har ett operativ system som kör Windows-klienten, vilket bara stöds i Visual Studio-prenumeration.
Det gick inte att hitta den virtuella datorn för nödvändiga lagrings prestanda. | Lagrings prestanda (IOPS/data flöde) som krävs för datorn överskrider stöd för virtuella Azure-datorer. Minska lagrings kraven för datorn innan du migrerar.
Det gick inte att hitta den virtuella datorn för den nödvändiga nätverks prestandan. | Nätverks prestandan (in/ut) som krävs för datorn överskrider stöd för virtuella Azure-datorer. Minska nätverks kraven för datorn.
Den virtuella datorn hittades inte på den angivna platsen. | Använd en annan målplats innan migreringen.
En eller flera olämpliga diskar. | En eller flera diskar som är anslutna till den virtuella datorn uppfyller inte kraven för Azure. För varje disk som är ansluten till den virtuella datorn kontrollerar du att storleken på disken är < 4 TB, om inte, minskar disk storleken innan du migrerar till Azure. Kontrol lera att prestandan (IOPS/data flödet) som krävs för varje disk stöds av Azure- [hanterade virtuella dator diskar](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
Ett eller flera olämpliga nätverkskort. | Ta bort oanvända nätverkskort från datorn innan migreringen.
Antalet diskar överskrider gränsen | Ta bort oanvända diskar från datorn innan migreringen.
Disk storleken överskrider gränsen | Azure har stöd för diskar med upp till 4 TB. Minska diskar till mindre än 4 TB innan migrering.
Disken är inte tillgänglig på den angivna platsen | Kontrol lera att disken finns på mål platsen innan du migrerar.
Disken är inte tillgänglig för den angivna redundansen | Disken bör använda den redundans lagrings typ som definierats i utvärderings inställningarna (LRS som standard).
Det gick inte att fastställa diskens lämplighet på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att hitta den virtuella datorn med nödvändiga kärnor och minne | Azure kunde inte finjustera en lämplig VM-typ. Minska minnet och antalet kärnor för den lokala datorn innan du migrerar.
Det gick inte att fastställa VM-lämplighet på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämpligheten för en eller flera diskar på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för ett eller flera nätverkskort på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Jag kan inte ange Enterprise-avtal (EA) som ett Azure-erbjudande i bedömnings egenskaperna?
Azure Migrate: Server utvärderingen stöder för närvarande inte Enterprise-avtal (EA)-baserade priser. Lösningen är att använda "betala per användning" som Azure-erbjudande och Använd egenskapen "rabatt" för att ange en anpassad rabatt som du får. [Läs mer om hur du kan anpassa en utvärdering](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Varför markerar Server utvärderingen mina virtuella Linux-datorer med villkoret klar. Finns det något jag behöver göra för att åtgärda detta?
Det finns en känd lucka i Server utvärderingen där det inte går att identifiera den lägre versionen av Linux-operativsystem som är installerad på lokala virtuella datorer (till exempel för RHEL 6,10 identifieras endast RHEL 6 som operativ system version) av Server utvärderingen. Eftersom Azure bara godkänner vissa versioner av Linux är de virtuella Linux-datorerna för närvarande markerade som villkorligt klara i Server utvärderingen. Du kan manuellt se till att Linux-operativsystem som körs på den lokala virtuella datorn har godkänts i Azure genom att granska [support dokumentationen för Azure Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros) När du har verifierat den godkända distribution kan du ignorera den här varningen.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Den VM-SKU som rekommenderas av Server utvärderingen har fler antal kärnor och större minnes storlek än vad som allokeras lokalt. Varför?
Den virtuella datorns SKU-rekommendation i Server utvärderingen beror på utvärderings egenskaperna. Du kan skapa två typer av utvärderingar i Server utvärdering, "prestandabaserade" och "som lokala" utvärderingar. För prestandabaserade utvärderingar beaktar Server utvärderingen användnings data för de lokala virtuella datorerna (CPU, minne, disk och nätverks användning) för att fastställa rätt mål-SKU för virtuella datorer för dina lokala virtuella datorer. För prestandabaserade storleks ändringar beaktas dessutom den praktiska faktorn för att identifiera den effektiva användningen. För som lokal storleks sortering beaktas inte prestanda data och en SKU för ett mål rekommenderas, baserat på vad som är allokerat lokalt.

Anta till exempel att det finns en lokal virtuell dator med 4 kärnor och 8 GB minne med 50% processor användning och 50% minnes användning, och en bekvämlighets faktor på 1,3 anges i utvärderingen. Om storleks kriteriet för utvärderingen är "som lokalt" är ett Azure VM-SKU med 4 kärnor och 8 GB minne rekommenderas, men om storleks kriteriet är prestanda baserat på den effektiva processor-och minnes användningen (50% av 4 kärnor * 1,3 = 2,6 kärnor och 50% av 8 GB minne * 1,3 = 5,3 GB minne), billigaste VM-SKU: n för fyra kärnor (närmaste antal kärnor) och minnes storleken på 8 GB (närmsta minnes storlek som stöds) rekommenderas. [Läs mer om hur Server utvärderingen utför storleks ändringar.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)..

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Den disk-SKU som rekommenderas av Server utvärderingen har en större storlek än vad som allokeras lokalt. Varför?
Disk storleken i Server utvärderingen beror på två bedömnings egenskaper – storleks ändrings kriterium och lagrings typ. Om storleks kriteriet är "prestanda" och lagrings typen är inställd på "automatisk", anses diskens IOPS-och data flödes värden som identifierar mål disk typen (Standard HDD, Standard SSD eller Premium diskar). En disk-SKU inom disk typen rekommenderas sedan med hänsyn till storleks kraven för den lokala disken. Om storleks kriteriet är "prestanda" och lagrings typ är "Premium", rekommenderas en Premium disk-SKU i Azure baserat på IOPS, data flöde och storleks krav för lokal disk. Samma logik används för att göra disk storlek när storleks kriteriet är "som lokal storlek och lagrings typ" Standard HDD "," Standard SSD "eller" Premium ".

Om du till exempel har en lokal disk med 32 GB minne, men den aggregerade läsnings-och skriv-IOPS för disken är 800 IOPS, rekommenderar Server utvärderingen en Premium disk typ (på grund av de högre IOPS-kraven) och rekommenderar sedan en disk-SKU som stöder nödvändig IOPS och storlek. Den närmaste matchningen i det här exemplet skulle bli p15 (256 GB, 1100 IOPS). Så även om den storlek som krävs av den lokala disken var 32 GB, rekommenderar Server bedömning en disk med större storlek på grund av det höga IOPS-kravet för den lokala disken.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Ingår OS-licens kostnaden för den virtuella datorn i beräknings kostnaden beräknad av Server utvärderingen?
Server utvärderingen betraktar för närvarande bara OS-licensens kostnad för Windows-datorer, men operativ systemets licens kostnad för Linux-datorer anses inte för närvarande. 

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Vilken effekt påverkar prestanda historik och percentils användning på storleks rekommendationerna?
Dessa egenskaper gäller endast för "prestandabaserad"-storlek. Server utvärderingen samlar kontinuerligt in prestanda data för lokala datorer och använder dem för att rekommendera VM SKU och disk-SKU i Azure. Nedan visas hur prestanda data samlas in av Server utvärderingen:
- Azure Migrates apparaten registrerar kontinuerligt den lokala miljön för att samla in användnings data i real tid var 20: e sekund för virtuella VMware-datorer och var 30: e sekund för virtuella Hyper-V-datorer.
- Enheten samlar in de 20/30 sekunderna exemplen för att skapa en enda data punkt för var tionde minut. Om du vill skapa en enskild data punkt väljer du det högsta värdet från alla exempel på 20/30 sekunder och skickar det till Azure.
- När du skapar en utvärdering i Server utvärderingen, baserat på percentilvärdet för prestanda och prestanda historik, identifieras det representativa användning svärdet. Om prestanda historiken till exempel är en vecka och percentilen är 95, kommer Azure Migrate att sortera alla exempel punkter på 10 minuter under den sista veckan i stigande ordning och sedan välja 95-percentilen som det representativa värdet.
Värdet 95 percentil garanterar att du ignorerar eventuella avvikande värden, som kan inkluderas om du väljer 99 percentil. Om du vill välja högsta användnings nivå för perioden och inte vill ha några avvikande värden bör du välja 99 percentil som percentilvärdet.

## <a name="dependency-visualization-issues"></a>Problem med beroende visualisering

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Jag kan inte hitta funktionen för beroende visualisering för Azure Government projekt.

Azure Migrate är beroende av Tjänstkarta för funktionerna för beroende visualisering och eftersom Tjänstkarta för närvarande inte är tillgänglig i Azure Government, är den här funktionen inte tillgänglig i Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Jag har installerat Microsoft Monitoring Agent (MMA) och beroende agenten på mina lokala virtuella datorer, men beroendena visas nu i Azure Migrate-portalen.

När du har installerat agenterna tar Azure Migrate vanligt vis 15-30 minuter att Visa beroenden i portalen. Om du har väntat i mer än 30 minuter kontrollerar du att MMA-agenten kan kommunicera med OMS-arbetsytan genom att följa stegen nedan:

För virtuell Windows-dator:
1. Gå till **kontroll panelen** och starta **Microsoft Monitoring Agent**.
2. Gå till fliken **Azure Log Analytics (OMS)** i popup-fönstret Egenskaper för MMA.
3. Kontrol lera att arbets ytans **status** är grön.
4. Om statusen inte är grön kan du försöka ta bort arbets ytan och lägga till den igen till MMA.
        ![Status för MMA](./media/troubleshooting-general/mma-status.png)

För virtuella Linux-datorer kontrollerar du att installations kommandona för MMA och beroende agenten har lyckats.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Vilka operativ system stöds av MMA?

Listan över Windows-operativsystem som stöds av MMA finns [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Listan med Linux-operativsystem som stöds av MMA finns [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Vilka operativ system stöds av beroende agenten?

Listan över Windows-operativsystem som stöds av beroende agenten finns [här](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Listan med Linux-operativsystem som stöds av beroende agenten finns [här](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Jag kan inte visualisera beroenden i Azure Migrate i mer än en timmes varaktighet?
Med Azure Migrate kan du visualisera beroenden i upp till en Tim varaktighet. Azure Migrate gör det möjligt att gå tillbaka till ett visst datum i historiken för upp till den senaste månaden, och den längsta varaktigheten för vilken du kan visualisera beroendena är upp till 1 timme. Du kan till exempel använda funktionen tids varaktighet i beroende kartan för att Visa beroenden för igår, men kan bara visa den för ett Tim fönster. Du kan dock använda Azure Monitor loggar för att [fråga beroende data](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre varaktighet.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Jag kan inte visualisera beroenden för grupper med fler än 10 virtuella datorer?
Du kan [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) som har upp till 10 virtuella datorer, om du har en grupp med fler än 10 virtuella datorer, rekommenderar vi att du delar upp gruppen i mindre grupper och visualiserar beroendena.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Jag har installerat agenter och använde beroende visualiseringen för att skapa grupper. Nu efter redundansväxlingen, visar datorerna "installera agent"-åtgärd i stället för "Visa beroenden"
* Publicera planerad eller oplanerad redundans är lokala datorer inaktiverade och motsvarande datorer är i Azure. De här datorerna hämtar en annan MAC-adress. De kan förvärva en annan IP-adress baserat på om användaren väljer att behålla den lokala IP-adressen eller inte. Om både MAC-och IP-adresser skiljer sig, associerar Azure Migrate inte lokala datorer med Tjänstkarta beroende data och ber användaren att installera agenter i stället för att Visa beroenden.
* Efter redundanstest är de lokala datorerna fortfarande aktiverade som förväntat. Likvärdiga datorer som är förändrade i Azure får en annan MAC-adress och kan förvärva en annan IP-adress. Om inte användaren blockerar utgående Azure Monitor loggar trafik från dessa datorer, associerar Azure Migrate inte de lokala datorerna med några Tjänstkarta beroende data och ber användaren att installera agenter i stället för att Visa beroenden.

## <a name="collect-azure-portal-logs"></a>Samla in Azure Portal loggar

**Hur gör jag för att samla in Azure Portal nätverks trafik loggar?**

1. Öppna webbläsaren och navigera och logga in [på portalen](https://portal.azure.com).
2. Starta Utvecklarverktyg genom att trycka på F12. Om det behövs avmarkerar du inställningen **Rensa poster i navigeringen**.
3. Klicka på fliken **nätverk** och börja samla in nätverks trafik:
   - I Chrome väljer du **bevara logg**. Inspelningen bör starta automatiskt. En röd cirkel visar att trafiken fångas. Om den inte visas, klickar du på den svarta cirkeln för att starta.
   - I Microsoft Edge/IE ska inspelningen starta automatiskt. Annars klickar du på den gröna uppspelnings knappen.
4. Försök att återskapa felet.
5. När du har påträffat felet under inspelningen, stoppa inspelningen och spara en kopia av den inspelade aktiviteten:
   - I Chrome högerklickar du på och klickar på **Spara som har innehåll**. Detta komprimerar och exporterar loggarna som en. har-fil.
   - I Microsoft Edge/IE klickar du på ikonen **Exportera insamlad trafik** . Detta komprimerar och exporterar loggen.
6. Gå till fliken **konsol** för att kontrol lera om det finns varningar eller fel. Så här sparar du konsol loggen:
   - I Chrome högerklickar du på valfri plats i konsol loggen. Välj **Spara som**, för att exportera och zippa loggen.
   - I Microsoft Edge/IE högerklickar du på felen och väljer **Kopiera alla**.
7. Stäng Utvecklarverktyg.
