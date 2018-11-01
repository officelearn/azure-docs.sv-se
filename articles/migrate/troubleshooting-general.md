---
title: Felsökning av problem med Azure Migrate | Microsoft Docs
description: Innehåller en översikt över kända problem i Azure Migrate-tjänsten och felsökningstips för vanliga fel.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: raynew
ms.openlocfilehash: 0b2954ddfda0ab4c94ddf6176d76d8bcd937fa42
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413341"
---
# <a name="troubleshoot-azure-migrate"></a>Felsöka Azure Migrate

## <a name="troubleshoot-common-errors"></a>Felsöka vanliga fel

[Azure Migrate](migrate-overview.md) utvärderar lokala arbetsbelastningar för migrering till Azure. Använd den här artikeln för att felsöka problem när du distribuerar och använder Azure Migrate.

### <a name="i-am-using-the-continuous-discovery-ova-but-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Jag använder kontinuerlig identifieringen OVA, men virtuella datorer som har tagits bort i min lokala miljö är fortfarande visas i portalen.

Installationen för identifiering av kontinuerlig installation endast samlar in prestandadata kontinuerligt, upptäcks inte varje konfigurationsändring i den lokala miljön (dvs. VM-tillägg, borttagning, disk tillägg osv.). Om det finns en konfigurationsändring i den lokala miljön kan du göra följande för att återspegla ändringarna i portalen:

- Tillägg av objekt (virtuella datorer, kärnor osv.): Om du vill återspegla dessa ändringar i Azure-portalen kan du stoppa identifieringen från installationen och sedan börja om igen. Då uppdateras ändringarna i Azure Migrate-projektet.

   ![Stoppa identifiering](./media/troubleshooting-general/stop-discovery.png)

- Borttagning av virtuella datorer: På grund av hur installationen är utformad återspeglas inte borttagning av virtuella datorer även om du stoppar och startar identifieringen. Det beror på att data från efterföljande identifieringar läggs till äldre identifieringar och inte åsidosätts. I det här fallet kan du helt enkelt ignorera den virtuella datorn genom att ta bort den från gruppen och beräkna utvärderingen.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Skapa projekt för migreringen misslyckades med felet *förfrågningar måste innehålla användaridentitetsrubriker*

Det här problemet kan inträffa för användare som inte har åtkomst till Azure Active Directory (Azure AD)-klient för organisationen. När en användare har lagts till en Azure AD-klient för första gången, får han/hon ett e-postinbjudan för att ansluta till klientorganisationen. Användare måste du gå till e-postmeddelandet och tacka ja till inbjudan har läggs till i klientorganisationen. Om du inte kan se e-postmeddelandet, nå ut till en användare som redan har åtkomst till klienten och be dem att skicka om inbjudan till dig med hjälp av stegen som anges [här](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

När e-postinbjudan tas emot måste du öppna e-postmeddelandet och klicka på länken i e-postmeddelandet att tacka ja till inbjudan. När det är klart måste du logga ut från Azure-portalen och logga in igen, uppdatering av webbläsaren fungerar inte. Du kan sedan försöka skapa migration-projekt.

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>Prestandadata för diskar och nätverk-kort som visar nollor

Detta kan inträffa om statistikinställningen till nivå på vCenter-servern är inställt på mindre än tre. På nivån tre eller högre, lagrar vCenter VM prestandahistorik för beräkning, lagring och nätverk. För mindre än på tre lagrar inte vCenter lagring och nätverksdata, men processor- och data. I det här scenariot prestanda data visas som noll i Azure Migrate Azure Migrate ger storlek rekommendation för diskar och nätverk baserat på de metadata som samlas in från lokala datorer.

Om du vill aktivera insamling av prestandadata för disk- och ändra nivån för statistikinställningar till tre. Sedan, vänta minst en dag för att identifiera din miljö och utvärdera den.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Jag installerade agenter och används visualiseringen av beroenden för att skapa grupper. Nu efter redundans, datorerna visa ”installera agenten” åtgärd i stället för ”Visa beroenden”
* Post planerad eller oplanerad redundans lokala datorer är avstängda och motsvarande datorer skapas i Azure. De här datorerna erhållit en annan MAC-adress. De kan hämta en annan IP-adress baserat på om användaren har valt att behålla den lokala IP-adress eller inte. Om MAC- och IP-adresser skiljer sig åt Azure Migrate kopplar inte lokala datorer med Service Map beroendedata och ber användaren att installera agenter i stället för att visa beroenden.
* Publicera redundanstest, lokala datorer är aktiverade som förväntat. Motsvarande datorer i Azure kunde få annan MAC-adress och kan hämta annan IP-adress. Om inte användaren blockerar utgående trafik för Log Analytics från dessa datorer, Azure Migrate kopplar inte lokala datorer med Service Map beroendedata och ber användaren att installera agenter i stället för att visa beroenden.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>Jag har angett en Azure geografi när du skapar ett migreringsprojekt hur tar jag reda på exakt Azure-regionen där de identifierade metadata lagras?

Du kan gå till den **Essentials** i avsnittet den **översikt** projektets att identifiera den exakta platsen där metadata som lagras. Platsen väljs slumpmässigt i geografiska område som Azure Migrate och du kan inte ändra den. Om du vill skapa ett projekt i en viss region kan du kan använda REST-API: er för att skapa migration-projekt och skicka önskad region.

   ![Projektets plats](./media/troubleshooting-general/geography-location.png)

## <a name="collector-errors"></a>Fel för logginsamlare

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Distribution av Azure Migrate Collector misslyckades med fel: den angivna manifestfilen är ogiltig: Ogiltigt OVF manifest posten.

1. Kontrollera om Azure Migrate Collector OVA-filen laddas ned korrekt genom att kontrollera hash-värdet. Referera till den [artikeln](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance) Kontrollera hash-värde. Om hash-värdet inte matchar, hämta OVA-filen igen och försöker distribuera igen.
2. Om det inte fungerar och om du använder VMware vSphere-klienten för att distribuera OVF, försök att distribuera det via vSphere-webbklienten. Om det fortfarande inte, försök att använda olika webbläsare.
3. Om du använder vSphere-webbklienten och försök att distribuera den på vCenter Server 6.5, försöker distribuera ova-filen direkt på ESXi-värden genom att följa de stegen nedan:
  - Ansluta till ESXi-värden direkt (i stället för vCenter-Server) med hjälp av webbklienten (https:// <*vara värd för IP-adress*> /ui)
  - Gå till startsidan > inventering
  - Klicka på fil > Distribuera OVF-mall > Gå till ova-filen och slutföra distributionen
4. Om distributionen fortfarande misslyckas, kontakta supporten för Azure Migrate.


### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Insamlaren kan inte ansluta till internet

Detta kan inträffa när den dator som du använder är bakom en proxyserver. Kontrollera att du anger autentiseringsuppgifterna om proxyn kräver en.
Om du använder någon Webbadressbaserad brandväggsproxy för att styra utgående anslutningar, var noga med att du godkänna följande obligatoriska URL: er:

**URL** | **Syfte**  
--- | ---
*.portal.azure.com | Krävs för att kontrollera anslutningen med Azure-tjänsten och validera tidssynkronisering utfärdar.
*.oneget.org | Krävs för baserad att ladda ned powershell vCenter PowerCLI-modulen.

**Insamlaren kan inte ansluta till internet på grund av en certifikatsverifieringen misslyckades**

Detta kan inträffa om du använder en spärrande proxy för att ansluta till Internet och om du inte har importerat proxycertifikat in på den Virtuella insamlardatorn. Du kan importera proxy-certifikatet med hjälp av stegen som beskrivs [här](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity).

**Insamlaren kan inte ansluta till projektet med projekt-ID och nyckel jag kopierade från portalen.**

Kontrollera att du har kopierat och klistrat in rätt information. Om du vill felsöka, installera Microsoft Monitoring Agent (MMA) och kontrollera om du MMA kan ansluta till projektet enligt följande:

1. På den Virtuella insamlardatorn, ladda ned den [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Dubbelklicka på den hämta filen om du vill starta installationen.
3. I installationsprogrammet, på den **Välkommen** klickar du på **nästa**. På sidan **Licensvillkor** klickar du på **Jag accepterar** för att acceptera licensen.
4. I **målmapp**, behålla eller ändra standardinstallationsmappen > **nästa**.
5. I **installationsalternativ för Agent**väljer **Azure Log Analytics** > **nästa**.
6. Klicka på **Lägg till** att lägga till en ny Log Analytics-arbetsyta. Klistra in projekt-ID och nyckel som du kopierade. Klicka sedan på **Nästa**.
7. Kontrollera att agenten kan ansluta till projektet. Om det inte går att kontrollera inställningarna. Om agenten kan ansluta men insamlaren inte kan, kontakta supporten.


### <a name="error-802-date-and-time-synchronization-error"></a>Fel 802: Datum och tid synkroniseringsfel

Server klockan kanske ut för synkronisering med den aktuella tiden med fler än fem minuter. Ändra clock-tid i insamlaren så att det matchar den aktuella tiden på följande sätt:

1. Öppna Kommandotolken som administratör på den virtuella datorn.
2. Kör w32tm /tz för att kontrollera den aktuella tidszonen.
3. Kör w32tm/resync för att synkronisera tiden.

### <a name="vmware-powercli-installation-failed"></a>VMware PowerCLI-installationen misslyckades

Azure Migrate collector laddar ned PowerCLI och installerar den på installationen. Fel i PowerCLI-installationen kan bero på kan inte nås slutpunkter för lagringsplatsen som PowerCLI. Om du vill felsöka, försök att manuellt installera PowerCLI i insamlaren virtuell dator med följande steg:

1. Öppna Windows PowerShell i administratörsläge
2. Gå till katalogen C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Kör skriptet InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Interna felet UnhandledException uppstod: System.IO.FileNotFoundException

Det här problemet kan inträffa på grund av ett problem med VMware PowerCLI-installationen. Följ de stegen nedan för att lösa problemet:

1. Om du inte har den senaste versionen av för insamlingsprogram [uppgradera insamlaren till den senaste versionen](https://aka.ms/migrate/col/checkforupdates) och kontrollera om problemet är löst.
2. Om du redan har den senaste versionen av insamlaren kan manuellt installera [VMware PowerCLI 6.5.2](https://www.powershellgallery.com/packages/VMware.PowerCLI/6.5.2.6268016) och kontrollera om problemet är löst.
3. Om ovanstående inte löser problemet, navigera till mappen C:\Program Files\ProfilerService och ta bort VMware.dll och VimService65.dll filer finns i mappen och starta sedan om Azure Migrate Collector-tjänsten i Windows-tjänster hanterar (Öppna ” Kör ”och Skriv” services.msc ”för att öppna Windows Service Manager).

### <a name="error-unabletoconnecttoserver"></a>Felet UnableToConnectToServer

Det gick inte att ansluta till vCenter-servern ”Servername.com:9443” på grund av följande fel: Det fanns inte någon slutpunkt som lyssnade på https://Servername.com:9443/sdk som kunde acceptera meddelandet.

Kontrollera om du kör den senaste versionen av insamlingsprogrammet, om inte, uppgradera installationen till den [senaste versionen](https://docs.microsoft.com/azure/migrate/concepts-collector#how-to-upgrade-collector).

Om problemet inträffar fortfarande i den senaste versionen kan bero det insamlardatorn är inte matcha namnet på vCenter-servern har angetts eller porten som anges är fel. Om porten inte anges försöker insamlaren som standard att ansluta till portnumret 443.

1. Försök att pinga Servername.com från insamlardatorn.
2. Om steg 1 misslyckas kan du försöka ansluta till vCenter-servern via en IP-adress.
3. Identifiera rätt portnummer för att ansluta till vCenter.
4. Kontrollera slutligen om vCenter-servern är igång.

## <a name="troubleshoot-dependency-visualization-issues"></a>Felsöka problem med beroende-visualisering

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Jag har installerat Microsoft Monitoring Agent (MMA) och beroendeagenten på min lokala virtuella datorer, men beroenden som nu visas i Azure Migrate-portalen.

När du har installerat agenterna tar Azure Migrate normalt 15 – 30 minuter att visa beroenden i portalen. Om du har väntat i mer än 30 minuter, se till att MMA-agenten kan kommunicera med OMS-arbetsytan genom att följa de stegen nedan:

För Windows VM:
1. Gå till **Kontrollpanelen** och starta **Microsoft Monitoring Agent**
2. Gå till den **Azure Log Analytics (OMS)** i popup-MMA egenskaperna
3. Se till att den **Status** för arbetsytan är grönt.
4. Om statusen inte är grön, försök att ta bort arbetsytan och lägga till den igen till MMA.
        ![MMA-Status](./media/troubleshooting-general/mma-status.png)

Se till att installationskommandon för MMA och beroendeagenter agent hade har utförts för Linux VM.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Vilka är de operativsystem som stöds av MMA?

Lista över Windows-operativsystem som stöds av MMA är [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Listan med Linux-operativsystem som stöds av MMA är [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Vilka är de operativsystem som stöds av beroendeagenten?

Lista över Windows-operativsystem som stöds av beroendeagenten är [här](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Listan med Linux-operativsystem som stöds av beroendeagenten är [här](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Jag kan inte visualisera beroenden i Azure Migrate för mer än en timme?
Azure Migrate kan du visualisera beroenden för varaktighet för upp till en timme. Även om Azure Migrate kan du gå tillbaka till ett visst datum i historiken för upp till senaste månaden, är maximal varaktighet för vilken du kan visualisera beroenden upp till 1 timme. Exempel: du kan använda funktionen tid varaktighet på beroendekartan visar beroenden för igår, men kan bara visa för ett fönster med en timme.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Jag kan inte visualisera beroenden för grupper med fler än 10 virtuella datorer?
Du kan [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) som har upp till 10 virtuella datorer, om du har en grupp med fler än 10 virtuella datorer, rekommenderar vi att dela upp gruppen i mindre grupper och visualisera beroenden.

## <a name="troubleshoot-readiness-issues"></a>Felsöka beredskapsproblem

**Problemet** | **Åtgärda**
--- | ---
Starttypen stöds inte | Azure stöder inte virtuella datorer med EFI-starttyp. Vi rekommenderar att konvertera starttyp till BIOS innan du kör en migrering. <br/><br/>Du kan använda [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) göra migrering av sådana virtuella datorer som den konverterar starttyp för den virtuella datorn till BIOS under migreringen.
Villkorligt stött Windows OS | Operativsystemet har upphört att supportperioden och behöver en anpassad stöder avtal (CSA) för [stöd i Azure](https://aka.ms/WSosstatement), Överväg att uppgradera Operativsystemet innan du migrerar till Azure.
Windows-operativsystem som inte stöds | Azure stöder endast [Windows OS-versioner har valt](https://aka.ms/WSosstatement), Överväg att uppgradera Operativsystemet på datorn innan du migrerar till Azure.
Villkorligt godkänt Linux OS | Azure godkänner endast [valt Linux OS-versioner](../virtual-machines/linux/endorsed-distros.md), Överväg att uppgradera Operativsystemet på datorn innan du migrerar till Azure.
Linux-OS som inte stöds | Datorn kan starta i Azure, men någon OS-support tillhandahålls av Azure bör du överväga att uppgradera datorns operativsystem till en [godkända Linux-versionen](../virtual-machines/linux/endorsed-distros.md) innan du migrerar till Azure
Okänt operativsystem | Operativsystemet på den virtuella datorn angavs som ”annan” i vCenter Server, vilket Azure Migrate inte identifiera Azure-beredskap för den virtuella datorn. Se till att Operativsystemet som körs på datorn är [stöds](https://aka.ms/azureoslist) av Azure innan du migrerar datorn.
OS-bitar som inte stöds | Virtuella datorer med 32-bitars operativsystem kan starta i Azure, men det rekommenderas att uppgradera Operativsystemet på den virtuella datorn från 32-bitars till 64-bitars innan du migrerar till Azure.
Kräver Visual Studio-prenumeration. | Datorerna som har en Windows-klient OS som körs inuti sig. som stöds bara i Visual Studio-prenumeration.
Den virtuella datorn hittades inte för nödvändiga lagringsprestanda. | Lagringsprestanda (IOPS/dataflödet) krävs för datorn överskrider support för Azure virtuella datorer. Minska utrymmeskraven för datorn före migreringen.
Den virtuella datorn hittades inte för nödvändig nätverksprestanda. | Nätverkets prestanda (in/ut) krävs för datorn överskrider support för Azure virtuella datorer. Minska nätverkskrav för datorn.
Den virtuella datorn hittades inte i angivna prisnivån. | Överväg att minska VM-storleken innan du migrerar till Azure om prisnivån har angetts till Standard. Om den storlek nivån Basic kan du ändra utvärderingens prisnivå till Standard.
Den virtuella datorn hittades inte i den angivna platsen. | Använd en annan målplats före migreringen.
En eller flera olämpliga diskar. | En eller flera diskar som är anslutna till den virtuella datorn uppfyller inte kraven för Azure. För varje disk som är ansluten till den virtuella datorn, se till att storleken på disken är < 4 TB, om inte, minska storleken innan du migrerar till Azure. Se till att prestanda (IOPS/dataflödet) krävs för varje disk stöds av Azure [hanterade virtuella datordiskar](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
En eller flera oanvändbara nätverkskort. | Ta bort oanvända nätverkskort från datorn före migreringen.
Antalet diskar överskrider gränsen | Ta bort oanvända diskar från datorn före migreringen.
Diskstorleken överskrider gränsen | Azure har stöd för diskar med upp till 4 TB. Komprimera diskar till mindre än 4 TB innan migreringen.
Disken är inte tillgänglig på den angivna platsen | Kontrollera att disken är i målets plats innan du migrerar.
Disken är ej tillgänglig för den angivna redundansen | Disken bör använda redundans lagringstyp som definierats i utvärderingsinställningarna (LRS som standard).
Det gick inte att fastställa diskens lämplighet pga ett internt fel | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att hitta någon virtuell dator med nödvändiga kärnor och minne | Azure kunde inte funkar en lämplig typ av virtuell dator. Minska minnes- och antalet kärnor i den lokala datorn innan du migrerar.
Det gick inte att fastställa VM-lämpligheten pga ett internt fel. | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämpligheten för en eller flera diskar pga ett internt fel. | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämpligheten för en eller flera nätverkskort pga ett internt fel. | Försök att skapa en ny utvärdering för gruppen.


## <a name="collect-logs"></a>Samla in loggar

**Hur jag för att samla in loggar på den Virtuella insamlardatorn?**

Loggning är aktiverat som standard. Loggarna finns på följande sätt:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Om du vill samla in Event Tracing för Windows, gör du följande:

1. Öppna ett PowerShell-kommandofönster på Virtuella insamlardatorn.
2. Kör **Get-EventLog - loggnamn program | export-csv eventlog.csv**.

**Hur jag samla in portalen nätverk trafikloggar**

1. Öppna webbläsaren och navigera och logga in [till portalen](https://portal.azure.com).
2. Tryck på F12 för att starta Developer Tools. Om det behövs, avmarkerar du inställningen **raderar du i navigeringsfönstret**.
3. Klicka på den **nätverk** fliken och börja samla in nätverkstrafik:
 - Välj i Chrome, **Preserve log**. Inspelningen ska starta automatiskt. En röd cirkel anger att trafik avbildning. Om den inte visas klickar du på den svarta cirkeln att starta
 - I Edge/IE Starta inspelning automatiskt. Om det inte, klickar på gröna knappen Spela upp.
4. Försök att återskapa felet.
5. När det har inträffat ett fel uppstod när inspelning stoppa inspelningen och spara en kopia av registrerad aktivitet:
 - I Chrome, högerklicka och klicka på **Spara som HAR med innehåll**. Det här zips och exporterar loggarna som en .har-fil.
 - I Edge/IE, klickar du på den **Export avbildas trafik** ikon. Det här zips och exporterar loggen.
6. Navigera till den **konsolen** fliken för att söka efter eventuella varningar och fel. Spara konsolloggen:
 - I Chrome, högerklickar du någonstans i konsolloggen för. Välj **Spara som**, för att exportera och zip-loggen.
 - Högerklicka på fel och välj i Edge/IE **kopiera alla**.
7. Stäng Developer Tools.

## <a name="collector-error-codes-and-recommended-actions"></a>Insamlaren felkoder och rekommenderade åtgärder

| Felkod | Fel namn   | Meddelande   | Möjliga orsaker | Rekommenderad åtgärd  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | Insamlaren har upphört att gälla.                                                        | Insamlaren har upphört.                                                                                    | Hämta en ny version av insamlaren och försök igen.                                                                                      |
| 751       | UnableToConnectToServer        | Det gick inte att ansluta till vCenter Server %Name; på grund av felet: %ErrorMessage;     | Läs felmeddelandet om du vill ha mer information.                                                             | Åtgärda problemet och försök igen.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | Servern %Name; är inte en vCenter Server.                                  | Ange vCenter Server-information.                                                                       | Försök åtgärden igen med korrekt vCenter Server-information.                                                                                   |
| 753       | InvalidLoginCredentials        | Det gick inte att ansluta till vCenter Server %Name; på grund av felet %ErrorMessage; | Det gick inte att ansluta till vCenter Server eftersom autentiseringsuppgifterna för inloggning är ogiltiga.                             | Kontrollera att de autentiseringsuppgifter för inloggning som angetts stämmer.                                                                                    |
| 754       | NoPerfDataAvaialable           | Prestandadata är inte tillgängliga.                                               | Kontrollera statistiknivån i vCenter Server. Det ska ställas in som 3 om prestandadata ska vara tillgängliga. | Ändra statistiknivån till 3 (med 5 minuters, 30 minuters och 2 timmars varaktighet) och försök igen efter att ha väntat minst en dag.                   |
| 756       | NullInstanceUUID               | En dator med null InstanceUUID påträffades                                  | vCenter Server kan ha ett olämpligt objekt.                                                      | Åtgärda problemet och försök igen.                                                                                                           |
| 757       | VMNotFound                     | Det gick inte att hitta den virtuella datorn                                                  | Den viirtuella datorn kan tas bort: %VMID;                                                                | Se till att de virtuella datorer som valdes när vCenter-lagret omfångsdefinierades finns under identifieringen                                      |
| 758       | GetPerfDataTimeout             | VCenter-begäran uppnådde sin tidsgräns. Meddelande % Message;                                  | vCenter Server-autentiseringsuppgifterna är felaktiga                                                              | Kontrollera autentiseringsuppgifter för vCenter Server och se till att vCenter Server kan nås. Försök igen. Kontakta supporten om problemet kvarstår. |
| 759       | VmwareDllNotFound              | Det gick inte att hitta VMWare.Vim-DLL:en.                                                     | PowerCLI har inte installerats korrekt.                                                                   | Kontrollera om PowerCLI har installerats korrekt. Försök igen. Kontakta supporten om problemet kvarstår.                               |
| 800       | ServiceError                   | Tjänsten Azure Migrate Collector körs inte.                               | Tjänsten Azure Migrate Collector körs inte.                                                       | Använd services.msc om du vill starta tjänsten och försök igen.                                                                             |
| 801       | PowerCLIError                  | VMware PowerCLI-installationen misslyckades.                                          | VMware PowerCLI-installationen misslyckades.                                                                  | Försök igen. Om problemet kvarstår kan du installera det manuellt och försök igen.                                                   |
| 802       | TimeSyncError                  | Tiden har inte synkroniserats med Internet-tidsservern.                            | Tiden har inte synkroniserats med Internet-tidsservern.                                                    | Se till att tiden på datorn är korrekt inställd för datorns tidszon och försök igen.                                 |
| 702       | OMSInvalidProjectKey           | En ogiltig projektnyckel har angetts.                                                | En ogiltig projektnyckel har angetts.                                                                        | Gör om åtgärden med korrekt projektnyckel.                                                                                              |
| 703       | OMSHttpRequestException        | Fel uppstod när begäran skickas. Meddelande % Message;                                | Kontrollera projekt-ID och nyckel och se till att slutpunkten kan nås.                                       | Försök igen. Kontakta Microsoft-supporten om problemet kvarstår.                                                                     |
| 704       | OMSHttpRequestTimeoutException | HTTP-begäran uppnådde sin tidsgräns. Meddelande % Message;                                     | Kontrollera projekt-ID och nyckel och se till att slutpunkten kan nås.                                       | Försök igen. Kontakta Microsoft-supporten om problemet kvarstår.                                                                     |
