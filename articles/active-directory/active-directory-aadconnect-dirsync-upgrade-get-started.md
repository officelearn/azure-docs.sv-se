<properties
   pageTitle="Azure AD Connect: Uppgradera från DirSync | Microsoft Azure"
   description="Lär dig hur du uppgraderar från DirSync till Azure AD Connect. I den här artikeln lär du dig hur du uppgraderar från DirSync till Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/31/2016"
   ms.author="andkjell;shoatman;billmath"/>

# Azure AD Connect: Uppgradera från DirSync
Azure AD Connect är efterföljaren till DirSync. I det här avsnittet lär du dig hur du kan uppgradera från DirSync på olika sätt. Stegen fungerar inte om du ska uppgradera från en annan version av Azure AD Connect eller från Azure AD Sync.

Innan du börjar installera Azure AD Connect [laddar du ned Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) och kontrollerar att du uppfyller kraven i [Azure AD Connect: Maskinvara och krav](active-directory-aadconnect-prerequisites.md).

Om du inte uppgraderar från DirSync läser du [relaterad dokumentation](#related-documentation) för andra scenarier.

## Uppgradera från DirSync
Beroende på din aktuella DirSync-distribution finns det olika alternativ för uppgraderingen. Om den förväntade tiden för uppgraderingen är mindre än tre timmar rekommenderar vi att du gör en uppgradering på plats. Om den förväntade tiden för uppgraderingen är mer än tre timmar rekommenderar vi att du gör en parallell distribution på en annan server. Uppskattningsvis tar uppgraderingen mer än tre timmar om du har mer än 50 000 objekt.

Scenario |  
---- | ----
[Uppgradering på plats](#in-place-upgrade)  | Rekommenderat alternativ om uppgraderingen förväntas ta mindre än tre timmar.
[Parallell distribution](#parallel-deployment) | Rekommenderat alternativ om uppgraderingen förväntas ta mer än tre timmar.

>[AZURE.NOTE] När du planerar att uppgradera från DirSync till Azure AD Connect ska du inte avinstallera DirSync själv före uppgraderingen. Azure AD Connect läser och migrerar konfigurationen från DirSync och avinstallerar efter att ha inspekterat servern.

**Uppgradering på plats**  
Den förväntade tiden för att slutföra uppgraderingen visas i guiden. Den här uppskattningen baseras på antagandet att det tar tre timmar att slutföra en uppgradering för en databas med 50 000 objekt (användare, kontakter och grupper). Azure AD Connect analyserar dina aktuella DirSync-inställningar och rekommenderar en uppgradering på plats om antalet objekt i databasen är färre än 50 000. Om du väljer att fortsätta tillämpas dina aktuella inställningar automatiskt under uppgraderingen och servern återupptar automatiskt den aktiva synkroniseringen.

Om du vill utföra en konfigurationsmigrering och en parallell distribution kan du åsidosätta rekommendationen om en uppgradering på plats. Du kan till exempel passa på att uppdatera maskinvaran och operativsystemet. Mer information finns i avsnittet om [parallell distribution](#parallel-deployment).

**Parallell distribution**  
En parallell distribution rekommenderas om du har fler än 50 000 objekt. På så sätt undviker du risken för fördröjningar för andra användare. Azure AD Connect-installationen försöker beräkna avbrottstiden för uppgraderingen, men om du har uppgraderat DirSync tidigare är din egen erfarenheter antagligen den bästa riktlinjen.

### DirSync-konfigurationer som stöds som ska uppgraderas
Följande konfigurationsändringar stöds med DirSync och kommer att uppgraderas:

- Domän- och organisationsenhetsfiltrering
- Alternativt ID (UPN)
- Lösenordssynkronisering och Exchange-hybridinställningar
- Din skog/domän och dina Azure AD-inställningar
- Filtrering baserat på användarattribut

Följande ändring kan inte uppgraderas. Om du har gjort den här ändringen blockeras uppgraderingen:

- DirSync-ändringar som inte stöds, t.ex. borttagna attribut och användningen av ett anpassat tilläggs-DLL

![Uppgraderingen har blockerats](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

I dessa fall rekommenderar vi att du installerar en ny Azure AD Connect-server i [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode) och verifierar den gamla DirSync-konfigurationen och den nya Azure AD Connect-konfigurationen. Tillämpa eventuella ändringar med en anpassad konfiguration. Mer information finns i [Anpassad Azure AD Connect Sync-konfiguration](active-directory-aadconnectsync-whatis.md).

Lösenorden som används av DirSync för tjänstkontona kan inte hämtas och migreras inte. Dessa lösenord återställs under uppgraderingen.

### Allmänna steg för att uppgradera från DirSync till Azure AD Connect

1. Välkommen till Azure AD Connect
2. Analys av den aktuella DirSync-konfigurationen
3. Samla in lösenordet för globala Azure AD-administratörer
4. Samla in autentiseringsuppgifterna för ett konto för företagsadministratörer (används endast under installationen av Azure AD Connect)
5. Installation av Azure AD Connect
    * Avinstallera DirSync
    * Installera Azure AD Connect
    * Starta en synkronisering om det behövs

Ytterligare steg krävs om:

* Du har en fullständig SQL-server – lokal eller fjärransluten
* Du har mer än 50 000 objekt som ska synkroniseras

## Uppgradering på plats

1. Starta Azure AD Connect-installationsprogrammet (MSI).
2. Läs och godkänn licensvillkoren och sekretesspolicyn.
![Välkommen till Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Klicka på Nästa när du vill börja analysera din befintliga DirSync-installation.
![Analysera den befintliga Directory Sync-installationen](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. När analysen är klar visas rekommendationer om hur du bör fortsätta.  
    - Om du använder SQL Server Express och har färre än 50 000 objekt visas följande skärm: ![Analysen är klar och du kan uppgradera från DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Om du använder en fullständig SQL-server för DirSync visas den här sidan i stället: ![Analysen är klar och du kan uppgradera från DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
Informationen om den befintliga SQL Server-databasservern som används av DirSync visas. Gör relevanta justeringar om det behövs. Klicka på **Nästa** när du vill fortsätta med installationen.
    - Om du har fler än 50 000 objekt visas den här skärmen i stället: ![Analysen är klar och du kan uppgradera från DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
Om du vill fortsätta med en uppgradering på plats klickar du på kryssrutan bredvid det här meddelandet: **Fortsätt att uppgradera DirSync på den här datorn.**
Om du vill utföra en [parallell distribution](#parallel-deployment) i stället exporterar du DirSync-konfigurationsinställningarna och flyttar dem till den nya servern.
5. Ange lösenordet för det konto som du för närvarande använder för att ansluta till Azure AD. Det här måste vara det konto som för närvarande används av DirSync.
![Ange dina autentiseringsuppgifter för Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
Om du får ett fel och har problem med anslutningen läser du [Felsöka anslutningsproblem](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Ange ett företagsadministratörskonto för Active Directory.
![Ange dina ADDS-autentiseringsuppgifter](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Nu är det dags att konfigurera inställningarna. När du klickar på **Uppgradera** avinstalleras DirSync och Azure AD Connect konfigureras och börjar synkronisera.
![Klart att konfigurera](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. När installationen är klar loggar du ut och loggar sedan in igen i Windows innan du använder Synchronization Service Manager eller Synchronization Rule Editor eller innan du försöker göra andra konfigurationsändringar.

## Parallell distribution

### Exportera DirSync-konfigurationen
**Parallell distribution med mer än 50 000 objekt**

Om du har fler än 50 000 objekt rekommenderar Azure AD Connect-installationen en parallell distribution.

En skärm liknande följande visas:

![Analysen är klar](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Om du vill fortsätta med den parallella distributionen måste du utföra följande steg:

- Klicka på knappen **Exportera inställningar**. När du installerar Azure AD Connect på en separat server importeras dessa inställningar för migrering av eventuella inställningar från din aktuella DirSync till den nya Azure AD Connect-installationen.

När inställningarna har exporterats kan du avsluta Azure AD Connect-guiden på DirSync-servern. Fortsätt med nästa steg för att [installera Azure AD Connect på en separat server](#installation-of-azure-ad-connect-on-separate-server)

**Parallell distribution med mindre än 50 000 objekt**

Om du har färre än 50 000 objekt, men ändå vill utföra en parallell distribution, gör du följande:

1. Kör Azure AD Connect-installationsprogrammet (MSI).
2. När du ser skärmen **Välkommen till Azure AD Connect** avslutar du installationsguiden genom att klicka på ”X” i det övre högra hörnet i fönstret.
3. Öppna en kommandotolk.
4. Kör följande kommando från installationsplatsen för Azure AD Connect (som standard C:\Program Files\Microsoft Azure Active Directory Connect):  `AzureADConnect.exe /ForceExport`.
5. Klicka på knappen **Exportera inställningar**.  När du installerar Azure AD Connect på en separat server importeras dessa inställningar för migrering av eventuella inställningar från din aktuella DirSync till den nya Azure AD Connect-installationen.

![Analysen är klar](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

När inställningarna har exporterats kan du avsluta Azure AD Connect-guiden på DirSync-servern. Fortsätt med nästa steg för att [installera Azure AD Connect på en separat server](#installation-of-azure-ad-connect-on-separate-server)

### Installera Azure AD Connect på en separat server

När du installerar Azure AD Connect på en ny server förutsätts det att du vill utföra en ren installation av Azure AD Connect. Eftersom du vill använda DirSync-konfigurationen måste du utföra några extra steg:

1. Kör Azure AD Connect-installationsprogrammet (MSI).
2. När du ser skärmen **Välkommen till Azure AD Connect** avslutar du installationsguiden genom att klicka på ”X” i det övre högra hörnet i fönstret.
3. Öppna en kommandotolk.
4. Kör följande kommando från installationsplatsen för Azure AD Connect (som standard C:\Program Files\Microsoft Azure Active Directory Connect):  `AzureADConnect.exe /migrate`.
    Installationsguiden för Azure AD Connect startar och följande skärm visas: ![Ange dina autentiseringsuppgifter för Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Välj inställningsfilen som exporterades från DirSync-installationen.
6. Konfigurera eventuella avancerade alternativ, inklusive:
    - En anpassad installationsplats för Azure AD Connect.
    - En befintlig instans av SQL Server (SQL Server 2012 Express installeras som standard av Azure AD Connect). Använd inte samma databasinstans som DirSync-servern.
    - Ett tjänstkonto som används för att ansluta till SQL Server (om SQL Server-databasen är en fjärrplats måste det här kontot vara ett domäntjänstkonto).
Dessa alternativ visas på skärmen: ![Ange dina autentiseringsuppgifter för Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Klicka på **Nästa**.
8. På sidan **Klart att konfigurera** lämnar du kryssrutan **Starta synkroniseringsprocessen så snart som konfigurationen är klar** markerad. Eftersom servern är i [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode) exporteras inga ändringar till Azure AD just nu.
9. Klicka på **Installera**.
10. När installationen är klar loggar du ut och loggar sedan in igen i Windows innan du använder Synchronization Service Manager eller Synchronization Rule Editor eller innan du försöker göra andra konfigurationsändringar.

>[AZURE.NOTE] Synkroniseringen mellan Windows Server Active Directory och Azure Active Directory börjar, men inga ändringar exporteras till Azure AD.  Endast ett synkroniseringsverktyg i taget kan aktivt exportera ändringar. Detta kallas [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode).

### Kontrollera att Azure AD Connect är redo att påbörja synkroniseringen

För att bekräfta att Azure AD Connect är redo att ta över från DirSync måste du öppna **Synchronization Service Manager** i gruppen **Azure AD Connect** från startmenyn.

I programmet öppnar du fliken **Åtgärder**. På den här fliken bekräftar du att följande åtgärder har slutförts:

- Import i AD-anslutningen
- Import i Azure AD Connector
- Fullständig synkronisering i AD-anslutningen
- Fullständig synkronisering i Azure AD Connector

![Importen och synkroniseringen har slutförts](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Granska resultatet från dessa åtgärder och kontrollera att det inte finns några fel.

Om du vill visa och inspektera de ändringar som är på väg att exporteras till Azure AD läser du hur du verifierar konfigurationen under [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode). Gör nödvändiga konfigurationsändringar tills du inte ser något oväntat.

När dessa fyra åtgärder har utförts och det inte finns några fel och du är nöjd med ändringarna som ska exporteras är du redo att avinstallera DirSync och aktivera Azure AD Connect-synkroniseringen. Slutför migreringen gnom att utföra nästa två steg.

### Avinstallera DirSync (den gamla servern)

- Leta upp **Synkroniseringsverktyget för Microsoft Azure Active Directory** i **Program och funktioner**
- Avinstallera **Synkroniseringsverktyget för Microsoft Azure Active Directory**
- Observera att avinstallationen kan ta upp till 15 minuter att slutföra.

När DirSync har avinstallerats finns det ingen aktiv server som exporterar till Azure AD. Nästa steg måste slutföras innan ändringar i din lokala Active Directory fortsätter att synkroniseras till Azure AD.

### Aktivera Azure AD Connect (den nya servern)
Efter installationen kan du öppna Azure AD Connect igen om du vill göra ytterligare konfigurationsändringar. Starta **Azure AD Connect** från Start-menyn eller genvägen på skrivbordet. Var försiktig så att du inte råkar köra MSI-installationsfilen igen.

Du bör se följande:

![Ytterligare uppgifter](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Välj **Konfigurera mellanlagringsläge**.
- Inaktivera mellanlagring genom att avmarkera kryssrutan **Aktivera mellanlagringsläge**.

![Ange dina autentiseringsuppgifter för Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Klicka på knappen **Nästa**
- Klicka på knappen **Installera** på bekräftelsesidan.

Nu är Azure AD Connect din aktiva server.

## Nästa steg
Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](active-directory-aadconnect-whats-next.md).

Lär dig mer om dessa nya funktioner, som aktiverades med installationen: [Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md), [Förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](active-directory-aadconnectsync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

## Relaterad dokumentation

Avsnitt |  
--------- | ---------
Översikt över Azure AD Connect | [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
Uppgradera från en tidigare Connect-version | [Uppgradera från en tidigare version av Connect](active-directory-aadconnect-upgrade-previous-version.md)
Installera med standardinställningar | [Snabbinstallation av Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Konton som används för installation | [Mer information om konton och behörigheter för Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)



<!--HONumber=Jun16_HO2-->


