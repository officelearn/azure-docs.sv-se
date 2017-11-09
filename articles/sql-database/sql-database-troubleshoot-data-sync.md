---
title: "Felsöka Azure SQL datasynkronisering | Microsoft Docs"
description: "Lär dig hur du felsöker vanliga problem med Azure SQL-datasynkronisering"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: bbfcac5a54f04f20dbdeeecef7c06b91128b8c6a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync-preview"></a>Felsökning av problem med Azure SQL-datasynkronisering (förhandsgranskning)

Den här artikeln beskriver hur du felsöker aktuella problem som är begränsade till teamet SQL datasynkronisering (förhandsversion). Om det finns en lösning på ett problem, som den här.

En översikt över SQL datasynkronisering finns [synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-sync-data.md).
                                                           
## <a name="my-client-agent-doesnt-work"></a>Min klientagenten fungerar inte

### <a name="description-and-symptoms"></a>Beskrivning och problem

Du får följande felmeddelanden när du försöker använda klientagenten.

”Synkronisering misslyckades med undantaget som ett fel uppstod vid försök att avserialisera parametern www.microsoft.com/.../05:GetBatchInfoResult. Mer information finns i InnerException.

”Inre Undantagsmeddelande: typ 'Microsoft.Synchronization.ChangeBatch' är en ogiltig samlingstyp eftersom den inte har en standardkonstruktor”.


### <a name="cause"></a>Orsak

Det här felet är ett problem med SQL-datasynkronisering (förhandsversion).

Den mest troliga orsaken till problemet är:

-   Du kör Windows 8 Developer Preview eller

-   Du har .NET 4.5 har installerats.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Se till att du installerar klientagenten på en dator som inte kör Windows 8 Developer Preview och att .NET Framework 4.5 inte är installerad.

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Min klientagenten fungerar inte när du har avbrutit avinstallationen

### <a name="description-and-symptoms"></a>Beskrivning och problem

Klientagenten fungerar inte även om du har avbrutit dess avinstallationen.

### <a name="cause"></a>Orsak

Det här problemet beror på att autentiseringsuppgifterna inte lagras i SQL-datasynkronisering (förhandsgranskning) klientagenten.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Det finns två lösningar:

-   Använd först services.msc ange dina autentiseringsuppgifter igen för klientagenten.

-   Därefter avinstallera klientagenten och installera en ny. Hämta och installera den senaste klientagenten från [Download Center](http://go.microsoft.com/fwlink/?linkid=221479).

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>Databasen är listad i listrutan agent

### <a name="description-and-symptoms"></a>Beskrivning och problem

När du försöker lägga till en befintlig SQL Server-databas till en grupp för synkronisering, visas inte i databasen i listrutan.

### <a name="cause"></a>Orsak

Det finns flera möjliga orsaker till problemet:

-   Agent och sync Klientgruppen finns i olika datacenter.

-   Klientagenten listan över databaser är inte aktuell.

### <a name="solution"></a>Lösning

Lösningen är beroende av orsaken.

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>Agent och sync Klientgruppen finns i olika Datacenter

Du måste ha både klientagenten och sync-gruppen i samma datacenter. Du kan ställa in den här konfigurationen genom att göra något av följande saker:

-   Skapa en ny agent i samma datacenter som gruppen synkronisering. Registrera sedan databasen med agenten. Se [hur man: Installera en SQL-datasynkronisering (förhandsgranskning) Klientagent](#install-a-sql-data-sync-client-agent) för mer information.

-   Ta bort den aktuella sync-gruppen. Återskapa det sedan i samma datacenter som agenten.

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>Klientagenten listan över databaser är inte aktuell

Stoppa och starta sedan om klient-agenttjänsten.
Lokal agent hämtar listan över associerade databaser endast på den första överföringen av agentnyckeln, inte på efterföljande agent viktiga bidrag. Därför visas databaser som har registrerats under en agent Flytta inte i den ursprungliga agent-instansen.

## <a name="client-agent-doesnt-start-error-1069"></a>Klientagenten startar inte (fel 1069)

### <a name="description-and-symptoms"></a>Beskrivning och problem

Du upptäcker att agenten inte körs på en dator som värd för SQL Server. När du försöker starta agenten manuellt, du får en feldialogruta med felmeddelandet ”fel 1069: Det gick inte att starta tjänsten på grund av ett inloggningsfel”.

![Feldialogrutan 1069 för synkronisering](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>Orsak

En trolig orsak till felet är att lösenord på den lokala servern har ändrats sedan du skapat agenten och fick ett inloggningslösenord.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Uppdatera agentens lösenordet till ditt nuvarande serverlösenord.

1. Leta upp klientagenten SQL datasynkronisering (förhandsgranskning) Preview service.

    a. Klicka på **starta**.

    b. Skriv ”services.msc” i sökrutan.

    c. I sökresultaten klickar du på ”tjänster”.

    d. I den **Services** och rulla till posten för **datasynkronisering (förhandsversion) för SQL Agent Preview**.

2. Högerklicka på posten och välj **stoppa**.

3. Högerklicka på posten och klicka sedan på **egenskaper**.

4. I den **SQL datasynkronisering (förhandsgranskning) agentegenskaper Preview** -fönstret klickar du på den **logga in** fliken.

5. Ange ditt lösenord i lösenordsrutan.

6. Bekräfta ditt lösenord i textrutan Bekräfta lösenord.

7. Klicka på **Tillämpa** och klicka sedan på **OK**.

8. I den **Services** fönstret högerklickar du på den **datasynkronisering (förhandsversion) för SQL Agent Preview** tjänsten och klicka sedan på **starta**.

9. Stäng den **Services** fönster.

## <a name="i-get-a-disk-out-of-space-message"></a>Jag får ett meddelande om ”disk slut på utrymme”

### <a name="cause"></a>Orsak

Meddelandet ”disk slut på utrymme” kan visas när filerna som ska tas bort är bakom. Det här tillståndet kan inträffa på grund av antivirusprogram eller eftersom filerna är öppna när delete-åtgärder kan utföras.

### <a name="solution"></a>Lösning

Lösningen är att manuellt ta bort filer för synkronisering på `%temp%` (`del \*sync\* /s`), och ta sedan bort underkataloger samt.

> [!IMPORTANT]
> Vänta tills synkroniseringen är klar innan du tar bort några filer.

## <a name="i-cannot-delete-my-sync-group"></a>Jag kan inte ta bort min sync-grupp

### <a name="description-and-symptoms"></a>Beskrivning och problem

Du växlar i ditt försök att ta bort en grupp för synkronisering.

### <a name="causes"></a>Orsaker

Något av följande saker kan resultera i att ta bort en grupp för synkronisering misslyckades.

-   Klientagenten är offline.

-   Klientagenten är installeras eller saknas. 

-   En databas är offline. 

-   Gruppen sync etablering eller synkronisering. 

### <a name="solutions"></a>Lösningar

Så här löser du det inte gick att ta bort en grupp för synkronisering, kontrollera följande:

-   Kontrollera att klientagenten är online och försök sedan igen.

-   Om klientagenten installeras eller på annat sätt saknas:

    a. Ta bort agenten XML-fil från installationsmappen SQL datasynkronisering (förhandsgranskning) om filen finns.

    b. Installera agenten på samma eller om en annan lokal dator, skickar agentnyckeln från portalen genereras för den agent som visas offline.

-   **Tjänsten SQL-datasynkronisering (förhandsversion) har stoppats.**

    a. I den **starta** -menyn, söka efter tjänster.

    b. Klicka på tjänster i sökresultaten.

    c. Hitta de **SQL datasynkronisering (förhandsgranskning) Preview** service.

    d. Om tjänstestatus är **stoppad**, högerklicka på tjänstens namn och välj **starta** från listrutan.

-   Kontrollera din SQL-databaser och SQL Server-databaser för att vara säker på att de är anslutna.

-   Vänta tills processen för etablering eller synkronisering har slutförts. Försök sedan synkronisera gruppen tas bort.

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>Synkroniseringen misslyckas i portalens användargränssnitt för lokala databaser som är associerade med klientens agent

### <a name="description-and-symptoms"></a>Beskrivning och problem

Synkronisering misslyckas på SQL-datasynkronisering (förhandsgranskning) portalens användargränssnitt för lokala databaser som är associerade med agenten. Du ser System.IO.IOException fel i händelseloggen om att disken har inte tillräckligt med utrymme på den lokala datorn som kör agenten.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Skapa mer utrymme på enheten som finns i katalogen % TEMP %.

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Det gick inte att avregistrera en lokal SQL Server-databas

### <a name="cause"></a>Orsak

Du försöker troligen att avregistrera en databas som redan har tagits bort.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Om du vill avregistrera en lokal SQL Server-databas markerar du databasen och klickar på **kraft ta bort**.

Om den här åtgärden inte kan ta bort databasen från sync-grupp, kan du göra följande:

1. Stoppa och starta sedan om klient-Agenttjänsten för värden.

    a. Klicka på Start-menyn.

    b. Ange *services.msc* i sökrutan.

    c. I programmen avsnitt i resultatfönstret dubbelklickar du på **Services**.

    d. Hitta och högerklicka på tjänsten **SQL datasynkronisering (förhandsgranskning)**.

    e. Om tjänsten körs, stoppas den.

    f. Högerklicka och välj **starta**.

    g. Kontrollera om databasen är inte längre registrerad. Om det är inte längre registrerad är du klar. Annars fortsätter du med nästa steg.

2. Öppna klientappen agent (SqlAzureDataSyncAgent).

3. Klicka på **redigera autentiseringsuppgifter** och ange autentiseringsuppgifterna för databasen så att den kan nås.

4. Fortsätt med avregistrering.

## <a name="i-cannot-submit-the-agent-key"></a>Jag kan inte skicka Agent-nyckeln

### <a name="description-and-symptoms"></a>Beskrivning och problem

När du skapar eller skapa en nyckel för en agent, försök att skicka nyckeln via SqlAzureDataSyncAgent program, men överföringen misslyckas att slutföra.

![Synkronisera feldialogrutan - kan inte skicka agent-nyckeln](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Innan du fortsätter bör du kontrollera att ett fel på en av följande villkor inte är orsaken till problemet.

-   SQL-datasynkronisering (förhandsgranskning) Windows-tjänsten körs.

-   Tjänstkontot för SQL-datasynkronisering (förhandsgranskning) Preview Windows-tjänsten har åtkomst till nätverket.

-   Klientagenten kan kontakta tjänsten lokaliserare. Kontrollera att registernyckeln har värdet ”https://locator.sync.azure.com/LocatorServiceApi.svc”

    -   På en x86 dator:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   På en x64 dator:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>Orsak

Agentnyckeln identifierar unikt varje lokal agent. Nyckeln måste uppfylla förutsättningar att fungera:

-   Klienten agentnyckeln datasynkronisering (förhandsversion) för SQL-servern och den lokala datorn måste vara identiska.

-   Nyckeln för klienten agent kan användas en gång.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Om agenten inte fungerar, beror det på att en eller båda av dessa villkor inte uppfylls. Hämta din agenten ska fungera igen:

1. Skapa en ny nyckel.

2. Tillämpa den nya nyckeln till agenten.

Om du vill använda den nya nyckeln till agenten, kan du göra följande:

1. Använd Utforskaren och navigera till din katalog för agentinstallation. Standardinstallationskatalogen är `c:\\program files (x86)\\microsoft sql data sync`.

2. Dubbelklicka på den `bin` underkatalog.

3. Starta den `SqlAzureDataSyncAgent` program.

4. Klicka på **skicka Agentnyckeln**.

5. Klistra in nyckeln från Urklipp i det angivna området.

6. Klicka på **OK**.

7. Stäng programmet.

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>Har inte behörighet att starta systemtjänster

### <a name="cause"></a>Orsak

Detta fel uppstår i två situationer:

-   Användarnamnet och/eller lösenordet är felaktigt.

-   Det angivna användarkontot saknar behörighet att logga in som en tjänst.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Bevilja log-på-as-a-service-autentiseringsuppgifter för användarkontot.

1. Navigera till **starta | Kontrollpanelen | Administrativa verktyg |  Lokal säkerhetsprincip | Lokal princip | Hantering av användarrättigheter**.

2. Hitta och klickar på den **logga in som en tjänst** post.

3. Lägg till användarkontot i den **logga in som en tjänst egenskaper** dialogrutan.

4. Klicka på **gäller** sedan **OK**.

5. Stäng fönstren.

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>Lokal Agent för Sync-app är inte ansluta till den lokala synkroniseringstjänsten

### <a name="solution-or-workaround"></a>Lösning eller lösning

Prova följande steg:

1. Avsluta appen.

2. Öppna panelen Component Services.

    a. I sökrutan i Aktivitetsfältet, skriver du ”services.msc”.

    b. Dubbelklicka på ”tjänster” i sökresultaten.

3. Stoppa och starta sedan om tjänsten ”SQL Sync (förhandsgranskning) förhandsgranskning”.

4. Starta om appen.

## <a name="install-uninstall-or-repair-fails"></a>Installera, avinstallera eller reparera misslyckas

### <a name="cause"></a>Orsak

Det finns många möjliga orsaker till felet. Du måste granska loggarna för att fastställa orsaken till detta fel.

### <a name="solution-or-workaround"></a>Lösning eller lösning

För att hitta orsaken till felet som du har haft måste du generera och titta på Windows Installer-loggarna. Du kan aktivera loggning från kommandoraden. Anta till exempel att den hämta filen AgentServiceSetup.msi är LocalAgentHost.msi. Generera och undersöka loggfiler med hjälp av följande kommandorader:

-   För installationer:`msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   Så här avinstallerar för:`msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Du kan också aktivera loggning för alla installationer som utförs av Windows Installer. I Microsoft Knowledge Base-artikel [hur du aktiverar loggning för Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) ger en enkelklickning lösning för att aktivera loggning för Windows Installer. Det ger också platsen för de här loggarna.

## <a name="a-database-has-an-out-of-date-status"></a>En databas med statusen ”inaktuell”

### <a name="cause"></a>Orsak

SQL-datasynkronisering (förhandsgranskning) tar bort databaser som har varit offline i 45 dagar eller mer (som räknas från den tidpunkt som databasen övergick till offlineläge) från tjänsten. Om en databas är offline i 45 dagar eller mer och går tillbaka online, anges dess status till ”inaktuell”.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Du kan undvika statusen ”inaktuell” genom att se till att ingen av databaserna går offline i 45 dagar eller mer.

Om en databas status är ”inaktuell”, som du behöver göra följande:

1. Ta bort ”inaktuell” databasen från gruppen synkronisering.

2. Lägga till databasen till gruppen synkronisera igen.

> [!WARNING]
> Du förlorar alla ändringar som gjorts i den här databasen när den var offline.

## <a name="a-sync-group-has-an-out-of-date-status"></a>En grupp för synkronisering med statusen ”inaktuell”

### <a name="cause"></a>Orsak

Om en eller flera ändringar inte gäller för hela kvarhållningsperioden 45 dagar, kan en grupp för synkronisering bli inaktuell.

### <a name="solution-or-workaround"></a>Lösning eller lösning

För att undvika statusen ”inaktuell”, granskar du resultatet av dina synkroniseringsjobb i visningsprogrammet för historik regelbundet, och undersöka och lösa eventuella ändringar som inte att tillämpa.

Om en synkronisering grupp status är ”inaktuell”, måste du ta bort gruppen synkronisering och återskapa den.

## <a name="i-see-erroneous-data-in-my-tables"></a>Det finns felaktiga data i Mina tabeller

### <a name="description-and-symptoms"></a>Beskrivning och problem

Om tabeller med samma namn men från olika scheman i en databas är inblandade synkroniserade, ser du felaktiga data i dessa tabeller efter synkronisering.

### <a name="cause-and-fix"></a>Orsak och lösning

SQL-datasynkronisering (förhandsgranskning) etableringsprocessen använder samma spårningstabeller för tabeller med samma namn men olika scheman. Därför ändringar från båda tabellerna visas i tabellen spårning och problemet orsakar felaktiga data ändras under synkroniseringen.

### <a name="resolution-or-workaround"></a>Lösning eller lösning

Kontrollera att namnen på de tabeller som är inblandade i synkroniseringen är olika även om de tillhör olika scheman.

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>Inkonsekventa data av den primära nyckeln visas efter en lyckad synkronisering

### <a name="description-and-symptoms"></a>Beskrivning och problem

När en synkronisering som har rapporterats som lyckas och loggen visar inga misslyckade eller överhoppade rader du märka att primära viktiga data stämmer inte överens mellan databaser i gruppen synkronisering.

### <a name="cause"></a>Orsak

Det här beteendet är avsiktligt. Ändringar i en primärnyckelkolumn leda till inkonsekventa data i rader där den primära nyckeln har ändrats.

### <a name="resolution-or-workaround"></a>Lösning eller lösning

Se till att inga data i en primärnyckelkolumn ändras för att förhindra det här problemet.

Åtgärda problemet när det har uppstått, måste du släppa den aktuella raden från alla slutpunkter i gruppen synkronisering och infoga raden på nytt.

## <a name="i-see-a-significant-degradation-in-performance"></a>Det finns en kraftig prestandaförsämring i prestanda

### <a name="description-and-symptoms"></a>Beskrivning och problem

Din prestanda försämras avsevärt, möjligen till den punkt där du även inte kan starta Användargränssnittet för synkronisering av Data.

### <a name="cause"></a>Orsak

Den troligaste orsaken är en loop synkronisering. En loop synkronisering inträffar när en synkronisering av sync grupp A utlösare en synkronisering av sync grupp B, som i sin tur utlöser en synkronisering av sync grupp A. Den faktiska situationen kan vara mer komplexa som rör fler än två synkroniseringsgrupper i en slinga men viktig faktor är att det finns en cirkulär utlöst synkroniseringar som orsakas av synkroniseringsgrupper överlappar varandra.

### <a name="resolution-or-workaround"></a>Lösning eller lösning

Bästa finns förebyggande. Se till att du inte har cirkelreferenser i sync-grupper. Kan inte synkroniseras alla rader som är synkroniserad med en grupp för synkronisering av en annan synkronisering grupp.

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Klientagenten kan inte tas bort från portalen om dess associerade lokala databasen inte kan nås

### <a name="description-and-symptoms"></a>Beskrivning och problem

Om en lokal slutpunkt (det vill säga en databas) som har registrerats med en SQL-datasynkronisering (förhandsgranskning) klientagenten blir kan inte nås, kan inte klientagenten tas bort.

### <a name="cause"></a>Orsak

Lokal agent kan inte raderas eftersom databasen inte kan nås fortfarande är registrerad med agenten. När du försöker ta bort agenten försöker borttagningsprocessen nå databasen misslyckas.

### <a name="resolution-or-workaround"></a>Lösning eller lösning

Använd ”Framtvinga borttagning” ta bort databasen kan inte nås.

> [!NOTE]
> Om du efter en ”ta bort force” synkronisera metadatatabeller förblir Använd deprovisioningutil.exe att rensa dem.

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>En grupp för synkronisering kan inte tas bort inom tre minuter efter avinstallationen/stoppa agenten

### <a name="description-and-symptoms"></a>Beskrivning och problem

Det går inte att ta bort en grupp för synkronisering inom tre minuter avinstallera/stoppa klientagenten associerade SQL datasynkronisering (förhandsversion).

### <a name="resolution-or-workaround"></a>Lösning eller lösning

1. Ta bort en synkronisering medan associerade sync-agenter är online (rekommenderas).

2. Om agenten är offline men installerade, ta den online på den lokala datorn. Sedan vänta tills statusen för agenten ska visas som online i SQL-datasynkronisering (förhandsgranskning) portal och ta bort gruppen synkronisering.

3. Om agenten är offline eftersom den avinstallerats, kan du göra följande. Försök sedan ta bort gruppen synkronisering.

    a.  Ta bort agenten XML-filen från installationsmappen SQL datasynkronisering (förhandsgranskning) om filen finns.

    b.  Installera agenten på samma eller en annan lokal dator skickar agentnyckeln från portalen genereras för den agent som visas offline.

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Min sync-grupp har fastnat i bearbetningstillstånd

### <a name="description-and-symptoms"></a>Beskrivning och problem

En grupp för synkronisering i SQL-datasynkronisering (förhandsversion) har i bearbetningstillstånd för en längre tidsperiod, inte svarar på stoppkommandot och loggarna visar inga nya poster.

### <a name="causes"></a>Orsaker

Något av följande villkor kan resultera i en grupp för synkronisering har fastnat i bearbetningstillstånd.

-   **Klientagenten är offline.** Kontrollera att klientagenten är online och försök sedan igen.

-   **Klientagenten är installeras eller saknas.** Om klientagenten installeras eller på annat sätt saknas:

    1. Ta bort agenten XML-fil från installationsmappen SQL datasynkronisering (förhandsgranskning) om filen finns.

    2. Installera agenten på samma eller en annan lokal dator. Skicka agent-nyckeln från portalen genereras för den agent som visas som offline.

-   **Tjänsten SQL-datasynkronisering (förhandsversion) har stoppats.**

    1. I den **starta** -menyn, söka efter tjänster.

    2. Klicka på tjänster i sökresultaten.

    3. Hitta de **SQL datasynkronisering (förhandsgranskning)** service.

    4. Om tjänstestatus är **stoppad**, högerklicka på tjänstens namn och välj **starta** från listrutan.

### <a name="solution-or-workaround"></a>Lösning eller lösning

Om det inte går att lösa problemet, status för synkronisering-grupp kan återställas av Microsoft-supporten. För att få din status, återställa, skapa ett foruminlägg på den [Azure SQL Database-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), och inkluderar ditt prenumerations-ID och synkronisera grupp-ID för gruppen som ska återställas. Supportteknikern ska svara på ditt inlägg och att du vet när status har återställts.

## <a name="next-steps"></a>Nästa steg
För mer information om SQL-datasynkronisering, se:

-   [Synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering](sql-database-sync-data.md)
-   [Kom igång med Azure SQL datasynkronisering](sql-database-get-started-sql-data-sync.md)
-   [Metodtips för Azure SQL-datasynkronisering](sql-database-best-practices-data-sync.md)

-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL datasynkronisering:
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Hämta SQL Data Sync REST API-dokumentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

För mer information om SQL-databasen, se:

-   [Översikt över SQL-databas](sql-database-technical-overview.md)
-   [Livscykelhantering för databasen](https://msdn.microsoft.com/library/jj907294.aspx)
