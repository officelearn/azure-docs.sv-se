---
title: "Felsökning av problem med Azure migrera | Microsoft Docs"
description: "En översikt över kända problem i tjänsten Azure migrera och felsökningstips för vanliga fel."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 02/21/2018
ms.author: raynew
ms.openlocfilehash: e1e7a1a57f780ef477379dfb1ceaead0c8654970
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-azure-migrate"></a>Felsöka Azure Migrate

## <a name="troubleshoot-common-errors"></a>Felsöka vanliga fel

[Azure migrera](migrate-overview.md) utvärderar lokala arbetsbelastningar för migrering till Azure. Använd den här artikeln för att felsöka problem när du distribuerar och använder Azure migrera.


**Insamlaren kan inte ansluta till internet**

Detta kan inträffa när den datorn som du använder är bakom en proxyserver. Kontrollera att du anger autentiseringsuppgifterna om proxyn måste en.
Om du använder alla URL-baserade brandväggen proxy för att styra utgående anslutning, att kontrollera listan över godkända följande obligatoriska URL: er:

**URL** | **Purpose**  
--- | ---
*.portal.azure.com | Krävs för att kontrollera anslutningen till Azure-tjänsten och validera tidssynkronisering utfärdar.
*.oneget.org | Krävs för baserad att ladda ned powershell vCenter PowerCLI modul.

**Insamlaren kan inte ansluta till projektet med projekt-ID och nyckel I kopieras från portalen.**

Kontrollera att du har kopieras och klistras in rätt information. Om du vill felsöka, installera Microsoft Monitoring Agent (MMA) och kontrollera om MMA kan ansluta till projektet på följande sätt:

1. På insamlaren VM, ladda ned den [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Dubbelklicka på den hämta filen om du vill starta installationen.
3. I installationsprogrammet, på den **Välkommen** klickar du på **nästa**. På den **licensvillkoren** klickar du på **jag accepterar** att acceptera licensvillkoren.
4. I **målmappen**, behålla eller ändra standardinstallationsmappen > **nästa**.
5. I **installationsalternativ för Agent**väljer **Azure logganalys (OMS)** > **nästa**.
6. Klicka på **Lägg till** att lägga till en ny logganalys-arbetsyta. Klistra in i projekt-ID och nyckel som du kopierade. Klicka sedan på **Nästa**.
7. Kontrollera att agenten kan ansluta till projektet. Om det inte går att kontrollera inställningarna. Om agenten kan ansluta men insamlaren inte kan kontakta Support.


**Fel 802: Jag får ett synkroniseringsfel datum och tid.**

Serverns klocka kanske out för synkronisering med den aktuella tiden med fler än fem minuter. Ändra tiden klockan på insamlaren så att det matchar den aktuella tiden på följande sätt:

1. Öppna en kommandotolk för administratörer på den virtuella datorn.
2. Kör w32tm /tz för att kontrollera tidszonen.
3. Kör w32tm/resync för att synkronisera tiden.

**Mina projekt-nyckel har ”==” symboler mot slutet. Dessa kodas till andra alfanumeriska tecken Collector. Förväntas detta?**

Ja, alla projekt nycklar slutar med ”==”. Insamlaren krypterar nyckeln projekt innan bearbetningen.

**Prestandadata för diskar och nätverk för nätverkskort visas som noll**

Detta kan inträffa om statistik anger nivån på vCenter server anges till mindre än tre. Nivå 3 eller högre, lagrar vCenter VM prestandahistorik för bearbetning, lagring och nätverk. För mindre än nivån tre lagras vCenter inte i lagring och nätverksdata, men CPU och minne data. I det här scenariot prestanda data visas som noll i Azure migrera och migrera Azure ger storlek rekommendation för diskar och nätverk baserat på metadata som samlas in från lokala datorer.

Om du vill aktivera insamling av prestandadata för disk- och ändra statistik inställningar till tre. Sedan vänta minst en dag för att identifiera din miljö och utvärdera den. 

**Jag installerade agenter och används beroende visualiseringen för att skapa grupper. Nu efter växling vid fel, datorerna visa ”installera agenten” åtgärd i stället för ”Visa beroenden”**
* Post planerad eller oplanerad växling vid fel, lokala datorer är avstängda och motsvarande datorer skapas i Azure. Dessa datorer få en annan MAC-adress. De kan hämta en annan IP-adress baserat på om användaren valde att behålla lokala IP-adress eller inte. Om både MAC och IP-adresser skiljer sig åt Azure migrera associeras inte lokala datorer med en Tjänstkarta beroendedata och frågar användaren att installera agenter i stället för att visa beroenden.
* Bokför testa redundans, lokala datorer är aktiverade som förväntat. Motsvarande datorer efter en redundansväxling i Azure få annan MAC-adress och kan hämta annan IP-adress. Om inte användaren blockerar utgående OMS-trafik från de datorerna, Azure migrera associeras inte lokala datorer med en Tjänstkarta beroendedata och frågar användaren att installera agenter i stället för att visa beroenden.


## <a name="troubleshoot-readiness-issues"></a>Felsökning av beredskapsproblem med

**Problemet** | **Fix**
--- | ---
Starttypen stöds inte | Azure stöder inte virtuella datorer med EFI-start. Det rekommenderas att konvertera typen Start till BIOS innan du kör en migrering. <br/><br/>Du kan använda [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) att göra migreringen av dessa virtuella datorer som den konverterar typen Start av den virtuella datorn till BIOS under migreringen.
Antalet diskar överskrider gränsen | Ta bort oanvända diskar på datorn innan migreringen.
Diskstorleken överskrider gränsen | Azure har stöd för diskar med upp till storleken 4 TB. Komprimera diskar till mindre än 4 TB innan migreringen. 
Disken är inte tillgänglig på den angivna platsen | Kontrollera att disken är i din målplats innan du migrerar.
Disken är ej tillgänglig för den angivna redundansen | Disken bör använda redundans lagringstyp som definierats i assessment-inställningar (LRS som standard).
Det gick inte att fastställa diskens lämplighet pga ett internt fel | Försök att skapa en ny utvärdering för gruppen. 
Det gick inte att hitta någon virtuell dator med nödvändiga kärnor och minne | Azure kunde inte finjustering lämplig VM typ. Minska minne och antalet kärnor på den lokala datorn innan du migrerar. 
En eller flera olämpliga diskar. | Se till att lokala diskar är 4 TB eller under innan du kör en migrering.
En eller flera olämpliga nätverkskort. | Ta bort oanvända nätverkskort på datorn innan migreringen.
Kunde inte fastställa VM lämplighet på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen. 
Kunde inte fastställa lämplighet för ett eller flera diskar på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för ett eller flera nätverkskort på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen.
VM hittades inte för den obligatoriska lagringsprestanda. | Lagringsprestanda (IOPS/dataflöde) krävs för datorn överskrider Virtuella Azure-supporten. Minska utrymmeskraven för datorn innan migreringen.
VM hittades för obligatoriskt nätverkets prestanda. | Nätverkets prestanda (in/ut) krävs för datorn överskrider Virtuella Azure-supporten. Minska nätverkskrav för datorn. 
VM hittades inte i angivna prisnivå. | Överväg att downsizing den virtuella datorn innan du migrerar till Azure om prisnivån är Standard. Om sizing nivån Basic kan du ändra prisnivån för bedömningen till Standard. 
VM hittades inte i den angivna platsen. | Använd en annan målplats innan migreringen.
Okänd operativsystem | Operativsystemet på den virtuella datorn har angetts som ”annan” i vCenter-servern på grund av som Azure migrera inte kan identifiera Azure beredskap för den virtuella datorn. Kontrollera att Operativsystemet som körs på datorn är [stöds](https://aka.ms/azureoslist) av Azure innan du migrerar datorn.
Villkorligt stödda Windows-operativsystem | Operativsystemet har passerat slutet av stöd för datum och behöver en anpassad stöder avtal (CSA) för [stöd i Azure](https://aka.ms/WSosstatement), bör du uppgradera Operativsystemet innan du migrerar till Azure.
Stöds inte av Windows OS | Azure har endast stöd för [valt Windows OS-versioner](https://aka.ms/WSosstatement), bör du uppgradera Operativsystemet på datorn innan du migrerar till Azure. 
Villkorligt godkända Operativsystemet Linux | Azure godkänner endast [valt Linux OS-versioner](../virtual-machines/linux/endorsed-distros.md), bör du uppgradera Operativsystemet på datorn innan du migrerar till Azure.
Unendorsed Linux OS | Datorn kan starta i Azure, men ingen OS-support tillhandahålls av Azure bör du överväga att uppgradera Operativsystemet till en [godkända Linux-version](../virtual-machines/linux/endorsed-distros.md) innan du migrerar till Azure
OS-bitar som inte stöds | Virtuella datorer med 32-bitars operativsystem kan starta i Azure, men det rekommenderas att uppgradera Operativsystemet på den virtuella datorn från 32-bitars till 64-bitars innan du migrerar till Azure.
Kräver Visual Studio-prenumeration. | Datorerna har Operativsystemet som körs i den som en Windows-klient stöds bara i Visual Studio-prenumeration.


## <a name="collect-logs"></a>Samla in loggar

**Hur samlar loggar på VM-insamlaren?**

Loggning är aktiverat som standard. Loggarna finns på följande sätt:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Om du vill samla in Event Tracing for Windows gör följande:

1. Öppna ett PowerShell-Kommandotolken på insamlaren VM.
2. Kör **Get-EventLog - loggnamn program | export-csv eventlog.csv**.

**Hur samlar portal nätverket trafikloggar?**

1. Öppna webbläsaren och navigera och logga in [till portalen](https://portal.azure.com).
2. Tryck på F12 för att starta utvecklingsverktygen. Om det behövs, avmarkera inställningen **Rensa posterna i navigeringen**.
3. Klicka på den **nätverk** fliken och börja samla in nätverkstrafik:
 - Välj i Chrome, **Preserve loggen**. Inspelningen ska starta automatiskt. En röd cirkel anger att trafik avbildning. Om den inte visas klickar du på den svarta cirkeln att starta
 - I Edge/IE ska spela in startas automatiskt. Om det inte, klickar på gröna knappen Spela upp.
4. Försök att återskapa felet.
5. När det har inträffat ett fel uppstod när registrering stoppa inspelningen och spara en kopia av aktiviteten inspelade:
 - I Chrome, högerklicka och klicka på **Spara som HAR med innehåll**. Detta sänds och exporterar loggarna som en .har-fil.
 - I Edge/Internet Explorer klickar du på den **Export avbildas trafik** ikon. Detta sänds och exporterar loggen.
6. Navigera till den **konsolen** att söka efter eventuella varningar eller fel. Spara konsolen loggen:
 - I Chrome, högerklicka någonstans i loggen för konsolen. Välj **Spara som**, för att exportera och zip-loggen.
 - Högerklicka på fel och välj i Edge/IE **kopiera alla**. 
7. Stäng utvecklingsverktyg.
 

## <a name="vcenter-errors"></a>vCenter-fel

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Fel UnhandledException internt fel uppstod: System.IO.FileNotFoundException

Detta är ett problem visas på insamlaren versioner än 1.0.9.5. Om du är på en insamlaren version 1.0.9.2 eller pre-GA-versioner som 1.0.8.59 kommer du att få det här problemet. Följ den [länka angivna här till forum för en detaljerad svaret](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[Uppgradera din insamlaren för att åtgärda problemet](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Fel UnableToConnectToServer

Det gick inte att ansluta till vCenter-servern ”Servername.com:9443” på grund av fel: det fanns inte någon slutpunkt som lyssnade på https://Servername.com:9443/sdk som kunde acceptera meddelandet.

Detta händer när insamlaren datorn kan inte matcha det angivna servernamnet vCenter eller port speficified är fel. Om porten inte anges försöker insamlaren att ansluta till portnumret 443.

1. Försök att pinga example.com från insamlaren-datorn.
2. Om steg 1 misslyckas, kan du försöka ansluta till vCenter-servern via IP-adress.
3. Identifiera rätt portnummer för att ansluta till vCenter.
4. Kontrollera slutligen om vCenter-servern är igång.
 

