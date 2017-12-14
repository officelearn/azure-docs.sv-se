---
title: "Felsökning av problem med Azure migrera | Microsoft Docs"
description: "En översikt över kända problem i tjänsten Azure migrera och felsökningstips för vanliga fel."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 1fcc9e12e63eda73d53ae2085bc2a64d31ea2067
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="troubleshoot-azure-migrate"></a>Felsöka Azure Migrate

## <a name="troubleshoot-common-errors"></a>Felsök vanliga fel

[Azure migrera](migrate-overview.md) utvärderar lokala arbetsbelastningar för migrering till Azure. Använd den här artikeln för att felsöka problem när du distribuerar och använder Azure migrera.


**Insamlaren kan inte ansluta till internet**

Detta kan inträffa när den datorn som du använder är bakom en proxyserver. Kontrollera att du anger autentiseringsuppgifterna om proxyn måste en.
Om du använder alla URL-baserade brandväggen proxy för att styra utgående anslutning, att kontrollera listan över godkända följande obligatoriska URL: er:

**URL: EN** | **Syfte**  
--- | ---
*. portal.azure.com | Krävs för att kontrollera anslutningen till Azure-tjänsten och validera tidssynkronisering utfärdar.
*. oneget.org | Krävs för baserad att ladda ned powershell vCenter PowerCLI modul.

**Insamlaren kan inte ansluta till projektet med projekt-ID och nyckel I kopieras från portalen.**

Kontrollera att du har kopieras och klistras in rätt information. Felsök genom att installera Microsoft Monitoring Agent (MMA) på följande sätt:

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

**Problemet** | **Åtgärda**
--- | ---
Start-typ som inte stöds | Ändra BIOS innan du kör en migrering.
Diskantalet överskrider gränsen | Ta bort oanvända diskar på datorn innan migreringen.
Diskens storlek överskrider gränsen | Komprimera diskar till mindre än 4 TB innan migreringen. 
Disk som är tillgänglig på den angivna platsen | Kontrollera att disken är i din målplats innan du migrerar.
Disken är inte tillgänglig för den angivna redundansen | Disken bör använda redundans lagringstyp som definierats i assessment-inställningar (LRS som standard).
Kunde inte avgöra disk lämplighet på grund av ett internt fel | Försök att skapa en ny utvärdering för gruppen. 
Virtuell dator med nödvändiga kärnor och minne som inte hittades | Azure kunde inte finjustering lämplig VM typ. Minska minne och antalet kärnor på den lokala datorn innan du migrerar. 
En eller flera olämpliga diskar. | Se till att lokala diskar är 4 TB eller under innan du kör en migrering.
En eller flera olämpliga nätverkskort. | Ta bort oanvända nätverkskort på datorn innan migreringen.
Kunde inte fastställa VM lämplighet på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen. 
Kunde inte fastställa lämplighet för ett eller flera diskar på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen.
Det gick inte att fastställa lämplighet för ett eller flera nätverkskort på grund av ett internt fel. | Försök att skapa en ny utvärdering för gruppen.
VM hittades inte för den obligatoriska lagringsprestanda. | Lagringsprestanda (IOPS/dataflöde) krävs för datorn överskrider Virtuella Azure-supporten. Minska utrymmeskraven för datorn innan migreringen.
VM hittades för obligatoriskt nätverkets prestanda. | Nätverkets prestanda (in/ut) krävs för datorn överskrider Virtuella Azure-supporten. Minska nätverkskrav för datorn. 
VM hittades inte för den angivna prisnivån. | Kontrollera inställningarna för prisnivå nivå. 
VM hittades inte i den angivna platsen. | Använd en annan målplats innan migreringen.
Problem med stöd för Operativsystemet Linux | Kontrollera att du arbetar med 64-bitars med dessa stöds [operativsystem](../virtual-machines/linux/endorsed-distros.md).
Windows-Operativsystemet stöder problem | Kontrollera att du kör ett operativsystem som stöds. [Läs mer](concepts-assessment-calculation.md#azure-suitability-analysis)
Okänd operativsystem. | Kontrollera att det operativsystem som anges i vCenter är korrekt och upprepa processen för identifiering.
Kräver Visual Studio-prenumeration. | Windows-klientoperativsystem stöds bara på Visual Studio (MSDN)-prenumerationer.


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
 



