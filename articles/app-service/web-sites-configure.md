---
title: Konfigurera webbappar i Azure App Service
description: Hur du konfigurerar en webbapp i Azure App Service
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.openlocfilehash: fd1a8cf442ea0688e027f8f8028ee8b4e149d8d2
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="configure-web-apps-in-azure-app-service"></a>Konfigurera webbappar i Azure App Service
Det här avsnittet beskriver hur du konfigurerar en web app med hjälp av den [Azure Portal].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Programinställningar
1. I den [Azure Portal], öppna bladet för webbprogrammet.
3. Klicka på **Programinställningar**.

![Programinställningar][configure01]

Den **programinställningar** bladet har inställningar som är grupperade under flera kategorier.

### <a name="general-settings"></a>Allmänna inställningar
**Versioner av Framework**. Ange dessa alternativ om din app använder alla dessa ramverk: 

* **.NET framework**: Ange .NET framework-version. 
* **PHP**: Ange PHP-version eller **OFF** att inaktivera PHP. 
* **Java**: Välj den Java-versionen eller **OFF** att inaktivera Java. Använd den **Webbehållaren** möjlighet att välja mellan Tomcat och Jetty-versioner.
* **Python**: Välj Python-version eller **OFF** att inaktivera Python.

Av tekniska skäl inaktiveras när du aktiverar Java för din app alternativen för .NET, PHP och Python.

<a name="platform"></a>
**Plattform**. Anger om ditt webbprogram kör i en 32-bitars eller 64-bitars miljö. 64-bitars miljö kräver Basic eller Standard-läge. Frigör och delade lägen köras alltid i en 32-bitars-miljö.

**Web Sockets**. Ange **ON** att WebSocket-protokollet, till exempel om ditt webbprogram använder [ASP.NET SignalR] eller [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Always On**. Som standard inaktiveras webbappar om de är inaktiv under en tidsperiod. Detta gör att systemet spara resurser. I Basic eller Standard-läge kan du aktivera **alltid på** att appen att läsa in hela tiden. Om din app körs kontinuerliga Webbjobb eller körs WebJobs aktiveras med hjälp av ett CRON-uttryck, bör du aktivera **alltid på**, eller web-jobb körs inte på ett tillförlitligt sätt.

**Hanterat Pipeline-Version**. Anger IIS [pipelineläge]. Låt den här uppsättningen integrerad (standard) om du inte har en äldre app som kräver en äldre version av IIS.

**Automatisk växling**. Om du aktiverar automatisk växling för en distributionsplats Apptjänst automatiskt att växla som webbappen till produktionen när du trycker på en uppdatering till platsen. Mer information finns i [till mellanlagring fack för web apps i Azure App Service](web-sites-staged-publishing.md).

### <a name="debugging"></a>Felsökning
**Fjärrfelsökning**. Aktiverar fjärrfelsökning. När aktiverat, kan du använda fjärråtkomst felsökaren i Visual Studio för att ansluta direkt till ditt webbprogram. Fjärrfelsökning förblir aktiverat för 48 timmar. 

### <a name="app-settings"></a>App-inställningar
Det här avsnittet innehåller namn/värde-par som du web app läser in vid start. 

* För .NET-appar de här inställningarna är injekteras i konfigurationen av .NET `AppSettings` vid körning kan åsidosätta befintliga inställningar. 
* PHP, Python, Java och noden program kan komma åt de här inställningarna som miljövariabler vid körning. Två miljövariabler som skapas för varje appinställning. en med namnet som anges i inställningen som appen och en annan med prefixet APPSETTING_. Båda innehåller samma värde.

### <a name="connection-strings"></a>Anslutningssträngar
Anslutningssträngar för länkade resurser. 

För .NET-appar har dessa anslutningssträngar injekteras i konfigurationen av .NET `connectionStrings` inställningar vid körning kan åsidosätta befintliga poster där nyckeln är lika med länkade databasnamnet. 

PHP, Python, Java och noden program, ska inställningarna vara tillgänglig som miljövariabler vid körning, föregås av anslutningstypen. Variabeln prefix miljö är följande: 

* SQLServer:`SQLCONNSTR_`
* MySQL:`MYSQLCONNSTR_`
* SQL-databas:`SQLAZURECONNSTR_`
* Anpassad:`CUSTOMCONNSTR_`

Om exempelvis en MySql-anslutningssträng vid namn `connectionstring1`, den kan nås via miljövariabeln `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Standarddokument
Standarddokumentet är webbsidan som visas på rot-URL för en webbplats.  Den första matchande filen i listan används. 

Webbprogram kan använda moduler att vägen baserat på URL: en i stället för betjänar statiskt innehåll, då det finns inga standarddokument som sådana.    

### <a name="handler-mappings"></a>Hanterarmappningar
Använd det här området för att lägga till processorer för anpassat skript för att hantera begäranden för specifika filtillägg. 

* **Tillägget**. Filtillägg som ska hanteras, till exempel *.php eller handler.fcgi. 
* **Script Processor sökvägen**. Den absoluta sökvägen till Skriptprocessor. Begäranden om filer som matchar filnamnstillägget kommer att behandlas av Skriptprocessor för. Använd sökvägen med `D:\home\site\wwwroot` att referera till rotkatalogen för din app.
* **Ytterligare argument**. Valfria kommandoradsargument för Skriptprocessor 

### <a name="virtual-applications-and-directories"></a>Virtuella program och kataloger
Ange varje virtuell katalog och dess motsvarande fysiska sökväg i förhållande till webbplatsroten för att konfigurera virtuella program och kataloger. Du kan också markera den **programmet** kryssrutan för att markera en virtuell katalog som ett program.

## <a name="enabling-diagnostic-logs"></a>Aktivera diagnostikloggar
Aktivera diagnostikloggar:

1. I bladet för din webbapp klickar du på **alla inställningar**.
2. Klicka på **diagnostikloggar**. 

Alternativ för att skriva diagnostiska loggar från webbprogram som har stöd för loggning: 

* **Programloggning**. Skriver programloggar till filsystemet. Loggning varar under en period på 12 timmar. 

**Nivå**. När programmet loggning är aktiverat anger det här alternativet mängden information som ska registreras (fel, varning, Information eller utförlig).

**Web server-loggning**. Loggar sparas i W3C utökat loggfilsformat. 

**Detaljerade felmeddelanden**. Sparar felmeddelanden detaljerade .htm-filer. Filerna sparas under /LogFiles/DetailedErrors. 

**Spårning av misslyckade begäranden**. Loggar misslyckade förfrågningar till XML-filer. Filerna sparas under loggfilerna/W3SVC*xxx*, där xxx är en unik identifierare. Den här mappen innehåller en XSL-fil och en eller flera XML-filer. Se till att hämta XSL-filen eftersom det ger funktioner för att formatera och filtrera innehållet i XML-filerna.

Om du vill visa loggfilerna måste du skapa FTP-autentiseringsuppgifter, enligt följande:

1. I bladet för din webbapp klickar du på **alla inställningar**.
2. Klicka på **distributionsbehörigheterna**.
3. Ange ett användarnamn och lösenord.
4. Klicka på **Spara**.

![Ange autentiseringsuppgifter för distribution][configure03]

Det fullständiga namnet för FTP-användare är ”app\username” där *app* är namnet på ditt webbprogram. Användarnamnet visas i bladet webbapp, under **Essentials**.  

![Autentiseringsuppgifter för FTP-distribution][configure02]

## <a name="other-configuration-tasks"></a>Andra konfigurationsuppgifter
### <a name="ssl"></a>SSL
Du kan överföra SSL-certifikat för en anpassad domän i Basic eller Standard-läge. Mer information finns i [aktivera HTTPS för en webbapp]. 

Om du vill visa dina överförda certifikat klickar du på **alla inställningar** > **anpassade domäner och SSL**.

### <a name="domain-names"></a>Domännamn
Lägg till anpassade domännamn för din webbapp. Mer information finns i [konfigurera ett anpassat domännamn för en webbapp i Azure App Service].

Om du vill visa dina domännamn, klickar du på **alla inställningar** > **anpassade domäner och SSL**.

### <a name="deployments"></a>Distributioner
* Ställ in kontinuerlig distribution. Se [med Git för att distribuera Webbappar i Azure App Service](app-service-deploy-local-git.md).
* Distributionsplatser. Se [distribuera till Mellanlagringsmiljöer för Web Apps i Azure App Service].

Om du vill visa dina distributionsplatser, klickar du på **alla inställningar** > **distributionsfack**.

### <a name="monitoring"></a>Övervakning
Du kan testa tillgängligheten för HTTP eller HTTPS-slutpunkter från upp till tre fördelade platser i Basic eller Standard-läge. En övervakning testet misslyckas om HTTP-svarskoden är ett fel (4xx eller 5xx) eller svaret tar mer än 30 sekunder. En slutpunkt anses vara tillgänglig om övervakning testerna lyckas från angivna platser. 

Mer information finns i [så här: övervaka status för web endpoint].

> [!NOTE]
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst]. Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Konfigurera ett anpassat domännamn i Azure App Service]
* [Aktivera HTTPS för en app i Azure App Service]
* [Skala en webbapp i Azure App Service]
* [Övervakning grunderna för Web Apps i Azure App Service]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurera ett anpassat domännamn i Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[distribuera till Mellanlagringsmiljöer för Web Apps i Azure App Service]: ./web-sites-staged-publishing.md
[Aktivera HTTPS för en app i Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[så här: övervaka status för web endpoint]: http://go.microsoft.com/fwLink/?LinkID=279906
[Övervakning grunderna för Web Apps i Azure App Service]: ./web-sites-monitor.md
[pipelineläge]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skala en webbapp i Azure App Service]: ./web-sites-scale.md
[Prova Apptjänst]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
