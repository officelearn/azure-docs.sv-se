---
title: Funktioner för operativsystemet
description: Lär dig mer om OS-funktionerna i Azure App Service på Windows. Ta reda på vilka typer av fil-, nätverks- och registeråtkomst som appen får.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: ed84cb2b0cb8d98b12fe787e49c400ba47e4e38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671614"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Operativsystemfunktioner på Azure App Service
I den här artikeln beskrivs de vanliga funktioner för grundläggande operativsystem som är tillgängliga för alla Windows-appar som körs på [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Den här funktionen omfattar fil-, nätverks- och registeråtkomst samt diagnostikloggar och händelser. 

> [!NOTE] 
> [Linux-appar](containers/app-service-linux-intro.md) i App Service körs i sina egna behållare. Ingen åtkomst till värdoperativsystemet är tillåtet, du har root-åtkomst till behållaren. På samma sätt har du administrativ åtkomst till behållaren för [appar som körs i Windows-behållare,](app-service-web-get-started-windows-container.md)men ingen åtkomst till värdoperativsystemet. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Nivåer för apptjänstplan
App Service kör kundappar i en värdmiljö för flera innehavare. Appar som distribueras på **nivåerna Kostnadsfri** och **Delad** körs i arbetsprocesser på delade virtuella datorer, medan appar som distribueras på **standard-** och **premiumnivåerna** körs på virtuella datorer som är särskilt avsedda för appar som är associerade med en enskild kund.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Eftersom App Service stöder en sömlös skalningsupplevelse mellan olika nivåer förblir säkerhetskonfigurationen för App Service-appar densamma. Detta säkerställer att appar inte plötsligt beter sig annorlunda, misslyckas på oväntade sätt, när App Service-abonnemanget växlar från en nivå till en annan.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Utvecklingsramar
App Service-prisnivåer styr mängden beräkningsresurser (CPU, disklagring, minne och nätverksutgående) som är tillgängliga för appar. Bredden på ramfunktionen som är tillgänglig för appar förblir dock densamma oavsett skalningsnivåer.

App Service stöder en mängd olika utvecklingsramverk, inklusive ASP.NET, klassisk ASP, node.js, PHP och Python - som alla körs som tillägg inom IIS. För att förenkla och normalisera säkerhetskonfigurationen kör App Service-appar vanligtvis de olika utvecklingsramverken med sina standardinställningar. En metod för att konfigurera appar kan ha varit att anpassa API-ytan och funktionaliteten för varje enskilt utvecklingsramverk. App Service tar istället en mer allmän metod genom att aktivera en gemensam baslinje för operativsystemfunktioner oavsett en apps utvecklingsramverk.

I följande avsnitt sammanfattas de allmänna typerna av operativsystemfunktioner som är tillgängliga för App Service-appar.

<a id="FileAccess"></a>

## <a name="file-access"></a>Tillgång till fil
Det finns olika enheter inom App Service, inklusive lokala enheter och nätverksenheter.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Lokala enheter
I grunden är App Service en tjänst som körs ovanpå Azure PaaS-infrastrukturen (plattform som tjänst). Därför är de lokala enheter som är "kopplade" till en virtuell dator samma enhetstyper som är tillgängliga för alla arbetarroller som körs i Azure. Det här omfattar:

- En operativsystemenhet (D:\ enhet)
- En programenhet som innehåller Cspkg-filer för Azure Package som används uteslutande av App Service (och som inte är tillgängliga för kunder)
- En "användare"-enhet (C:\ enhet), vars storlek varierar beroende på storleken på den virtuella datorn. 

Det är viktigt att övervaka diskanvändningen när programmet växer. Om diskkvoten har uppnåtts kan den ha negativa effekter för ditt program. Ett exempel: 

- Appen kan utlösa ett fel som anger att det inte finns tillräckligt med utrymme på disken.
- Du kan se diskfel när du bläddrar till Kudu-konsolen.
- Distribution från Azure DevOps eller `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`Visual Studio kan misslyckas med .
- Din app kan få långsam prestanda.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Nätverksenheter (även om UNC-resurser)
En av de unika aspekterna av App Service som gör appdistribution och underhåll enkelt är att allt användarinnehåll lagras på en uppsättning UNC-resurser. Den här modellen mappar väl till det gemensamma mönstret för innehållslagring som används av lokala webbhotell miljöer som har flera belastningsbalanserade servrar. 

Inom App Service skapas ett antal UNC-resurser i varje datacenter. En procentandel av användarinnehållet för alla kunder i varje datacenter allokeras till varje UNC-resurs. Dessutom placeras allt filinnehåll för en enskild kunds prenumeration alltid på samma UNC-resurs. 

På grund av hur Azure-tjänster fungerar ändras den specifika virtuella datorn som är ansvarig för en UNC-resurs med tiden. Det är garanterat att UNC-resurser kommer att monteras av olika virtuella datorer när de tas upp och ner under den normala azure-verksamheten. Därför bör appar aldrig göra hårdkodade antaganden om att maskininformationen i en UNC-filsökväg förblir stabil med tiden. I stället bör de använda den praktiska *faux* absoluta sökvägen **D:\home\site** som App Service tillhandahåller. Den här absoluta faux-sökvägen ger en bärbar, app-och-användare-agnostisk metod för att referera till sin egen app. Genom att använda **D:\home\site**kan man överföra delade filer från app till app utan att behöva konfigurera en ny absolut sökväg för varje överföring.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typer av filåtkomst som beviljas till en app
Varje kunds prenumeration har en reserverad katalogstruktur på en specifik UNC-resurs i ett datacenter. En kund kan ha flera appar som skapats i ett visst datacenter, så alla kataloger som tillhör en enda kundprenumeration skapas på samma UNC-resurs. Resursen kan innehålla kataloger som de för innehåll, fel och diagnostikloggar och tidigare versioner av appen som skapats av källkontrollen. Som förväntat är en kunds appkataloger tillgängliga för läs- och skrivåtkomst vid körning av appens programkod.

På de lokala enheter som är anslutna till den virtuella datorn som kör en app reserverar App Service ett utrymme på C:\ enhet för appspecifik tillfällig lokal lagring. Även om en app har fullständig läs-/skrivåtkomst till sin egen tillfälliga lokala lagring, är det lagringsutrymmet verkligen inte avsett att användas direkt av programkoden. Avsikten är snarare att tillhandahålla tillfällig fillagring för IIS- och webbprogramramverk. App Service begränsar också mängden tillfällig lokal lagring som är tillgänglig för varje app för att förhindra att enskilda appar förbrukar stora mängder lokal fillagring.

Två exempel på hur App Service använder tillfällig lokal lagring är katalogen för tillfälliga ASP.NET filer och katalogen för IIS-komprimerade filer. Sammanställningssystemet ASP.NET använder katalogen "Tillfällig ASP.NET-filer" som en tillfällig samlingscacheplats. IIS använder katalogen "IIS Temporary Compressed Files" för att lagra komprimerad svarsutdata. Båda dessa typer av filanvändning (liksom andra) mappas om i App Service till tillfällig lokal lagring per app. Den här ommappningen säkerställer att funktionaliteten fortsätter som förväntat.

Varje app i App Service körs som en slumpmässig unik arbetarprocessidentitet med låg [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities)behörighet som kallas "application pool identity", som beskrivs närmare här: . Programkod använder den här identiteten för grundläggande skrivskyddad åtkomst till operativsystemenheten (D:\ enheten). Detta innebär att programkod kan lista vanliga katalogstrukturer och läsa vanliga filer på operativsystemenheten. Även om detta kan tyckas vara en något bred åtkomstnivå, är samma kataloger och filer tillgängliga när du etablerar en arbetsroll i en Azure-värdtjänst och läser enhetens innehåll. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Filåtkomst över flera instanser
Arbetskatalogen innehåller en apps innehåll och programkoden kan skriva till den. Om en app körs på flera instanser delas arbetskatalogen mellan alla instanser så att alla instanser ser samma katalog. Så, till exempel, om en app sparar uppladdade filer till arbetskatalogen, är dessa filer omedelbart tillgängliga för alla instanser. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Nätverksåtkomst
Programkod kan använda TCP/IP- och UDP-baserade protokoll för att upprätta utgående nätverksanslutningar till Internet-tillgängliga slutpunkter som exponerar externa tjänster. Appar kan använda samma protokoll för att ansluta till tjänster inom Azure, till exempel genom att upprätta HTTPS-anslutningar till SQL Database.

Det finns också en begränsad möjlighet för appar att upprätta en accessloopback-anslutning och ha en appavlyssning på den accessloopback-socketen. Den här funktionen finns främst för att aktivera appar som lyssnar på accessback-sockets som en del av deras funktionalitet. Varje app ser en "privat" loopback-anslutning. App "A" kan inte lyssna på en accessloopback socket som upprättats av appen "B".

Namngivna rör stöds också som en inter-process kommunikation (IPC) mekanism mellan olika processer som kollektivt kör en app. IIS FastCGI-modulen är till exempel beroende av namngivna rör för att samordna de enskilda processer som kör PHP-sidor.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Kodkörning, processer och minne
Som nämnts tidigare körs appar inuti arbetarprocesser med låg behörighet med hjälp av en slumpmässig programpoolidentitet. Programkoden har åtkomst till minnesutrymmet som är associerat med arbetsprocessen, liksom alla underordnade processer som kan skapas av CGI-processer eller andra program. En app kan dock inte komma åt minne eller data i en annan app även om den finns på samma virtuella dator.

Appar kan köra skript eller sidor som skrivits med ramar för webbutveckling som stöds. App Service konfigurerar inga inställningar för webbramverk till fler begränsade lägen. Till exempel ASP.NET appar som körs på App Service körs i "fullt" förtroende i motsats till ett mer begränsat förtroendeläge. Webbramverk, inklusive både klassiska ASP och ASP.NET, kan anropa COM-komponenter (men inte ur processen COM-komponenter) som ADO (ActiveX Data Objects) som är registrerade som standard i Operativsystemet Windows.

Appar kan leka och köra godtycklig kod. Det är tillåtet för en app att göra saker som att leka ett kommandoskal eller köra ett PowerShell-skript. Även om godtycklig kod och processer kan skapas från en app är körbara program och skript fortfarande begränsade till de privilegier som beviljas den överordnade programpoolen. En app kan till exempel skapa en körbar som gör ett utgående HTTP-anrop, men samma körbara fil kan inte försöka ta bort IP-adressen för en virtuell dator från nätverkskortet. Att ringa ett utgående nätverksanrop är tillåtet för kod med låg behörighet, men om du försöker konfigurera om nätverksinställningarna på en virtuell dator krävs administratörsbehörighet.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostikloggar och händelser
Logginformation är en annan uppsättning data som vissa appar försöker komma åt. De typer av logginformation som är tillgänglig för kod som körs i App Service innehåller diagnostik- och logginformation som genereras av en app som också är lättillgänglig för appen. 

W3C HTTP-loggar som genereras av en aktiv app är till exempel tillgängliga antingen i en loggkatalog på nätverksresursplatsen som skapats för appen eller i blob-lagring om en kund har konfigurerat W3C-loggning till lagring. Det senare alternativet gör det möjligt att samla in stora mängder loggar utan risk för att överskrida de fillagringsgränser som är associerade med en nätverksresurs.

På liknande sätt kan diagnostikinformation i realtid från .NET-appar också loggas med hjälp av .NET-infrastrukturen för spårning och diagnostik, med alternativ för att skriva spårningsinformationen till antingen appens nätverksresurs eller alternativt till en blob-lagringsplats.

Områden med diagnostikloggning och spårning som inte är tillgängliga för appar är Windows ETW-händelser och vanliga Windows-händelseloggar (till exempel system-, program- och säkerhetshändelseloggar). Eftersom ETW-spårningsinformation potentiellt kan visas maskinomfattande (med rätt ACL) blockeras läs- och skrivåtkomst till ETW-händelser. Utvecklare kanske märker att API-anrop för att läsa och skriva ETW-händelser och vanliga Windows-händelseloggar verkar fungera, men det beror på att App Service "fejkar" anropen så att de verkar lyckas. I verkligheten har programkoden ingen åtkomst till dessa händelsedata.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Tillgång till register
Appar har skrivskyddad åtkomst till mycket (men inte alla) i registret för den virtuella datorn som de körs på. I praktiken innebär det att registernycklar som tillåter skrivskyddad åtkomst till den lokala användargruppen är tillgängliga via appar. Ett område i registret som för närvarande inte stöds för antingen\_\_läs- eller skrivåtkomst är HKEY CURRENT USER hive.

Skrivåtkomsten till registret är blockerad, inklusive åtkomst till alla registernycklar per användare. Ur appens perspektiv bör skrivåtkomst till registret aldrig åberopas i Azure-miljön eftersom appar kan (och gör) migreras över olika virtuella datorer. Det enda beständiga skrivbara lagringsutrymme som kan bero på av en app är innehållskatalogstrukturen per app som lagras på UNC-apptjänstens resurser. 

## <a name="remote-desktop-access"></a>Åtkomst till fjärrskrivbord

App Service ger inte fjärrskrivbordsåtkomst till VM-instanserna.

## <a name="more-information"></a>Mer information

[Begränsat till Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – Den senaste informationen om körningsmiljön för App Service. Den här sidan underhålls direkt av apptjänstutvecklingsteamet.

