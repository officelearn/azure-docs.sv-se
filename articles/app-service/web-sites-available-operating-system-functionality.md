---
title: Operativsystemet funktioner i Azure App Service
description: "Lär dig mer om OS-funktionerna till webbappar, mobilappsserverdelar och API apps i Azure App Service"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
ms.openlocfilehash: a5f022eca8f901388c9cf003f3320db1b9c49e6a
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Operativsystemet funktioner i Azure App Service
Den här artikeln beskrivs vanliga grundläggande operativsystem-funktioner som är tillgänglig för alla appar som körs på [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Den här funktionen innehåller filen, nätverket och till registret och diagnostik loggar och händelser. 

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App Service-plan nivåer
App Service kör kunden appar på en värd för flera innehavare. Appar som distribuerats i det **lediga** och **delade** nivåer kör i arbetsprocesser på delade virtuella datorer, medan appar som distribuerats i det **Standard** och **Premium**  nivåer som körs på virtuella datorer som är dedikerad för appar som är kopplad till en kund.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Eftersom Apptjänst har stöd för skalning smidigt mellan olika nivåer, förblir detsamma säkerhetskonfigurationen för Apptjänst-appar. Detta säkerställer att appar inte plötsligt fungerar annorlunda, misslyckas på oväntade sätt när App Service-plan som växlar från en nivå till en annan.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Utvecklingsramverk
Apptjänst prisnivåer styra hur mycket av beräkningsresurser (CPU, diskutrymme, minne och nätverk utgång) tillgänglig för appar. Bredden av framework-funktionerna till appar förblir detsamma oavsett skala nivåer.

Apptjänst har stöd för utvecklingsramverk, bland annat ASP.NET, klassisk ASP, node.js, PHP och Python - alla körs som tillägg i IIS. För att förenkla och normalisera säkerhetskonfiguration kör Apptjänst-appar vanligtvis utvecklingsramverk för olika med standardinställningarna. Ett sätt att konfigurera appar kan bero på att anpassa API-ytan och funktioner för varje enskild utvecklingsramverk. Apptjänst använder i stället en generisk metod genom att aktivera en gemensam baslinje för operativsystemet funktioner oavsett utvecklingsramverk för en app.

Följande avsnitt sammanfattar de allmänna typerna av funktioner i operativsystemet som visas för Apptjänst-appar.

<a id="FileAccess"></a>

## <a name="file-access"></a>Åtkomst till filen
Olika enheter finns i Apptjänst, bland annat lokala enheter och nätverksenheter.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Lokala enheter
Kärnan är Apptjänst en tjänst som körs på Azure PaaS (plattform som en tjänst)-infrastruktur. Därför är de lokala enheter som ”kopplas” till en virtuell dator på samma enhet typer som är tillgängliga för alla worker-rollen körs i Azure. Detta inkluderar en operativsystemenhet (D:\-enhet), en enhet för program som innehåller Azure cspkg paketfilerna används endast av App Service (och inte tillgängligt för kunder) och en ”användare” enhet (C:\ enheten) vars storlek varierar beroende på storleken på den virtuella datorn .

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Nätverksenhet (aka UNC delar)
En av unika aspekter av App Service som gör app-distribution och underhåll enkla är att alla användarinnehåll lagras på en uppsättning UNC-resurser. Den här modellen mappas mycket snyggt till det vanliga mönstret för lagring av innehåll används av lokala värdbaserade miljöer som har flera servrar för Utjämning av nätverksbelastning. 

I App Service finns antal UNC-resurser som har skapats i varje datacenter. En del av användarinnehåll för alla kunder i varje Datacenter är allokerade till varje UNC-resurs. Dessutom dela alla filen innehållet för en enskild kundens prenumeration alltid placeras på samma UNC. 

Observera att den specifika virtuella som ansvarar för en UNC-resurs på grund av hur cloud services arbete, ändrar över tid. Det är säkert att UNC-resurser ska monteras av olika virtuella datorer som de förs upp och ned under normala molnet operations. Därför bör aldrig appar Se hårdkodade antaganden att informationen för datorn på en UNC-sökväg till filen förblir stabil över tid. I stället de ska använda den praktiska *faux* absolut sökväg **D:\home\site** som App Service tillhandahåller. Den här faux absolut sökväg ger en bärbar, oberoende för app-användaren metod för att referera till det egna app. Med hjälp av **D:\home\site**, en kan överföra delade filer från en app till en app utan att behöva konfigurera en ny absolut sökväg för varje överföring.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typer av filen som har åtkomst till en app
Varje kund prenumeration har en reserverad katalogstruktur på en specifik UNC-resurs i ett datacenter. En kund kan ha flera appar som har skapats i en specifik datacenter, så alla kataloger som hör till en enskild kundprenumeration skapas på samma UNC delar. Resursen kan omfatta kataloger som de för innehåll, fel och diagnostikloggar och tidigare versioner av app som skapats av källkontroll. Som förväntat, finns en kund app kataloger för Läs- och skrivbehörighet vid körning av appens programkod.

På lokala enheter anslutna till den virtuella datorn som kör en app, reserverar Apptjänst en del av diskutrymme på enhet C:\ för app-specifik tillfälliga lokal lagring. Även om en app har fullständig läsning och skrivning åtkomst till sin egen tillfällig lokal lagring, är inte som lagring avsedd att användas direkt av programkoden. I stället är avsikten att tillhandahålla tillfällig lagring för IIS- och ramverk för programmet. Apptjänst begränsar mängden tillfälliga lokal lagring som är tillgängliga för varje app för att förhindra att enskilda appar från att konsumera alltför stora mängder lagring av lokala filer.

Två exempel på hur Apptjänst använder temporära lokal lagring är katalogen för temporära ASP.NET-filer och katalogen för IIS komprimerade filer. ASP.NET används-kompilering katalogen ”temporära ASP.NET-filer som en tillfällig kompilering cacheplats. IIS använder katalogen ”IIS tillfälliga komprimerade filer” för att lagra komprimerade svarsutdata. Båda typerna av filen användning (samt andra) mappas i App Service till per app tillfälliga lokal lagring. Den här ommappning säkerställer att funktioner fortfarande som förväntat.

Varje app i App Service körs som en slumpmässig unika lågprivilegierat arbetsprocessidentiteten kallas ”programpoolsidentiteten”, beskrivs mer här: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities ](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Programkod som använder den här identiteten för grundläggande skrivskyddad åtkomst till enheten med operativsystemet (D:\ enheten). Detta innebär att programkod kan visa en lista med vanliga katalogstrukturer och läsa vanliga filer på enheten med operativsystemet. Även om det verkar något generellt åtkomst, i samma kataloger och filer är tillgängliga när du etablerar en arbetsroll i en Azure värdtjänsten och läsa innehållet i enheten. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Åtkomst till filen på flera instanser
Arbetskatalogen innehåller en app innehåll och programkod kan skriva till den. Om en app som körs på flera instanser, delas arbetskatalogen med alla instanser så att alla instanser finns i samma katalog. Så, till exempel om en app sparar överförda filer till arbetskatalogen, dessa filer är omedelbart tillgängliga för alla instanser. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Nätverksåtkomst
Programkod kan använda TCP/IP och UDP-baserat protokoll för att utgående nätverksanslutningar till Internet tillgänglig slutpunkter som exponerar externa tjänster. Appar kan använda dessa samma protokoll för att ansluta till tjänster i Azure & #151, till exempel genom att etablera HTTPS-anslutningar till SQL-databas.

Det finns också en begränsad funktion för appar att upprätta en anslutning för lokal loopback och har en app som lyssnar på den lokala loopback-socketen. Den här funktionen finns i första hand för att aktivera appar som lyssnar på lokal loopback sockets som en del av deras funktioner. Observera att varje app ser en ”privat” loopback-anslutning. App ”A” kan inte lyssna på en lokal loopback socket som upprättas av app ”B”.

Namngivna pipes stöds också som en mekanism för kommunikation mellan processer (IPC) mellan olika processer som gemensamt kör en app. Till exempel använder IIS FastCGI-modulen namngivna pipes koordinera enskilda processer som körs PHP-sidor.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Kodkörning, processer och minne
Som tidigare nämnts kör appar inuti lågprivilegierat arbetsprocesser med en slumpmässig programpoolsidentiteten. Programkod som har åtkomst till minnesutrymme som är associerade med processen och alla underordnade processer som kan startas av CGI-processer eller andra program. Men en app kan inte komma åt minne eller data i en annan app även om det finns på samma virtuella dator.

Appar kan köra skript eller sidor som skrivits med stöds web utvecklingsramverk. Apptjänst Konfigurera inte några inställningar för framework för mer begränsad. Till exempel köra ASP.NET-appar som körs på Apptjänst i ”full” förtroende i stället för en mer begränsat förtroende. Web ramverk, inklusive både klassiska ASP och ASP.NET kan anropa pågående COM-komponenter (men inte out-of-process COM-komponenter) som ADO (ActiveX Data Objects) som är registrerade som standard på Windows-operativsystemet.

Appar kan starta och köra skadlig kod. Det är tillåtet för en app att göra sådant som åtgärder en kommandotolk eller köra ett PowerShell.skript. Även om skadlig kod och processer kan skapas från en app, är körbara program och skript dock fortfarande begränsat till de behörigheter som beviljas åtkomst till den överordnade programpoolen. En app kan till exempel starta en körbar fil som gör en utgående HTTP-anrop, men att samma körbara inte försöker koppla IP-adressen för en virtuell dator från dess nätverkskort. Samtal utgående nätverket tillåts lågprivilegierat koden, men försök att konfigurera nätverksinställningar på en virtuell dator kräver administrativa privilegier.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostik loggar och händelser
Logginformation är en annan uppsättning data som vissa appar försöker få åtkomst till. Vilka typer av logginformation som är tillgänglig för kod som körs i Apptjänst innehåller diagnostik och logga information som genererats av en app som är också lätt tillgängliga i appen. 

Till exempel finns W3C HTTP-loggar som genereras av en aktiv app antingen på en loggkatalog i nätverksresurs eller har skapats för appen tillgänglig i blob storage om en kund har ställt in W3C-loggning till lagring. Det senare alternativet gör att stora mängder loggar samlas in utan att riskera att överstiger Lagringsgränser fil som är kopplad till en nätverksresurs.

I en liknande vein realtid diagnostikinformation från .NET-appar kan också loggas med .NET-spårning och diagnostik infrastruktur med alternativ för att skriva spårningsinformationen till antingen appens nätverksresurs eller alternativt till en blob-lagringsplats.

Delar av diagnostik loggning och spårning som inte är tillgängliga för appar är Windows ETW-händelser och vanliga Windows-händelseloggar (t.ex., program händelseloggarna System och säkerhet). Eftersom ETW spårningsinformation kan vara synlig datoromfattande (med höger ACL: er), blockeras Läs- och skrivåtkomst till ETW-händelser. Utvecklare märker att API-anrop kan läsa och skriva ETW-händelser och vanliga Windows-händelseloggar verkar fungera, men det är eftersom Apptjänst ”faking” anropen så att de visas ska lyckas. I själva verket har programkoden inte åtkomst till den här informationen om händelsen.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Registeråtkomst
Appar som har skrivskyddad åtkomst till mycket (även om inte alla) i registret på den virtuella datorn körs på. I praktiken innebär detta registernycklar som tillåter skrivskyddad åtkomst till den lokala gruppen användare är tillgängliga för appar. En del av registret som inte stöds för närvarande för Läs- eller skrivbehörighet är HKEY\_aktuella\_användarstruktur.

Skrivåtkomst till registret är blockerad, inklusive åtkomst till några registernycklar per användare. Appens ur skrivåtkomst till registret bör inte förlita sig på i Azure-miljön eftersom appar kan (och göra) migreras mellan olika virtuella datorer. Endast beständig skrivbara lagring som kan vara beroende av en app är per app innehåll katalogstrukturen lagras på App Service UNC-resurser. 

## <a name="more-information"></a>Mer information

[Azure Web App sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) -den allra senaste informationen om körningsmiljö av App Service. Den här sidan hanteras direkt av Utvecklingsteamet App Service.

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
> 
> 


