---
title: "Felsöka Azure SQL-datasynkronisering (förhandsversion) | Microsoft Docs"
description: "Lär dig hur du felsöker vanliga problem med Azure SQL-datasynkronisering (förhandsversion)."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: a24e16abd78a89d6143c00fc08a3ef91d7dd8202
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Felsökning av problem med SQL-datasynkronisering (förhandsgranskning)

Den här artikeln beskriver hur du felsöker kända problem med Azure SQL Data Sync (förhandsversion). Om det finns en lösning på ett problem, som den här.

En översikt över SQL datasynkronisering (förhandsgranskning), se [synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Synkroniseringsproblem

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>Synkroniseringen misslyckas i portalens användargränssnitt för lokala databaser som är associerade med klientens agent

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Synkronisering misslyckas på SQL-datasynkronisering (förhandsgranskning) portalens användargränssnitt för lokala databaser som är associerade med agenten. På den lokala datorn som kör agenten finns System.IO.IOException fel i händelseloggen. Fel som säger att disken har inte tillräckligt med utrymme.

#### <a name="resolution"></a>Lösning

Skapa mer utrymme på enheten där katalogen % TEMP % finns.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Min sync-grupp har fastnat i bearbetningstillstånd

#### <a name="description-and-symptoms"></a>Beskrivning och problem

En grupp för synkronisering i SQL-datasynkronisering (förhandsversion) har i bearbetningstillstånd under lång tid. Svarar inte på den **stoppa** kommandot och loggarna visar inga nya poster.

#### <a name="cause"></a>Orsak

Något av följande villkor kan resultera i en grupp för synkronisering har fastnat i bearbetningstillstånd:

-   **Klientagenten är offline**. Kontrollera att klientagenten är online och försök sedan igen.

-   **Klientagenten är installeras eller saknas**. Om klientagenten installeras eller på annat sätt saknas:

    1. Ta bort agenten XML-filen från installationsmappen SQL datasynkronisering (förhandsgranskning) om filen finns.
    2. Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agent-nyckel som skapas i portalen för den agent som visas som offline.

-   **Datasynkronisering för SQL-tjänsten har stoppats**.

    1. I den **starta** -menyn, söka efter **Services**.
    2. I sökresultaten väljer **Services**.
    3. Hitta de **SQL datasynkronisering (förhandsgranskning)** service.
    4. Om tjänstestatus är **stoppad**, högerklicka på tjänstens namn och välj sedan **starta**.

#### <a name="resolution"></a>Lösning

Om informationen ovan inte flytta sync-gruppen från bearbetningstillstånd, återställa Microsoft-supporten status för synkronisering-grupp. Om du vill ha din grupp synkroniseringsstatus Återställ, i den [Azure SQL Database-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), skapa en post. I post, bland annat prenumerations-ID och synkronisera grupp-ID för gruppen som ska återställas. En Microsoft Support-tekniker att svara på ditt inlägg och informerar dig om när status har återställts.

### <a name="i-see-erroneous-data-in-my-tables"></a>Det finns felaktiga data i Mina tabeller

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Om tabeller som har samma namn men som är från en annan databas scheman ingår i en synkronisering, se felaktiga data i tabeller efter synkroniseringen.

#### <a name="cause"></a>Orsak

SQL-datasynkronisering (förhandsgranskning) etableringsprocessen använder samma spårningstabeller för tabeller som har samma namn men som är i olika scheman. Därför visas ändringar från båda tabellerna i tabellen samma uppföljning. Detta gör felaktiga ändringar under synkroniseringen.

#### <a name="resolution"></a>Lösning

Se till att namnen på de tabeller som ingår i en annan, även om tabellerna som tillhör olika scheman i en databas.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Inkonsekventa data av den primära nyckeln visas efter en lyckad synkronisering

#### <a name="description-and-symptoms"></a>Beskrivning och problem

En synkronisering har rapporterats som lyckas och loggen visar inga misslyckade eller överhoppade rader, men du märka att primära viktiga data stämmer inte överens mellan databaser i gruppen synkronisering.

#### <a name="cause"></a>Orsak

Detta är avsiktligt. Ändringar i en primärnyckelkolumn leda till inkonsekventa data i rader där den primära nyckeln har ändrats.

#### <a name="resolution"></a>Lösning

Se till att inga data i en primärnyckelkolumn ändras för att förhindra det här problemet.

Ta bort en rad som innehåller inkonsekventa data från alla slutpunkter i gruppen sync för att åtgärda problemet när det har uppstått. Sedan infoga raden.

### <a name="i-see-a-significant-degradation-in-performance"></a>Det finns en kraftig prestandaförsämring i prestanda

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Din prestanda försämras avsevärt, möjligen till den punkt där du kan även öppna Gränssnittet för synkronisering av Data.

#### <a name="cause"></a>Orsak

Den troligaste orsaken är en sync-loop. En loop sync inträffar när en synkronisering av sync grupp A utlöser en synkronisering av sync-grupp B, som utlöser en synkronisering av sync grupp A. Den faktiska situationen kan vara mer komplexa och det kan handla om fler än två synkroniseringsgrupper i en slinga. Problemet är att det finns en cirkulär utlöst synkroniserar som orsakas av synkroniseringsgrupper överlappar varandra.

#### <a name="resolution"></a>Lösning

Bästa finns förebyggande. Se till att du inte har cirkelreferenser i sync-grupper. Kan inte synkroniseras alla rader som är synkroniserad med en grupp för synkronisering av en annan synkronisering grupp.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Detta meddelande visas ”: Det går inte att infoga värdet NULL i kolumnen \<kolumnen\>. Kolumnen tillåter inte null-värden ”. Vad innebär det och hur kan jag åtgärda det? 
Det här felmeddelandet innebär att ett av de två följande problem har uppstått:
-  En tabell saknar primärnyckel. Åtgärda problemet genom att lägga till en primär nyckel i alla tabeller som du synkronisera.
-  Det finns en WHERE-sats i CREATE INDEX-instruktionen. Datasynkronisering (förhandsversion) kan inte hantera det här villkoret. Åtgärda problemet genom att ta bort WHERE-satsen eller göra ändringarna manuellt för alla databaser. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Hur hanterar datasynkronisering (förhandsgranskning) cirkelreferenser? Det vill säga när samma data synkroniseras i flera synkroniseringsgrupper och ändras som ett resultat?
Datasynkronisering (förhandsversion) kan inte hantera cirkelreferenser. Se till att undvikas. 

## <a name="client-agent-issues"></a>Agenten klientproblem

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>Klientagenten installera, avinstallera eller reparera misslyckas

#### <a name="cause"></a>Orsak

Många scenarier kan orsaka detta fel. Granska loggarna för att fastställa orsaken till detta fel.

#### <a name="resolution"></a>Lösning

Generera och titta på Windows Installer-loggarna för att hitta orsaken till felet. Du kan aktivera loggning i en kommandotolk. Om den hämta filen AgentServiceSetup.msi LocalAgentHost.msi generera och undersöka loggfiler med hjälp av följande kommandorader:

-   För installationer: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Så här avinstallerar för: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Du kan också aktivera loggning för alla installationer som utförs av Windows Installer. I Microsoft Knowledge Base-artikel [hur du aktiverar loggning för Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) ger en enkelklickning lösning för att aktivera loggning för Windows Installer. Det ger också platsen för loggarna.

### <a name="my-client-agent-doesnt-work"></a>Min klientagenten fungerar inte

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Du får följande meddelanden när du försöker använda klientagenten:

”Synkronisering misslyckades med undantaget som ett fel uppstod vid försök att avserialisera parametern www.microsoft.com/.../05:GetBatchInfoResult. (Se InnerException för mer information ”.

”Inre Undantagsmeddelande: typ 'Microsoft.Synchronization.ChangeBatch' är en ogiltig samlingstyp eftersom den inte har en standardkonstruktor”.

#### <a name="cause"></a>Orsak

Detta är ett känt problem med installation av SQL-datasynkronisering (förhandsversion). Den troligaste orsaken till det här meddelandet är en av följande:

-   Du kör Windows 8 Developer Preview.
-   Du har .NET Framework 4.5 är installerat.

#### <a name="resolution"></a>Lösning

Se till att du installerar klientagenten på en dator som inte kör Windows 8 Developer Preview och att .NET Framework 4.5 inte är installerad.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Min klientagenten fungerar inte när du har avbrutit avinstallationen

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Klientagenten fungerar inte även efter att du avbryter dess avinstallationen.

#### <a name="cause"></a>Orsak

Detta inträffar eftersom autentiseringsuppgifterna inte lagras i klientagenten SQL datasynkronisering (förhandsversion).

#### <a name="resolution"></a>Lösning

Du kan försöka att dessa två lösningar:

-   Använd services.msc för att ange autentiseringsuppgifterna för klientagenten.
-   Avinstallera den här klientagenten och installera en ny. Hämta och installera den senaste klientagenten från [Download Center](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Databasen är inte visas i listan agent

#### <a name="description-and-symptoms"></a>Beskrivning och problem

När du försöker lägga till en befintlig SQL Server-databas i en synkronisering visas databasen inte i listan över agenter.

#### <a name="cause"></a>Orsak

Dessa scenarier kan orsaka det här problemet:

-   Agent och sync Klientgruppen finns i olika datacenter.
-   Klientagenten listan över databaser är inte aktuell.

#### <a name="resolution"></a>Lösning

Lösningen är beroende av orsaken.

- **Klientgrupp agent och sync är i olika Datacenter**

    Klientagenten och gruppen synkronisering måste vara i samma datacenter. Om du vill konfigurera det här har du två alternativ:

    -   Skapa en ny agent i datacenter där sync-gruppen finns. Registrera sedan databasen med agenten.
    -   Ta bort den aktuella sync-gruppen. Sedan återskapa gruppen synkronisering i datacenter där agenten finns.

- **Klientagenten listan över databaser är inte aktuell**

    Stoppa och starta sedan om klient-agenttjänsten.

    Lokal agent hämtar listan över associerade databaser endast på den första överföringen av agentnyckeln. Det inte att hämta en lista över associerade databaserna på efterföljande agent viktiga bidrag. Databaser som har registrerats under en agent flytta visas inte i den ursprungliga agent-instansen.

### <a name="client-agent-doesnt-start-error-1069"></a>Klientagenten startar inte (fel 1069)

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Du upptäcker att agenten inte körs på en dator som är värd för SQL Server. När du försöker starta agenten manuellt, visas en dialogruta som visar meddelandet ”fel 1069: Det gick inte att starta tjänsten på grund av ett inloggningsfel”.

![Feldialogrutan 1069 för synkronisering](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Orsak

En trolig orsak till felet är att lösenord på den lokala servern har ändrats sedan du skapade agenten och agent lösenord.

#### <a name="resolution"></a>Lösning

Uppdatera agentens lösenordet till ditt nuvarande serverlösenord:

1. Leta upp klientagenten SQL datasynkronisering (förhandsgranskning) Preview service.  
    a. Välj **starta**.  
    b. I sökrutan anger **services.msc**.  
    c. I sökresultaten väljer **Services**.  
    d. I den **Services** och rulla till posten för **datasynkronisering (förhandsversion) för SQL Agent Preview**.  
2. Högerklicka på **datasynkronisering (förhandsversion) för SQL Agent Preview**, och välj sedan **stoppa**.
3. Högerklicka på **datasynkronisering (förhandsversion) för SQL Agent Preview**, och välj sedan **egenskaper**.
4. På **SQL datasynkronisering (förhandsgranskning) agentegenskaper Preview**, Välj den **logga in** fliken.
5. I den **lösenord** ange ditt lösenord.
6. I den **Bekräfta lösenord** rutan, ange ditt lösenord igen.
7. Välj **tillämpa**, och välj sedan **OK**.
8. I den **Services** fönstret högerklickar du på den **datasynkronisering (förhandsversion) för SQL Agent Preview** tjänst och klicka sedan på **starta**.
9. Stäng den **Services** fönster.

### <a name="i-cant-submit-the-agent-key"></a>Jag kan inte skicka agent-nyckeln

#### <a name="description-and-symptoms"></a>Beskrivning och problem

När du skapar eller skapa en nyckel för en agent, som du försöker skicka nyckel via SqlAzureDataSyncAgent-programmet. Det går inte att slutföra programobjektet.

![Synkronisera feldialogrutan - kan inte skicka agent-nyckeln](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Innan du går vidare kontrollerar du om följande villkor:

-   SQL-datasynkronisering (förhandsgranskning) Windows-tjänsten körs.  
-   Tjänstkontot för SQL-datasynkronisering (förhandsgranskning) Preview Windows-tjänsten har åtkomst till nätverket.    
-   Klientagenten kan kontakta Locator-tjänsten. Kontrollera att registernyckeln har värdet https://locator.sync.azure.com/LocatorServiceApi.svc:  
    -   På en x86 dator: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   På en x64 dator: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Orsak

Agentnyckeln identifierar unikt varje lokal agent. Nyckeln måste uppfylla förutsättningar:

-   Klienten agentnyckeln datasynkronisering (förhandsversion) för SQL-servern och den lokala datorn måste vara identiska.
-   Nyckeln för klienten agent kan användas en gång.

#### <a name="resolution"></a>Lösning

Om agenten inte fungerar, beror det på att en eller båda av dessa villkor inte uppfylls. Hämta din agenten ska fungera igen:

1. Skapa en ny nyckel.
2. Tillämpa den nya nyckeln till agenten.

Tillämpa den nya nyckeln för agenten:

1. I Utforskaren går du till din katalog för agentinstallation. Standardinstallationskatalogen är C:\\programfiler (x86)\\Microsoft SQL datasynkronisering.
2. Dubbelklicka på bin-katalogen.
3. Öppna programmet SqlAzureDataSyncAgent.
4. Välj **skicka Agentnyckeln**.
5. Klistra in nyckeln från Urklipp i det angivna området.
6. Välj **OK**.
7. Stäng programmet.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Klientagenten kan inte tas bort från portalen om dess associerade lokala databasen inte kan nås

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Om en lokal slutpunkt (det vill säga en databas) som har registrerats med en SQL-datasynkronisering (förhandsgranskning) klientagenten blir kan inte nås, kan inte klientagenten tas bort.

#### <a name="cause"></a>Orsak

Lokal agent kan inte raderas eftersom databasen inte kan nås fortfarande är registrerad med agenten. När du försöker ta bort agenten försöker borttagningsprocessen nå databasen misslyckas.

#### <a name="resolution"></a>Lösning

Använd ”Framtvinga borttagning” ta bort databasen kan inte nås.

> [!NOTE]
> Om synkronisering metadatatabeller kvar efter en ”Framtvinga borttagning”, använder du deprovisioningutil.exe för att rensa dem.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>Lokal Agent för Sync-app kan inte ansluta till den lokala synkroniseringstjänsten

#### <a name="resolution"></a>Lösning

Prova följande steg:

1. Avsluta appen.  
2. Öppna panelen Component Services.  
    a. I sökrutan i Aktivitetsfältet, ange **services.msc**.  
    b. Dubbelklicka i sökresultaten **Services**.  
3. Stoppa den **SQL datasynkronisering (förhandsgranskning) Preview** service.
4. Starta om den **SQL datasynkronisering (förhandsgranskning) Preview** service.  
5. Öppna appen.

## <a name="setup-and-maintenance-issues"></a>Problem med konfiguration och underhåll

### <a name="i-get-a-disk-out-of-space-message"></a>Jag får ett meddelande om ”disk slut på utrymme”

#### <a name="cause"></a>Orsak

Meddelandet ”disk slut på utrymme” kan visas om överblivna filer måste tas bort. Detta kan orsakas av ett antivirusprogram eller filer är öppna när delete-åtgärder kan utföras.

#### <a name="resolution"></a>Lösning

Ta manuellt bort sync-filer som finns i mappen % temp % (`del \*sync\* /s`). Ta sedan bort underkataloger i % temp %-mappen.

> [!IMPORTANT]
> Ta inte bort några filer när synkronisering pågår.

### <a name="i-cant-delete-my-sync-group"></a>Jag kan inte ta bort min sync-grupp

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Ditt försök att ta bort en grupp för synkronisering misslyckas.

#### <a name="causes"></a>Orsaker

Något av följande scenarier kan resultera i misslyckande med att ta bort en grupp för synkronisering:

-   Klientagenten är offline.
-   Klientagenten är installeras eller saknas. 
-   En databas är offline. 
-   Gruppen sync etablering eller synkroniserar. 

#### <a name="resolution"></a>Lösning

Så här löser du det gick inte att ta bort en sync-grupp:

-   Kontrollera att klientagenten är online och försök sedan igen.
-   Om klientagenten installeras eller på annat sätt saknas:  
    a. Ta bort agenten XML-filen från installationsmappen SQL datasynkronisering (förhandsgranskning) om filen finns.  
    b. Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agent-nyckel som skapas i portalen för den agent som visas som offline.
-   Kontrollera att tjänsten SQL-datasynkronisering (förhandsgranskning) körs:  
    a. I den **starta** -menyn, söka efter **Services**.  
    b. I sökresultaten väljer **Services**.  
    c. Hitta de **SQL datasynkronisering (förhandsgranskning) Preview** service.  
    d. Om tjänstestatus är **stoppad**, högerklicka på tjänstens namn och välj sedan **starta**.
-   Se till att SQL-databaser och SQL Server-databaser är anslutna.
-   Vänta tills processen för etablering eller synkronisering har slutförts och försök sedan synkronisera gruppen tas bort.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Det gick inte att avregistrera en lokal SQL Server-databas

#### <a name="cause"></a>Orsak

Du försöker troligen att avregistrera en databas som redan har tagits bort.

#### <a name="resolution"></a>Lösning

Om du vill avregistrera en lokal SQL Server-databas markerar du databasen och välj sedan **kraft ta bort**.

Om den här åtgärden inte kan ta bort databasen från gruppen synkronisering:

1. Stoppa och starta sedan om klient-Agenttjänsten för värden:  
    a. Välj den **starta** menyn.  
    b. I sökrutan anger **services.msc**.  
    c. I den **program** avsnitt i sökningen resulterar rutan dubbelklickar du på **Services**.  
    d. Högerklicka på den **SQL datasynkronisering (förhandsgranskning)** service.  
    e. Om tjänsten körs, stoppas den.  
    f. Högerklicka på tjänsten och välj sedan **starta**.  
    g. Kontrollera om databasen fortfarande är registrerad. Om det är inte längre registrerad är du klar. Annars fortsätter du med nästa steg.
2. Öppna klientappen agent (SqlAzureDataSyncAgent).
3. Välj **redigera autentiseringsuppgifter**, och ange autentiseringsuppgifterna för databasen.
4. Fortsätt med avregistrering.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>Jag har inte behörighet att starta systemtjänster

#### <a name="cause"></a>Orsak

Detta fel uppstår i två situationer:
-   Användarnamnet och/eller lösenordet är felaktigt.
-   Det angivna användarkontot saknar behörighet att logga in som en tjänst.

#### <a name="resolution"></a>Lösning

Bevilja log-på-as-a-service-autentiseringsuppgifter för användarkontot:

1. Gå till **starta** > **Kontrollpanelen** > **Administrationsverktyg** > **lokal säkerhetsprincip**  >  **Lokal princip** > **användaren Rights Management**.
2. Välj **logga in som en tjänst**.
3. I den **egenskaper** dialogrutan Lägg till användarkontot.
4. Välj **tillämpa**, och välj sedan **OK**.
5. Stäng alla fönster.

### <a name="a-database-has-an-out-of-date-status"></a>En databas med statusen ”inaktuell”

#### <a name="cause"></a>Orsak

SQL-datasynkronisering (förhandsgranskning) tar du bort databaser som har varit frånkopplad från tjänsten i 45 dagar eller mer (som räknas från den tidpunkt som databasen övergick till offlineläge). Om en databas är offline i 45 dagar eller mer och går tillbaka online, är dess status **inaktuella**.

#### <a name="resolution"></a>Lösning

Du kan undvika en **inaktuella** status genom att säkerställa att ingen av databaserna i offlineläge i 45 dagar eller mer.

Om en databas status är **inaktuella**:

1. Ta bort databasen med en **inaktuella** status från gruppen synkronisering.
2. Lägga till databasen till gruppen synkronisera igen.

> [!WARNING]
> Du förlorar alla ändringar som gjorts i den här databasen när den var offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a>En grupp för synkronisering med statusen ”inaktuell”

#### <a name="cause"></a>Orsak

Om en eller flera ändringar inte gäller för hela kvarhållningsperioden 45 dagar, kan en grupp för synkronisering bli inaktuell.

#### <a name="resolution"></a>Lösning

Att undvika ett **inaktuella** status för en grupp för synkronisering, granskar du resultatet av dina synkroniseringsjobb i visningsprogrammet för historik regelbundet. Undersöka och lösa eventuella ändringar som inte att tillämpa.

Om en synkronisering grupp status är **inaktuella**, ta bort gruppen synkronisering och skapa den igen.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>En grupp för synkronisering kan inte tas bort inom tre minuter efter avinstallation eller stoppa agenten

#### <a name="description-and-symptoms"></a>Beskrivning och problem

Du kan inte ta bort en grupp för synkronisering av tre minuter efter avinstallation eller stoppa klientagenten associerade SQL datasynkronisering (förhandsversion).

#### <a name="resolution"></a>Lösning

1. Ta bort en synkronisering medan associerade sync-agenter är online (rekommenderas).
2. Om agenten är offline men har installerats, ta den online på den lokala datorn. Vänta tills statusen för agenten ska visas som **Online** i portalen SQL datasynkronisering (förhandsversion). Ta sedan bort gruppen synkronisering.
3. Om agenten är offline eftersom den avinstallerats:  
    a.  Ta bort agenten XML-filen från installationsmappen SQL datasynkronisering (förhandsgranskning) om filen finns.  
    b.  Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agent-nyckel som skapas i portalen för den agent som visas som offline.  
    c. Försök att ta bort gruppen synkronisering.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>Vad händer när jag återställer en förlorade eller skadade databas?

Om du återställer en förlorade eller skadade databasen från en säkerhetskopia kan finnas det en nonconvergence av data i synkroniseringsgrupper som databasen tillhör.

## <a name="next-steps"></a>Nästa steg
Mer information om SQL-datasynkronisering (förhandsgranskning) finns i:

-   [Synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-sync-data.md)  
-   [Konfigurera Azure SQL datasynkronisering (förhandsgranskning)](sql-database-get-started-sql-data-sync.md)  
-   [Metodtips för datasynkronisering för Azure SQL (förhandsgranskning)](sql-database-best-practices-data-sync.md)  
-   [Övervaka Azure SQL-datasynkronisering (förhandsversion) med OMS logganalys](sql-database-sync-monitor-oms.md)  
-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL-datasynkronisering (förhandsversion):  
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Hämta SQL datasynkronisering (förhandsgranskning) REST API-dokumentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Mer information om SQL-databas finns i:

-   [Översikt över SQL-databas](sql-database-technical-overview.md)
-   [Livscykelhantering för databasen](https://msdn.microsoft.com/library/jj907294.aspx)
