---
title: Funktioner för operativsystemet på Azure App Service
description: Lär dig mer om den OS-funktionen som är tillgängliga för webbappar, serverdelar för mobilappar och API apps i Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: cephalin
ms.openlocfilehash: 9b4c87bd0889718fcb8938a9e3bb9207b8fe727a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420464"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funktioner för operativsystemet på Azure App Service
Den här artikeln beskriver de funktioner för vanliga baslinje operativsystemet som är tillgänglig för alla Windows-appar som körs på [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Den här funktionen innehåller filen, nätverk, och åtkomst till behållarregistret, och diagnostikloggar och händelser. 

> [!NOTE] 
> [Linux-appar](containers/app-service-linux-intro.md) i App Service som körs i sin egen behållare. Ingen åtkomst till värdoperativsystemet tillåts, du har rotåtkomst till behållaren. På så sätt, [program som körs på Windows-behållare](app-service-web-get-started-windows-container.md), du har administrativ åtkomst till behållaren men ingen åtkomst till värdoperativsystemet. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Nivåer för App Service-plan
App Service körs kundappar i en värdmiljö för flera innehavare. Appar som distribuerats i den **kostnadsfri** och **delad** nivåerna kör i arbetsprocesser på delade virtuella datorer, medan appar distribuerade i de **Standard** och **Premium**  nivåer som körs på virtuella datorer som är avsedda specifikt för appar som är associerade med en enda kund.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Eftersom App Service har stöd för en sömlös skalning upplevelse mellan olika nivåer, förblir densamma säkerhetskonfiguration som tillämpas för App Service-appar. Detta garanterar att appar inte plötsligt fungerar annorlunda, misslyckas på oväntade sätt när App Service-plan som växlar från en nivå till en annan.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Ramverken för programutveckling
App Service prisnivåer styra hur mycket av beräkningsresurser (CPU, disk storage, minne och utgående nätverkstrafik) tillgängliga för appar. Bredden på framework-funktioner som är tillgängliga för appar förblir detsamma oavsett skala nivåer.

App Service stöder en mängd olika ramverken för programutveckling, inklusive ASP.NET, klassisk ASP, node.js, PHP och Python – alla körs som tillägg i IIS. För att förenkla och normalisera säkerhetskonfiguration använder köra App Service-appar vanligtvis olika ramverken för programutveckling med standardinställningarna. En metod för att konfigurera appar kan bero på att anpassa API-ytan och funktioner för varje enskild utvecklingsramverk. App Service använder i stället en mer allmän metod genom att aktivera en gemensam baslinje för funktioner för operativsystemet, oavsett utvecklingsramverk för en app.

Följande avsnitt sammanfattar de allmänna typerna av funktioner för operativsystemet som är tillgängliga för App Service-appar.

<a id="FileAccess"></a>

## <a name="file-access"></a>Åtkomst till filen
Olika enheter finns i App Service, inklusive lokala enheter och nätverksenheter.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Lokala enheter
I grunden är är App Service en tjänst som körs på Azure PaaS (plattform som en tjänst)-infrastruktur. Därför kan är lokala enheter som är ”kopplade” till en virtuell dator de samma enhet typerna som är tillgängliga för alla worker-roll som körs i Azure. Det här omfattar:

- En operativsystemenhet (D:\-enhet)
- En enhet för program som innehåller Azure cspkg Paketfiler som används uteslutande av App Service (och otillgängligt för kunder)
- En ”användare”-enhet (C:\ enheten) vars storlek varierar beroende på storleken på den virtuella datorn. 

Det är viktigt att övervaka din diskanvändning när programmet växer. Om diskkvoten har nåtts kan det ha negativa effekter i ditt program.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Nätverksenhet (även kallat UNC delar)
En av de unika aspekterna av App Service som gör app-distribution och underhåll enkelt är att alla användarinnehåll lagras på en uppsättning UNC-resurser. Den här modellen mappar för det vanliga mönstret för innehållslagring som används av den lokala värdmiljöer som har flera servrar för Utjämning av nätverksbelastning. 

Det finns ett antal UNC-resurser som skapats i varje datacenter i App Service. En del av användarinnehåll för alla kunder i alla Datacenter har allokerats till varje UNC-resurs. Dessutom dela alla filen innehåll för en enskild kundprenumeration alltid placeras på samma UNC. 

På grund av hur Azure fungerar, kommer den specifika virtuella datorn som är ansvarig för som är värd för en UNC-resurs ändras med tiden. Det är säkert att UNC-resurser ska monteras av olika virtuella datorer som de förs upp och ned under normala driften av Azure. Därför bör apps aldrig är hårdkodat antaganden att informationen om datorn på en UNC-filsökväg förblir stabil över tid. I stället de ska använda den praktiskt *faux* absolut sökväg **D:\home\site** som App Service tillhandahåller. Den här faux absolut sökväg ger en bärbar, agnostiskt för app-användare metod för att referera till det egna app. Med hjälp av **D:\home\site**, en kan överföra delade filer från appen utan att behöva konfigurera en ny absolut sökväg för varje överföring.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typer av filen som har åtkomst till en app
Varje kunds prenumeration har en reserverad katalogstruktur på en specifik UNC-resurs i ett datacenter. En kund kan ha flera appar som har skapats i en specifik datacenter, så att alla kataloger som hör till en enskild kundprenumeration skapas på samma UNC dela. Resursen kan omfatta kataloger till exempel för innehåll, fel och diagnostikloggar och tidigare versioner av appen som skapats av källkontroll. Som förväntat, är en kunds app kataloger tillgängliga för Läs- och skrivåtkomst vid körning av appens programkod.

På de lokala enheter som är kopplade till den virtuella datorn som kör en app, reserverar App Service ett segment utrymme på enhet C:\ för appspecifika temporär lokal lagring. Även om en app har fullständig läs-/ skrivåtkomst till sin egen tillfällig lokal lagring, är inte lagringssystemet verkligen avsedd att användas direkt av programkoden. Avsikten är snarare att tillhandahålla tillfällig lagring för IIS och web ramverk för programmet. App Service också begränsar det temporära lokala lagringen i varje app för att förhindra att enskilda appar förbrukar alltför stora mängder lokal fillagring.

Två exempel på hur App Service använder lokal tillfällig lagring är katalogen för temporära ASP.NET-filer och katalogen för IIS komprimerade filer. ASP.NET används-kompilering katalogen ”tillfälliga ASP.NET-filer som en tillfällig kompilering cacheplats. IIS använder katalogen ”IIS temporära komprimerade filer” för att lagra komprimerade svarsutdata. Båda typerna av filen användning (samt andra) mappas i App Service till per app temporär lokal lagring. Den här ommappning säkerställer att funktionerna kan fortsätta som förväntat.

Varje app i App Service körs som ett slumpmässigt unika med låg behörighet arbetsprocess-ID kallas ”programpoolsidentiteten”, som beskrivs mer här: [ http://www.iis.net/learn/manage/configuring-security/application-pool-identities ](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Koden använder den här identiteten för grundläggande skrivskyddad åtkomst till enheten med operativsystemet (D:\ enheten). Det innebär att programkoden kan visa en lista över vanliga katalogstrukturer och läsa vanliga filer på enheten med operativsystemet. Även om det verka som att något generellt åtkomst, i samma kataloger och filer är tillgängliga när du etablerar en arbetsroll i en Azure värdtjänsten och läsa innehållet i enheten. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Åtkomst till filen över flera instanser
Arbetskatalogen innehåller en Apps innehåll och programkoden kan skriva till den. Om en app körs på flera instanser, delas arbetskatalogen mellan alla instanser så att alla instanser finns i samma katalog. Så, till exempel om en app sparar uppladdade filer till arbetskatalogen, dessa filer är omedelbart tillgängliga för alla instanser. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Nätverksåtkomst
Programkoden kan använda TCP/IP- och UDP-baserade protokoll för att göra utgående nätverksanslutningar till Internet tillgängliga slutpunkter som exponerar externa tjänster. Appar kan använda dessa samma protokoll för att ansluta till tjänster i Azure&#151;exempelvis genom att upprätta HTTPS-anslutningar till SQL-databas.

Det finns också en begränsad kapacitet för appar för att upprätta en anslutning för lokal loopback och har en app som lyssnar på den lokala loopback-socketen. Den här funktionen finns främst för att aktivera appar som lyssnar på lokal loopback sockets som en del av deras funktioner. Varje app ser en ”privat” loopback-anslutning. App ”A” Det går inte att lyssna på en lokal loopback socket som upprättats av app ”B”.

Namngivna pipes stöds också som en mekanism för kommunikation mellan processer (IPC) mellan olika processer som gemensamt kör en app. Till exempel använder IIS FastCGI-modulen namngivna pipes att samordna de enskilda processer som körs PHP-sidor.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Fjärrkörning av kod, processer och minne
Som tidigare nämnts körs apparna i med låg behörighet arbetsprocesser med en slumpmässig programpoolsidentiteten. Koden har åtkomst till minnesutrymme som är associerade med processen och alla underordnade processer som kan vara värdprocessen av CGI-processer eller andra program. Men en app kan inte komma åt samma minneskapacitet eller data i en annan app även om den finns på samma virtuella dator.

Appar kan köra skript eller sidor som hämtas med stöds webbutvecklingsramverk. App Service Konfigurera inte några inställningar för framework för mer begränsad. ASP.NET-appar som körs i App Service körs till exempel ”full” betrott istället för ett mer begränsat förtroende-läge. Webbramverk, inklusive både klassiska ASP och ASP.NET, kan anropa pågående COM-komponenter (men inte ut av processen COM-komponenter) som ADO (ActiveX Data Objects) som är registrerade som standard på Windows-operativsystem.

Appar kan starta och köra godtycklig kod. Det är tillåten för en app att göra saker som spawn en kommandotolk eller köra ett PowerShell.skript. Även om skadlig kod och processer kan vara värdprocessen från en app, är körbara program och skript dock ändå begränsad till de behörigheter som beviljas till överordnade programpool. En app kan till exempel skapa en körbar fil som gör ett utgående HTTP-anrop, men att samma körbara det går inte att försöka ta bort bindningen för IP-adressen för en virtuell dator från dess nätverkskort. Att göra ett anrop om utgående nätverkstrafik får kod med låg behörighet, men försök att konfigurera nätverksinställningar på en virtuell dator kräver administrativa privilegier.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostikloggar och händelser
Logginformation är en annan uppsättning data som vissa appar försöker komma åt. Vilka typer av logginformation som är tillgängliga för kod som körs i App Service innehåller diagnostik och logga information som genererats av en app som också är lättillgänglig till appen. 

Till exempel är W3C HTTP-loggar som genereras av en aktiv app tillgängliga antingen på en loggningskatalog i nätverksresurs som skapats för appen, eller som är tillgängliga i blob storage om en kund har ställt in W3C loggning till lagring. Det senare alternativet gör det möjligt för stora mängder loggar samlas in utan att riskera att som överstiger de filen gränser som är associerade med en nätverksresurs.

I en liknande vein i realtid diagnostikinformation från .NET-program kan också vara inloggad med .NET-spårning och diagnostik infrastruktur, med alternativ för att skriva spårningsinformationen till antingen appens nätverksresurs, eller också till en blob-lagringsplats.

Diagnostik och spårning som inte är tillgängliga appar visas Windows ETW-händelser och vanliga Windows-händelseloggarna (till exempel, program, händelseloggarna System och säkerhet). Eftersom ETW-spårningsinformation kan vara visas datoromfattande (med rätten ACL: er), blockeras Läs- och skrivåtkomst till ETW-händelser. Utvecklare märker att API-anrop kan läsa och skriva ETW-händelser och vanliga Windows-händelseloggar verkar fungera, men det beror på att App Service ”faking” anrop så att de ser ut att lyckas. I själva fallet har ingen åtkomst till den här händelsedata i programkoden.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Registeråtkomst
Appar som har skrivskyddad åtkomst till många (även om inte alla) i registret på den virtuella datorn som de körs på. I praktiken innebär detta registernycklar för att tillåter skrivskyddad åtkomst till den lokala gruppen användare som är tillgängliga för appar. En del av registret som inte stöds för närvarande för Läs- eller skrivbehörighet är HKEY\_aktuella\_användaren hive.

Skrivåtkomst till registret är blockerad, inklusive åtkomst till några registernycklar för per användare. Från appens perspektiv skrivåtkomst till registret bör aldrig förlita sig på i Azure-miljön eftersom appar kan (och göra) migreras mellan olika virtuella datorer. Endast beständig skrivbart lagring som kan vara beroende av en app är per app innehåll katalogstrukturen lagras på App Service-UNC-resurser. 

## <a name="more-information"></a>Mer information

[Azure Web App-sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – den senaste informationen om körningsmiljö för App Service. Den här sidan hanteras direkt av App Service-Utvecklingsteamet.

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
> 
> 


