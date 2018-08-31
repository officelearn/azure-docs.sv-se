---
title: Omstrukturera en Team Foundation Server-distribution till Visual Studio Team Services (VSTS) i Azure | Microsoft Docs
description: Lär dig hur Contoso omstrukturerar sin lokala TFS-distributioner genom att migrera den till Visual Studio Team Services (VSTS) i Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: raynew
ms.openlocfilehash: f854c269eaf65335fb5654709fe98857d11865b2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190143"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-visual-studio-team-services-vsts"></a>Contoso-migrering: omstrukturera en Team Foundation Server-distribution till Visual Studio Team Services (VSTS)

Den här artikeln visar hur Contoso omstrukturering sin lokala Team Foundation Server (TFS)-distributionen genom att migrera den till Visual Studio Team Services (VSTS) i Azure. Contosos Utvecklingsteamet har använt TFS för gruppsamarbete och källkontroll under de senaste fem åren. Nu kan vill teamet flytta till en molnbaserad lösning för utveckling och testning arbete och för källkontroll. VSTS kommer att ha en roll som teamet flytta till en DevOps-modell och utveckla nya molnbaserade appar.

Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migreringen och kör olika typer av migreringar. Scenarier växer i komplexitet. Vi lägger till ytterligare artiklar med tiden.


**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera Azure-infrastrukturen](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Contoso kör en utvärdering av dess lokal SmartHotel app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Tillgängligt
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt  
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Byt värd till virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel appen. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso har slutförts en lift and shift-migrering av Linux osTicket app på virtuella Azure-datorer med Azure Site Recovery | Tillgängligt
[Artikel 8: Byta Appvärd en Linux på Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Azure Site Recovery och migrerar app-databasen till en Azure MySQL-Server-instans med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app på Azure Web Apps och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar SmartHotel appen till en Azure Web App och migrerar app-databasen till en Azure SQL Server-instans med Database Migration Assistant | Tillgängligt
[Artikel 10: Omstrukturera en app för Linux på Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt 
Artikel 11: Omstrukturera TFS på VSTS | Contoso migrerar sin lokala Team Foundation Server-distributionen till Visual Studio Team Services i Azure. | Den här artikeln.
[Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt



## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har haft ett nära samarbete med affärspartners att identifiera framtida mål. Partner inte alltför bekymrad utvecklingsverktygen och teknikerna, men de har skapat de här punkterna:

- **Programvara**: oavsett kärnverksamhet, alla företag är nu programvaruföretag, till exempel Contoso. Företag ledarskap är intresserad av hur IT kan hjälpa dig att leda företaget med nya arbetsmetoder för användare och upplevelser för sina kunder.
- **Effektivitet**: Contoso behöver att effektivisera processen och ta bort onödiga procedurer för utvecklare och användare. Detta gör att företaget att leverera på kundernas krav på mer effektivt. Affärsbehoven IT att snabbt och utan att ödsla tid och pengar.
- **Flexibilitet**: Contoso IT måste svara på affärsbehov och reagera snabbare än på marketplace för att aktivera framgång i en global ekonomi. IT får inte vara en blockerare för företaget.

## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned mål för migrering till VSTS:

- Ett verktyg för att migrera data till molnet måste teamet. Några manuella processer ska behövas.
- Data för arbetsobjekt och historik för det senaste året måste migreras.
- Teamet vill inte konfigurera nya användarnamn och lösenord. Alla aktuella system-tilldelningar måste underhållas.
- Teamet vill flytta från Team Foundation Version Control (TFVC) till Git för källkontroll.
- Startpunkt Git är ett ”migreringen av tips” som importerar bara den senaste versionen av källkoden. Det som händer under ett avbrott när allt arbete ska stoppas som kodbas arbetspass. Teamet förstår att endast den aktuella huvudgrenen historiken är tillgänglig efter flytten.
- Teamet är orolig över ändringen och vill testa den innan du gör en fullständig flytt. Teamet vill behålla åtkomsten till TFS även efter övergången till VSTS.
- Contoso har flera samlingar och vill börja med en med endast några projekt att bättre förstå processen.
- Teamet veta att TFS-samlingar är ett förhållande med VSTS-konton med flera URL: er. Men matchar detta den aktuella modellen uppdelning för kodbaser och projekt.


## <a name="proposed-architecture"></a>Föreslagna arkitektur

- Contoso flytta sina TFS-projekt till molnet och inte längre vara värd för sina projekt eller källan kontroll på plats.
- TFS kommer att migreras till VSTS.
- Contoso har för närvarande en TFS-samling som heter **ContosoDev**, som kommer att migreras till en VSTS-konto med namnet **contosodevmigration.visualstudio.com**.
- Projekt, arbetsobjekt, buggar och iterationer från det senaste året kommer att migreras till VSTS.
- Contoso att utnyttja Azure Active Directory, som Contoso ställa in under distributionen av den [Azure-infrastrukturen](contoso-migration-infrastructure.md) i början av planering av migreringsaktiviteter. 


![Scenariots arkitektur](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Migreringsprocessen

Contoso kommer att slutföra migreringen på följande sätt:

1. Det finns en massa förberedelse. Som ett första steg måste Contoso att uppgradera sina TFS-implementering till en nivå som stöds. Contoso körs för närvarande TFS 2017 Update 3, men om du vill använda Databasmigrering måste köra en 2018-version som stöds med de senaste uppdateringarna.
2. Efter uppgraderingen, kommer Contoso köra Migreringsverktyget TFS och validera sin samling.
3. Contoso skapar en uppsättning filer för förberedelse, och utföra en migrering testsändning för testning.
4. Contoso körs en annan migrering, nu en fullständig migrering med arbetsuppgifter, buggar, sprintar och code.
5. Efter migreringen flyttar Contoso sin kod från TFVC till Git.

![Migreringsprocessen](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att slutföra migreringen:

> [!div class="checklist"]
> * **Steg 1: Skapa ett Azure storage-konto**: det här lagringskontot används under migreringsprocessen.
> * **Steg 2: Uppgradera TFS**: Contoso uppgraderar deras distribution till TFS 2018 uppgraderingen 2. 
> * **Steg 3: Kontrollera samling**: Contoso valideras TFS-samling vid förberedelserna för migreringen.
> * **Steg 4: Förbereda filen**: Contoso skapar migreringsfilerna med hjälp av migreringsverktyget TFS. 


## <a name="step-1-create-a-storage-account"></a>Steg 1: Skapa ett lagringskonto

1. I Azure-portalen, Contoso-administratörer för att skapa ett lagringskonto (**contosodevmigration**).
2. De placera kontot i deras sekundära region som de använder för redundans - centrala USA. De använder ett allmänt standard-konto med lokalt-sekundära (redundanta) lagring.

    ![Lagringskonto](./media/contoso-migration-tfs-vsts/storage1.png) 


**Behöver du mer hjälp?**

- [Introduktion till Azure storage](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>Steg 2: Uppgradera TFS

Contoso administratörer uppgradera TFS-servern till TFS 2018 uppdatering 2. Innan de börjar:

- Laddar ned [TFS 2018 uppdatering 2](https://visualstudio.microsoft.com/downloads/)
- De kontrollerar den [maskinvarukrav](https://docs.microsoft.com/tfs/server/requirements), och Läs igenom den [viktig](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) och [uppgradera saker](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018).

De uppgradera på följande sätt:

1. Börja genom de säkerhetskopiera sina TFS-servern (som körs på en virtuell VMware-dator) och ta en ögonblicksbild för VMware.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. TFS-installationsprogrammet startas, och de väljer installationsplatsen. Installationsprogrammet behöver Internetåtkomst.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. När installationen är klar startar guiden för konfiguration av servern.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. Guiden slutförs uppgraderingen när kontrollen är klar.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. De kontrollerar TFS-installationen genom att granska projekt, arbetsuppgifter och kod.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Vissa TFS-uppgraderingar måste du köra guiden Konfigurera funktioner när uppgraderingen är klar. [Läs mer](https://docs.microsoft.com/vsts/work/customize/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**Behöver du mer hjälp?**

Lär dig mer om [uppgraderar TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>Steg 3: Kontrollera TFS-samling

Contoso administratörer köra Migreringsverktyget TFS mot ContosoDev insamlingsdatabasen valideras innan migreringen.

1. De kan hämta och packa upp den [TFS-Migreringsverktyget](https://www.microsoft.com/download/details.aspx?id=54274). Det är viktigt att hämta versionen för TFS-uppdateringen som körs. Versionen kan kontrolleras i administratörskonsolen.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. De kör verktyget för att utföra verifiering, genom att ange URL: en för grupprojektsamlingen:

        **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev**


3. Verktyget visar ett fel.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. De finns i loggen filerna finns i den **loggar** mappen precis innan platsen för verktyget. En loggfil genereras för varje större verifiering. **TfsMigration.log** innehåller den viktigaste informationen.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. De kan hitta den här posten för identitet.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. De körs **TfsMigration Validera/Help** på kommandoraden, och ser till att kommandot **/tenantDomainName** verkar vara nödvändiga för att verifiera identiteter.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. De körs kommandot verifiering igen och inkludera det här värdet, tillsammans med sina Azure AD-namn: **TfsMigrator Validera/Collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. En Azure AD-inloggningen skärmen visas och de anger autentiseringsuppgifterna för en Global administratör-användare.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. Valideringen passerar och bekräftas av verktyget.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>Steg 4: Skapa migreringsfilerna

Contoso-administratörer kan använda Migreringsverktyget TFS för att skapa migreringsfilerna med verifiering klar.

1. De köra förberedelsesteget i verktyget.

    **TfsMigrator förbereda/Collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Förbereda](./media/contoso-migration-tfs-vsts/prep1.png)

    Förbereda följande:
    - Söker igenom samlingen för att hitta en lista över alla användare och fyller identifiera kartan loggen (**IdentityMapLog.csv**])
    - Förbereder anslutningen till Azure Active Directory för att hitta en matchning för varje identitet.
    - Contoso har redan distribuerats Azure AD och synkronisera den med hjälp av AD Connect, så Förbered ska kunna hitta de matchande identiteterna och markera dem som aktiv.

2. En Azure AD-inloggningen skärmen visas och de anger autentiseringsuppgifterna för Global administratör.

    ![Förbereda](./media/contoso-migration-tfs-vsts/prep2.png)

3. Förbereda är klar och verktyget rapporterar att importera filer har skapats.

    ![Förbereda](./media/contoso-migration-tfs-vsts/prep3.png)

4. De kan nu se att både IdentityMapLog.csv och import.json-filen har skapats i en ny mapp.

    ![Förbereda](./media/contoso-migration-tfs-vsts/prep4.png)

5. Filen import.json innehåller importinställningarna. Det omfattar information, till exempel önskad kontonamnet och information på lagringskontot. De flesta fälten fylls i automatiskt. Vissa fält kräver indata från användaren. De öppna filen och lägger till VSTS-kontonamn som ska skapas: **contosodevmigration**. Med det här namnet sina VSTS-URL: en blir **contosodevmigration.visualstudio.com**.

    ![Förbereda](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > Kontot måste skapas före migreringen, kan ändras när migreringen är klar.

6. De loggfilen i identitet karta som visar de konton som ska anslutas till VSTS under importen. 

    - Aktiva identiteter avser identiteter som ska bli användare i VSTS efter importen.
    - Dessa identiteter på VSTS, licensieras och visas som en användare på kontot efter migreringen.
    - Dessa identiteter är märkta **Active** i den **förväntat importstatus** kolumnen i filen.

    ![Förbereda](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-vsts"></a>Steg 5: Migrera till VSTS

Contoso-administratörer kan nu fokusera på migreringen med förberedelse på plats. När du har kört migreringen, kommer de växla från att använda TFVC Git för versionskontroll.

Innan de börjar Schemalägg administratörer driftstopp med dev-teamet att ta samlingen offline under migreringen. Det här är stegen för att migreringen:

1. **Koppla från samlingen**: de identitetsdata för samlingen finns i konfigurationsdatabasen för TFS-servern medan samlingen är online. När en samling kopplas bort från TFS-servern, tar en kopia av den identitetsdata och paket med samlingen för transport. Identity-delen av importen kan inte köras utan att dessa data. Du rekommenderas att samlingen förblir frånkopplade tills importen har slutförts, eftersom det inte finns något sätt att importera de ändringar som uppstod under importen.
2. **Skapa en säkerhetskopia**: nästa steg i migreringsprocessen är att skapa en säkerhetskopia som kan importeras till VSTS. Datanivå program komponenten paket (DACPAC), är en SQL Server-funktion som gör att databasändringar paketeras i en enda fil och distribueras till andra instanser av SQL. Det kan också återställas direkt till VSTS och används därför som paketering metod för att hämta samlingsdata i molnet. Contoso använder verktyget SqlPackage.exe för att generera DACPAC. Det här verktyget ingår i SQL Server Data Tools.
3. **Ladda upp till storage**: efter DACPAC skapas, de överför den till Azure Storage. När paketet har överförts får de en signatur för delad åtkomst (SAS) för att tillåta Migreringsverktyget för TFS-åtkomst till lagringen.
4. **Fyll importen**: de kan sedan fylla i saknade fält i importfilen, inklusive DACPAC-inställningen. Börja med de anger att de vill göra en **blobbar i kontrolläge har** import för att kontrollera att allt fungerar korrekt innan du fullständig migrering.
5. **Gör en testsändning**: testsändning import att testa migrering av samling. Torr körningar har begränsad livslängd och tas bort innan en migrering i produktion körs. De är tas bort automatiskt efter en angiven tidsperiod. En anteckning om när testsändningen kommer att tas bort ingår i de e-postmeddelande tas emot när importen är klar. Notera och planera på lämpligt sätt.
6. **Slutföra migreringen produktion**: med testsändning migreringen har slutförts, Contoso administratörer göra slutliga migreringen genom att uppdatera import.json och köra importera igen.



### <a name="detach-the-collection"></a>Koppla från samlingen

Innan du startar tar Contoso-administratörer en lokal SQL Server-säkerhetskopiering och VMware-ögonblicksbild av TFS-servern innan du kopplar från.

1.  I TFS-administratörskonsolen de väljer du den samling som de vill koppla från (**ContosoDev**).

    ![Migrera](./media/contoso-migration-tfs-vsts/migrate1.png)

2. I **Allmänt**, de väljer **koppla från samlingen**

    ![Migrera](./media/contoso-migration-tfs-vsts/migrate2.png)

3. Koppla från Team projekt samling guiden > **Servicing meddelande**, du får ett meddelande för användare som försöker ansluta till projekt i samlingen.

    ![Migrera](./media/contoso-migration-tfs-vsts/migrate3.png)

4. I **koppla från förloppet**, de övervakar förloppet och klicka på **nästa** när processen är klar.

    ![Migrera](./media/contoso-migration-tfs-vsts/migrate4.png)

5. I **Beredskapskontrollerna**, när kontrollerna är klar med de klickar du på **Detach**.

    ![Migrera](./media/contoso-migration-tfs-vsts/migrate5.png)

6. De klickar på **Stäng** att avsluta.

    ![Migrera](./media/contoso-migration-tfs-vsts/migrate6.png)

6. Samlingen inte längre refererar till i TFS-administratörskonsolen.

    ![Migrera](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>Generera en DACPAC

Contoso-administratörer skapa en säkerhetskopia (DACPAC) för import till VSTS.

- SqlPackage.exe i SQL Server Data Tools används för att skapa DACPAC. Det finns flera versioner av SqlPackage.exe installeras med SQL Server Data Tools finns under mappar med namn, t.ex 120 och 130 140. Det är viktigt att använda rätt version för att förbereda DACPAC.
- TFS 2018 import måste du använda SqlPackage.exe från mappen 140 eller högre.  För CONTOSOTFS, den här filen finns i mappen: **C:\Program Files (x86) \Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Contoso administratörer generera DACPAC på följande sätt:

1. De öppna en kommandotolk och navigera till platsen SQLPackage.exe. De skriver du den här följande kommando för att generera DACPAC:

    **SqlPackage.exe /sourceconnectionstring ”: Data Source = SQLSERVERNAME\INSTANCENAME; Initial Catalog = Tfs_ContosoDev; Integrated Security = True” /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData = true /p: Jag gnoreUserLoginMappings = true /p:IgnorePermissions = true /p:Storage = minne** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. Följande meddelande visas när kommandot har körts.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. De kontrollerar egenskaperna för DACPACfile

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Uppdatera filen till storage

När du har skapat DACPAC överför Contoso den till Azure Storage.

1. De [ladda ned och installera](https://azure.microsoft.com/features/storage-explorer/) Azure Storage Explorer.

    ![Ladda upp](./media/contoso-migration-tfs-vsts/backup5.png)

4. De ansluter till sin prenumeration och leta upp lagringskontot som de har skapat för migreringen (**contosodevmigration**). De skapar en ny blobbehållare, **vstsmigration**.

    ![Ladda upp](./media/contoso-migration-tfs-vsts/backup6.png)

5. De ange DACPAC-fil för överföring som en blockblob.

    ![Ladda upp](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. När användarens överföra filen de klickar på filnamnet > **generera SAS**. De Expandera blob-behållare under storage-konto och välj behållaren med importfiler, klicka **hämta signatur för delad åtkomst**.

    ![Ladda upp](./media/contoso-migration-tfs-vsts/backup8.png)

8. De accepterar du standardvärdena och klickar på **skapa**. På så sätt kan åtkomst i 24 timmar.

    ![Ladda upp](./media/contoso-migration-tfs-vsts/backup9.png)

9. De kopiera delad åtkomst signatur URL, så att den kan användas av migreringsverktyget TFS.

    ![Ladda upp](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> Migreringen måste inträffa innan inom tillåten tid fönster eller behörigheterna upphör att gälla.
> Inte generera en SAS-nyckel från Azure-portalen. Nycklar genereras så här är konto-omfattande och fungerar inte med importen.

### <a name="fill-in-the-import-settings"></a>Fyll i importinställningar

Contoso administratörer fyllt tidigare, delvis i importfilen-specifikationen (import.json). Nu kan behöva de lägga till de återstående inställningarna.

De öppna filen import.json och fylla i följande fält: • plats: platsen för SAS-nyckeln som genererades ovan.
• Dacpac: Anger namnet DACPAC filen du laddade upp till lagringskontot. Omfatta tillägget ”.dacpac”.
• ImportType: inställd på blobbar i kontrolläge har för tillfället.


![Importera inställningar](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Gör en testsändning migrering

Contoso administratörer börja med en testsändning migrering, att kontrollera att allt fungerar som förväntat.

1. De öppna en kommandotolk och leta upp till TfsMigration plats (C:\TFSMigrator).
2. Som ett första steg validera de importfilen. De vill att filen har formaterats korrekt och att SAS-nyckeln fungerar.

    **TfsMigrator importera /importFile:C:\TFSMigrator\import.json /validateonly**

3. Verifieringen returnerar ett fel som behöver en längre förfallotiden för SAS-nyckeln.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test1.png)

3. De kan använda Azure Storage Explorer för att skapa en ny SAS-nyckel med utgångsdatum angivet till sju dagar.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test2.png)

3. De uppdatera filen import.json och köra verifiering igen. Den här gången den har slutförts.

    **TfsMigrator importera /importFile:C:\TFSMigrator\import.json /validateonly**

    ![Testsändning](./media/contoso-migration-tfs-vsts/test3.png)
    
7. De startar testsändningen:

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. Ett meddelande har utfärdats för att bekräfta migreringen. Observera hur lång tid som det mellanlagrade data kommer att finnas kvar efter testsändningen.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test4.png)

9. Azure AD logga In visas och bör vara slutförs med Contoso administratör logga in.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test5.png)

10. Ett meddelande visar information om importen.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test6.png)

11. Efter 15 minuter de Bläddra till URL: en och se följande information:

     ![Testsändning](./media/contoso-migration-tfs-vsts/test7.png)

12. När migreringen är klar, en Contoso-utveckling Leads loggar in på VSTS för att kontrollera att testsändningen fungerat korrekt. Efter autentisering måste VSTS några uppgifter för att bekräfta kontot.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test8.png)

13. I VSTS ser ditt Dev Lead att projekt som har migrerats till VSTS. Det finns ett meddelande om att konton som ska tas bort i 15 dagar.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test9.png)

14. Dev-Lead öppnas ett av projekten och öppnar **arbetsobjekt** > **tilldelats mig**. Detta visar att objektet arbetsdata har migrerats, tillsammans med identiteten.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test10.png)

15. Ditt lead kontrollerar också andra projekt och kod för att bekräfta att källkoden och historik har migrerats.

    ![Testsändning](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Kör migrering för produktion

Med fullständig testsändning gå Contoso administratörer vidare till produktion-migrering. De ta bort testsändningen uppdatera importinställningarna för och kör importera igen.

1. Ta bort kontot testsändning i VSTS-portal de.
2. De uppdatera filen import.json att ställa in den **ImportType** till **ProductionRun**.

    ![Produktion](./media/contoso-migration-tfs-vsts/full1.png)

3. De startar migreringen som de gjorde för testsändningen: **TfsMigrator import /importFile:C:\TFSMigrator\import.json**.
4. Ett meddelande visar för att bekräfta migreringen, och användaren uppmanas att data kan lagras på en säker plats som ett mellanlagringsområde i upp till sju dagar.

    ![Produktion](./media/contoso-migration-tfs-vsts/full2.png)

5. I Azure AD-inloggning anger de Contoso administratör loggar in.

    ![Produktion](./media/contoso-migration-tfs-vsts/full3.png)

6. Ett meddelande visar information om importen.

    ![Produktion](./media/contoso-migration-tfs-vsts/full4.png)

7. Efter cirka 15 minuter, de Bläddra till URL: en, och ser du följande information:

    ![Produktion](./media/contoso-migration-tfs-vsts/full5.png)

8. När migreringen är klar en Contoso Dev leder loggar in på VSTS för att kontrollera att migreringen fungerat korrekt. Efter inloggningen ser ditt lead projekt har migrerats.

    ![Produktion](./media/contoso-migration-tfs-vsts/full6.png)

8. Dev-Lead öppnas ett av projekten och öppnar **arbetsobjekt** > **tilldelats mig**. Detta visar att objektet arbetsdata har migrerats, tillsammans med identiteten.

    ![Produktion](./media/contoso-migration-tfs-vsts/full7.png)

9. Ditt lead kontrollerar andra arbetsdata objekt att bekräfta.

    ![Produktion](./media/contoso-migration-tfs-vsts/full8.png)

15. Ditt lead kontrollerar också andra projekt och kod för att bekräfta att källkoden och historik har migrerats.

    ![Produktion](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>Flytta källkontroll från TFVC till GIT

Contoso vill flytta från TFVC till Git för hantering av källa kod med migreringen har slutförts. Contoso-administratörer måste du importera källkoden för närvarande i VSTS-konto som Git-databaser i samma konto.

1. I VSTS-portal de öppnar någon av TFVC-lagringsplatser (**$/ PolicyConnect**) och granska den.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. De klickar på den **källa** listrutan > **Import**.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. I **källtyp** de väljer **TFVC**, och ange sökvägen till lagringsplatsen. De har valt att inte migrera historiken.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > På grund av skillnader i hur TFVC och Git lagrar information om version kontroll, rekommenderar vi att Contoso inte migrerar historik. Det här är den metod som Microsoft tog när den migrerade Windows och andra produkter från centraliserad versionskontroll till Git.

4. Efter importen kan granska administratörer koden.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. De Upprepa processen för andra databasen (**$/ SmartHotelContainer**).

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. När du har granskat källan godkänner Dev leder att migreringen till VSTS görs. VSTS blir nu källan för alla utveckling inom grupper som ingår i migreringen.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**Behöver du mer hjälp?**

[Läs mer](https://docs.microsoft.com/vsts/git/import-from-tfvc?view=vsts) om hur du importerar från TFVC.

##  <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Med migreringen har slutförts behöver Contoso du göra följande:

- Granska den [efter import](https://docs.microsoft.com/vsts/articles/migration-post-import?view=vsts) artikeln för information om ytterligare import aktiviteter.
- Ta antingen bort TFVC lagringsplatser eller placera dem i skrivskyddat läge. Koden baser får inte används, men kan refereras för deras historik.

## <a name="next-steps"></a>Nästa steg

Contoso behöver du tillhandahålla VSTS och Git utbildning för relevanta gruppmedlemmar.



