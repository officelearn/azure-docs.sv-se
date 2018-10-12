---
title: Konfigurera webbappar i Azure App Service
description: Så här konfigurerar du en webbapp i Azure App Services
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.openlocfilehash: cf3a6fe24082a10db6a5b1267b70435d9e36b720
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115530"
---
# <a name="configure-web-apps-in-azure-app-service"></a>Konfigurera webbappar i Azure App Service

Det här avsnittet förklarar hur du konfigurerar en webbapp med den [Azure Portal].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Programinställningar
1. I den [Azure Portal], öppnar du bladet för webbappen.
3. Klicka på **Programinställningar**.

![Programinställningar][configure01]

Den **programinställningar** bladet har inställningar som är grupperade under flera kategorier.

### <a name="general-settings"></a>Allmänna inställningar
**Ramverksversioner**. Ange alternativ om din app använder alla dessa ramverk: 

* **.NET framework**: Ange .NET framework-version. 
* **PHP**: Ange PHP-version eller **OFF** att inaktivera PHP. 
* **Java**: Välj den Java-versionen eller **OFF** att inaktivera Java. Använd den **Webbehållaren** möjlighet att välja mellan Tomcat och Jetty-versioner.
* **Python**: Välj Python-version eller **OFF** att inaktivera Python.

För tekniska skäl inaktiveras om du aktiverar Java för din app alternativen .NET, PHP och Python.

<a name="platform"></a>
**Plattformen**. Anger om din webbapp körs i en 32-bitars eller 64-bitars miljö. 64-bitars-miljö kräver Basic eller Standard-nivån. Kostnadsfri och delad klient körs alltid i en 32-bitars-miljö.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Web Sockets**. Ange **på** att aktivera WebSocket-protokoll, till exempel, om din webbapp använder [ASP.NET SignalR] eller [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Always On**. Som standard inaktiveras webbappar om de är inaktiva under en viss tidsperiod. På så sätt kan systemet spara resurser. I Basic eller Standard-läge kan du aktivera **Always On** att hålla appen att läsa in hela tiden. Om din app körs kontinuerliga WebJobs eller körs WebJobs utlöses med hjälp av ett CRON-uttryck, bör du aktivera **alltid på**, eller webbjobb körs inte på ett tillförlitligt sätt.

**Hanterad Pipelineversion**. Anger IIS [Pipeline-läge]. Lämna den här uppsättningen integrerad (standard) om du inte har en äldre app som kräver en äldre version av IIS.

**HTTP-Version**. Ange **2.0** aktivera stöd för [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokoll. 

> [!NOTE]
> De flesta moderna webbläsare stöder HTTP/2-protokollet via TLS blir bara, medan icke-krypterad trafik fortsätter att använda HTTP/1.1. Se till att webbläsare ansluta till din app med HTTP/2, antingen [köpa ett App Service Certificate](web-sites-purchase-ssl-web-site.md) för appens anpassade domän eller [bind ett certifikat från tredje part](app-service-web-tutorial-custom-ssl.md).

**ARR-tillhörighet**. I en app som skalas ut till flera VM-instanser, ARR-tillhörighet cookies garanterar att klienten dirigeras till samma-instans för resten av sessionen. För att förbättra prestandan för tillståndslösa program, välja alternativet **av**.   

**Automatisk växling**. Om du aktiverar automatisk växling för ett distributionsfack App Service automatiskt att växla som webbappen till produktion när du skickar en uppdatering till platsen. Mer information finns i [distribuera till mellanlagringsplatser för web apps i Azure App Service](web-sites-staged-publishing.md).

### <a name="debugging"></a>Felsökning
**Fjärrfelsökning**. Gör det möjligt att fjärrfelsökning. När aktiverad kan du använda fjärrfelsökningsprogrammet i Visual Studio för att ansluta direkt till din webbapp. Fjärrfelsökning förblir aktiverat i 48 timmar. 

### <a name="app-settings"></a>Appinställningar
Det här avsnittet innehåller namn/värde-par som webbappen ska läsas in på start upp. 

* För .NET-appar är de här inställningarna införs i din .NET-konfiguration `AppSettings` vid körning, åsidosätter befintliga inställningar. 
* För App Service på Linux eller Web App for Containers, om du har kapslad json nyckelstruktur i ditt namn som `ApplicationInsights:InstrumentationKey` behöver du ha `ApplicationInsights__InstrumentationKey` som namn. Observera så att alla `:` ska ersättas med `__` (dvs. dubbla understreck).
* PHP, Python, Java och Node-program kan komma åt de här inställningarna som miljövariabler under körning. Två miljövariabler som skapats för varje appinställning: en med namnet som anges i inställningen appinmatning och en annan med prefixet APPSETTING_. Båda innehåller samma värde.

Appinställningar är alltid krypterade när lagrade (krypterat i vila).

### <a name="connection-strings"></a>Anslutningssträngar
Anslutningssträngar för länkade resurser. 

För .NET-appar, är dessa anslutningssträngar införs i din .NET-konfiguration `connectionStrings` inställningar vid körning, åsidosätter befintliga poster där nyckeln är lika med länkade databasens namn. 

För PHP, Python, Java och Node-program, kommer att finnas inställningarna som miljövariabler vid körning, föregås av anslutningstypen. Variabeln prefixen miljö är följande: 

* SQLServer: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL-databas: `SQLAZURECONNSTR_`
* Anpassad: `CUSTOMCONNSTR_`

Exempel: om en anslutningssträng för MySql med namnen `connectionstring1`, den kan nås via miljövariabeln `MYSQLCONNSTR_connectionString1`.

Anslutningssträngar krypteras alltid när lagras (krypterat i vila).

### <a name="default-documents"></a>Standarddokument
Standarddokument är den webbsida som visas på rot-URL för en webbplats.  Den första matchande filen i listan används. 

Webbappar kan använda moduler att vägen baserat på URL i stället för tillhandahåller statiskt innehåll, då det finns inga standarddokument som sådana.    

### <a name="handler-mappings"></a>Hanterarmappningar
Använd det här området för att lägga till processorer för anpassat skript för att hantera begäranden för specifika filtillägg. 

* **Tillägget**. Filnamnstillägg som ska hanteras, till exempel *.php eller handler.fcgi. 
* **Skriptet Processorsökväg**. Den absoluta sökvägen till Skriptprocessor. Begäranden om filer som matchar filnamnstillägget kommer att behandlas av Skriptprocessor för. Använd sökvägen med `D:\home\site\wwwroot` att referera till rotkatalogen för din app.
* **Ytterligare argument**. Valfria kommandoradsargument för Skriptprocessor 

### <a name="virtual-applications-and-directories"></a>Virtuella program och kataloger
Ange varje virtuell katalog och dess motsvarande fysisk sökväg i förhållande till webbplatsroten för att konfigurera virtuella program och kataloger. Du kan också markera den **program** kryssrutan för att markera en virtuell katalog som ett program.

## <a name="enabling-diagnostic-logs"></a>Aktiverar diagnostikloggar
Aktivera diagnostikloggar:

1. I bladet för din webbapp, klickar du på **alla inställningar**.
2. Klicka på **Diagnostikloggar**. 

Alternativ för att skriva diagnostikloggar från ett webbprogram som har stöd för loggning: 

* **Programloggning**. Skriver programloggar till filsystemet. Loggning användas i 12 timmar. 

**Nivå**. När programmet har aktiverats, anger det här alternativet mängden information som ska registreras (fel, varning, Information eller utförlig).

**Webbserverloggning**. Loggar sparas i W3C utökat loggfilsformat. 

**Detaljerade felmeddelanden**. Sparar felmeddelanden detaljerade htm-filer. Filerna sparas under /LogFiles/DetailedErrors. 

**Spårning av misslyckade begäranden**. Loggar misslyckade förfrågningar till XML-filer. Filerna sparas under/LogFiles/W3SVC*xxx*, där xxx är en unik identifierare. Den här mappen innehåller en XSL-fil och en eller flera XML-filer. Se till att hämta XSL-filen eftersom den innehåller funktioner för att formatera och filtrera innehållet i XML-filerna.

Om du vill visa loggfilerna, måste du skapa FTP-autentiseringsuppgifter på följande sätt:

1. I bladet för din webbapp, klickar du på **alla inställningar**.
2. Klicka på **distributionsbehörigheterna**.
3. Ange ett användarnamn och lösenord.
4. Klicka på **Spara**.

![Ange autentiseringsuppgifter för distribution][configure03]

Fullständig FTP-användarnamnet är ”app\username” där *app* är namnet på din webbapp. Användarnamnet visas i webbappsbladet, under **Essentials**.

![FTP-autentiseringsuppgifter för distribution][configure02]

## <a name="other-configuration-tasks"></a>Andra konfigurationsåtgärder
### <a name="ssl"></a>SSL
Du kan ladda upp SSL-certifikat för en anpassad domän i Basic eller Standard-läge. Mer information finns i [aktivera HTTPS för en webbapp](app-service-web-tutorial-custom-ssl.md). 

Om du vill visa dina uppladdade certifikat klickar du på **alla inställningar** > **anpassade domäner och SSL**.

### <a name="domain-names"></a>Domännamn
Lägga till anpassade domännamn för din webbapp. Mer information finns i [konfigurera ett anpassat domännamn för en webbapp i Azure App Service](app-service-web-tutorial-custom-domain.md).

Du kan visa dina domännamn genom att klicka på **alla inställningar** > **anpassade domäner och SSL**.

### <a name="deployments"></a>Distributioner
* Konfigurera kontinuerlig distribution. Se [med hjälp av Git för att distribuera Web Apps i Azure App Service](app-service-deploy-local-git.md).
* Distributionsplatser. Se [distribuera till Mellanlagringsmiljöer för Webbappar i Azure App Service].

Om du vill visa dina distributionsplatser, klickar du på **alla inställningar** > **distributionsfack**.

### <a name="monitoring"></a>Övervakning
Du kan testa tillgängligheten för HTTP eller HTTPS-slutpunkter, från upp till tre platser för geo-distribuerad i Basic eller Standard-läge. Det går inte att en övervakningstestet om HTTP-svarskoden är ett fel (4xx eller 5xx) eller svaret tar mer än 30 sekunder. En slutpunkt anses vara tillgänglig om övervakningstesten lyckas från de angivna platserna. 

Mer information finns i [så här: övervaka status för web-slutpunkt].

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service]. Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Konfigurera ett anpassat domännamn i Azure App Service]
* [Aktivera HTTPS för en app i Azure App Service]
* [Skala en webbapp i Azure App Service]
* [Grundläggande övervakning för Web Apps i Azure App Service]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurera ett anpassat domännamn i Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Distribuera till Mellanlagringsmiljöer för Webbappar i Azure App Service]: ./web-sites-staged-publishing.md
[Aktivera HTTPS för en app i Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Så här: övervaka status för web-slutpunkt]: http://go.microsoft.com/fwLink/?LinkID=279906
[Grundläggande övervakning för Web Apps i Azure App Service]: ./web-sites-monitor.md
[Pipeline-läge]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skala en webbapp i Azure App Service]: ./web-sites-scale.md
[Prova App Service]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
