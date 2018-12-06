---
title: Migrera från Mobile Services till en App Service-Mobilapp
description: Lär dig hur du enkelt migrera ditt program för mobiltjänster till en App Service Mobile App
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: crdun
ms.openlocfilehash: 7fdbbee27f83a4583390158e456270324967b28a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961611"
---
# <a name="article-top"></a>Migrera din befintliga Azure-Mobiltjänst till Azure App Service
Med den [allmän tillgänglighet för Azure App Service], Azure Mobile Services-platser kan enkelt migreras på plats kan utnyttja alla funktioner i Azure App Service.  Det här dokumentet beskriver vad som händer när du migrerar din webbplats från Azure Mobile Services till Azure App Service.

## <a name="what-does-migration-do"></a>Vad gör migreringen att webbplatsen
Migrering av din Azure-Mobiltjänst aktiverar din Mobiltjänst till ett [Azure App Service] app utan att påverka koden.  Din Meddelandehubbar, SQL-dataanslutning, autentiseringsinställningarna, schemalagda jobb och domännamn ändras inte.  Mobila klienter med hjälp av Azure mobila tjänster fortsätter att fungera normalt.  Migreringen startar om tjänsten när den överförs till Azure App Service.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Varför du bör migrera din plats
Microsoft rekommenderar att du migrerar dina Azure mobila tjänster för att kunna utnyttja funktionerna i Azure App Service, inklusive:

* Nya värdfunktioner, såsom [WebJobs] och [anpassade domännamn].
* Övervakning och felsökning med [Application Insights].
* Inbyggda DevOps-verktyg, inklusive [mellanlagringsplatser], återställa och i produktion testning.
* [Automatisk skalning], belastningsutjämning, och [prestandaövervakning].

Mer information om fördelarna med Azure App Service finns i den [Mobile Services jämfört med App Service] avsnittet.

## <a name="before-you-begin"></a>Innan du börjar
Innan du påbörjar något större arbete på din webbplats, bör du säkerhetskopiera dina Mobiltjänst skript och SQL-databas.

## <a name="migrating-site"></a>Migrera dina webbplatser
Migreringsprocessen migrerar alla platser inom en enda Azure-Region.

Att migrera din plats:

1. Logga in på den [Klassisk Azure-portal].
2. Välj en Mobiltjänst i den region som du vill migrera.
3. Klicka på den **migrera till App Service** knappen.

   ![Knappen migrera][0]
4. Läsa migrera till dialogrutan för Apptjänst.
5. Ange namnet på din Mobiltjänst i rutan.  Ange till exempel om ditt domännamn är contoso.azure-mobile.net, sedan *contoso* i rutan.
6. Klicka på knappen skalstreck.

Övervaka status för migreringen i Övervakaren för aktiviteten. Webbplatsen visas som *migrera* i den klassiska Azure-portalen.

  ![Aktivitetsövervakning för migrering][1]

Varje migrering kan ta allt från 3 till 15 minuter per mobil tjänst som migreras.  Webbplatsen finns kvar under migreringen.
Webbplatsen startas om i slutet av migreringsprocessen.  Platsen är inte tillgänglig under omstarten, vilket kan de senaste några sekunder.

## <a name="finalizing-migration"></a>Slutför migreringen
Planera att testa din webbplats från en mobil klient vid slutet av migreringsprocessen.  Se till att du kan utföra alla vanliga Klientåtgärder utan ändringar mobila klienten.  

### <a name="update-app-service-tier"></a>Välj en lämplig App Service-prisnivå
Har du mer flexibilitet i betalningen när du har migrerat till Azure App Service.

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Bladet inställningar öppnas som standard.
4. Klicka på **Apptjänstplan** på menyn Inställningar.
5. Klicka på den **prisnivå** panelen.
6. Klicka på panelen som passar dina behov och klicka sedan på **Välj**.  Du kan behöva klicka på **visa alla** att se alla tillgängliga prisnivåer.

Som utgångspunkt rekommenderar vi följande nivåer:

| Mobiltjänst prisnivå | App Service-prisnivå |
|:--- |:--- |
| Kostnadsfri |F1 Kostnadsfri |
| Basic |Basic B1 |
| Standard |S1 Standard |

Det finns större flexibilitet att välja rätt prisnivån för ditt program.  Referera till [Prissättning för App Service] för fullständig information om priser för din nya App Service.

> [!TIP]
> App Service Standard-nivån innehåller åtkomst till många funktioner som du kanske vill använda, inklusive [mellanlagringsplatser], automatisk säkerhetskopiering och automatisk skalning.  Kolla in de nya funktionerna när du är det!
>
>

### <a name="review-migration-scheduler-jobs"></a>Granska migrerade Scheduler-jobb
Scheduler-jobb visas inte förrän cirka 30 minuter efter migreringen.  Schemalagda jobb fortsätter att köras i bakgrunden.
Visa dina schemalagda jobb när de är synliga igen:

1. Logga in på [Azure Portal].
2. Välj **Bläddra >**, ange **schema** i den *Filter* rutan och välj sedan **Scheduler samlingar**.

Det finns ett begränsat antal kostnadsfria scheduler-jobb tillgängliga efter migreringen.  Granskar din användning och [Azure Scheduler-planer].

### <a name="configure-cors"></a>Konfigurera CORS om det behövs
Resursdelning mellan ursprung delning är en teknik för att tillåta en webbplats att komma åt ett webb-API på en annan domän.  Om du använder Azure Mobile Services med en associerad webbplats, måste du konfigurera CORS som en del av migreringen.  Om du använder Azure Mobile Services exklusivt från mobila enheter, behöver inte CORS konfigureras utom i sällsynta fall.

Dina migrerade CORS-inställningar är tillgängliga som den **MS_CrossDomainWhitelist** Appinställningen.  Migrera din webbplats till funktionen App Services CORS:

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Bladet inställningar öppnas som standard.
4. Klicka på **CORS** i API-menyn.
5. Ange några tillåtna ursprung i rutan, tryck på RETUR efter varje kriterium.
6. Klicka på knappen Spara när listan över tillåtna ursprung är korrekt.

> [!TIP]
> En av fördelarna med att använda en Azure App Service är att du kan köra din webbplats och Mobiltjänst på samma plats.  Mer information finns i den [nästa steg](#next-steps) avsnittet.
>
>

### <a name="download-publish-profile"></a>Ladda ned en ny publiceringsprofil
Publiceringsprofilen för din webbplats ändras när du migrerar till Azure App Service.  Om du vill publicera webbplatsen från Visual Studio behöver du en ny publiceringsprofilen.  Så här hämtar nya publiceringsprofilen:

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Klicka på **hämta publiceringsprofil**.

PublishSettings-filen laddas ned till datorn.  Det kallas vanligtvis *sitename*. PublishSettings.  Importera Publiceringsinställningar till ditt befintliga projekt:

1. Öppna Visual Studio och Azure mobila tjänster för projektet.
2. Högerklicka på projektet i den **Solution Explorer** och välj **publicera...**
3. Klicka på **Importera**
4. Klicka på **Bläddra** och välj din hämtade inställningsfilen för publicering.  Klicka på **OK**
5. Klicka på **Validera anslutningen** att se till att publicera inställningar fungerar.
6. Klicka på **publicera** att publicera din webbplats.

## <a name="working-with-your-site"></a>Arbeta med din webbplats efter migrering
Börja arbeta med din nya App Service i den [Azure Portal] efter migreringen.  Följande är några anmärkningar om specifika åtgärder som du använde för att utföra i den [Klassisk Azure-portal], tillsammans med motsvarande App Service.

### <a name="publishing-your-site"></a>Ladda ned och publicera webbplatsen migrerade
Webbplatsen är tillgängligt via git eller ftp och kan publiceras med olika olika sätt, inklusive WebDeploy, TFS, Mercurial, GitHub och FTP.  Autentiseringsuppgifter för distribution migreras med resten av din webbplats.  Om du inte har angett dina autentiseringsuppgifter för distribution eller om du inte kommer ihåg dem, kan du återställa dem:

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Bladet inställningar öppnas som standard.
4. Klicka på **autentiseringsuppgifter för distribution** i PUBLICERINGEN menyn.
5. Ange de nya autentiseringsuppgifterna för distribution i rutorna och sedan klicka på knappen Spara.

Du kan använda dessa autentiseringsuppgifter för att klona platsen med git eller konfigurera automatiska distributioner från GitHub, TFS eller Mercurial.  Mer information finns i [Azure App Service dokumentationen].

### <a name="appsettings"></a>Programinställningar
De flesta inställningar för en mobil tjänst till migrerade är tillgängliga via Appinställningar.  Du kan hämta en lista över de appinställningarna från den [Azure Portal].
Visar eller ändrar inställningarna för din app:

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Bladet inställningar öppnas som standard.
4. Klicka på **programinställningar** i menyn Allmänt.
5. Rulla ned till avsnittet Appinställningar och hitta din app-inställning.
6. Klicka på värdet på appinställningen som du vill redigera värdet.  Klicka på **spara** att spara värdet.

Du kan uppdatera flera appinställningar på samma gång.

> [!TIP]
> Det finns två programinställningar med samma värde.  Du kan till exempel se *ApplicationKey* och *MS\_ApplicationKey*.  Uppdatera båda programinställningar på samma gång.
>
>

### <a name="authentication"></a>Autentisering
Alla autentiseringsinställningar är tillgängliga som Appinställningar på webbplatsen migrerade.  För att uppdatera autentiseringsinställningarna för, måste du ändra appinställningarna för lämpliga.  I följande tabell visas lämpliga appinställningarna för din autentiseringsprovider:

| Leverantör | Klientorganisations-ID | Klienthemlighet | Andra inställningar |
|:--- |:--- |:--- |:--- |
| Microsoft-konto |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Obs: **MS\_AadTenants** lagras som en kommaavgränsad lista över klient domäner (fälten ”tillåtna Klientorganisationer” i Mobile Services-portalen).

> [!WARNING]
> **Använd inte autentiseringsmekanismer i menyn Inställningar**
>
> Azure App Service tillhandahåller ett separat ”Kodlösa” autentisering och auktorisering system under den *autentisering / auktorisering* menyn Inställningar och (föråldrad) *Mobile autentisering* alternativet under menyn Inställningar.  Dessa alternativ är inte kompatibel med en migrerade Azure-mobiltjänst.  Du kan [uppgraderar platsen](app-service-mobile-net-upgrading-from-mobile-services.md) att dra nytta av Azure App Service-autentisering.
>
>

### <a name="easytables"></a>Data
Den *Data* fliken i mobiltjänster har ersatts av *enkla tabeller* i Azure-portalen.  Öppna enkla tabeller:

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Bladet inställningar öppnas som standard.
4. Klicka på **enkla tabeller** på menyn mobila.

Du kan lägga till en tabell genom att klicka på den **Lägg till** knappen eller få åtkomst till dina befintliga tabeller genom att klicka på ett tabellnamn.  Det finns olika åtgärder som du kan göra från det här bladet, inklusive:

* Ändra tabellbehörigheter
* Redigera operativa skript
* Hantera tabellschemat
* Tar bort tabellen
* Rensa innehåll
* Tar bort specifika rader i tabellen

### <a name="easyapis"></a>API
Den *API* fliken i mobiltjänster har ersatts av *enkla API: er* i Azure-portalen.  Öppna enkla API: er:

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Bladet inställningar öppnas som standard.
4. Klicka på **enkla API: er** på menyn mobila.

Dina migrerade API: er visas redan i bladet.  Du kan också lägga till ett API från det här bladet.  Klicka på API: et för att hantera ett visst API.
Du kan justera behörigheterna och redigera skripten för API: et från det nya bladet.

### <a name="on-demand-jobs"></a>Scheduler-jobb
Alla scheduler-jobb är tillgängliga via avsnittet Scheduler-Jobbsamlingar.  Öppna scheduler-jobb:

1. Logga in på [Azure Portal].
2. Välj **Bläddra >**, ange **schema** i den *Filter* rutan och välj sedan **Scheduler samlingar**.
3. Välj Jobbsamlingen för din webbplats.  Namnet är *sitename*-jobb.
4. Klicka på **inställningar**.
5. Klicka på **Scheduler-jobb** under hantera.

Schemalagda jobb visas med den frekvens som du har angett före migreringen.  På begäran-jobben är inaktiverade.  Köra ett jobb på begäran:

1. Välj det jobb som du vill köra.
2. Om det behövs klickar du på **aktivera** att aktivera jobbet.
3. Klicka på **inställningar**, sedan **schema**.
4. Välj en upprepning av **när**, klicka sedan på **spara**

Jobb på begäran finns i `App_Data/config/scripts/scheduler post-migration`.  Vi rekommenderar att du konverterar alla på begäran-jobb [webjobs] eller [Funktioner].  Skriva ny scheduler-jobb som [WebJobs] eller [Funktioner].

### <a name="notification-hubs"></a>Meddelandehubbar
Mobiltjänster använder Meddelandehubbar för push-meddelanden.  Följande inställningar för appen används för att länka Notification Hub i dina mobila tjänster efter migreringen:

| Tillämpningsinställning | Beskrivning |
|:--- |:--- |
| **MS\_PushEntityNamespace** |Notification Hub Namespace |
| **MS\_NotificationHubName** |Namnet på Meddelandehubben |
| **MS\_NotificationHubConnectionString** |Notification Hub-anslutningssträngen |
| **MS\_NamespaceName** |Ett alias för MS_PushEntityNamespace |

Din Meddelandehubb hanteras via den [Azure Portal].  Observera Notification Hub-namn (du hittar den med hjälp av Appinställningarna):

1. Logga in på [Azure Portal].
2. Välj **Bläddra**> och välj sedan **Meddelandehubbar**
3. Klicka på namnet för Notification Hub som är associerade med den mobila tjänsten.

> [!NOTE]
> Om din Notification HUb är av typen ”kombinerad”, är det inte visas.  ”Blandat” Skriv notification hubs använder både Meddelandehubbar och äldre Service Bus-funktionerna.  [Omvandla dina blandat namnområden] innan du fortsätter.  När konverteringen har slutförts, din meddelandehubb som visas i den [Azure Portal].
>
>

Mer information finns i [Notification Hubs] dokumentation.

> [!TIP]
> Notification Hubs-hanteringsfunktioner i den [Azure Portal] är fortfarande i förhandsversion.  Den [Klassisk Azure-portal] förblir tillgängliga för hantering av alla Meddelandehubbar.
>
>

### <a name="legacy-push"></a>Äldre Push-inställningar
Om du har konfigurerat Push på mobiltjänsten innan introduktionen på Meddelandehubbar du använder *äldre push*.  Om du använder Push och du inte ser en Meddelandehubb som visas i din konfiguration så är det troligt att du använder *äldre push*.  Den här funktionen har migrerats med alla andra funktioner.  Vi rekommenderar dock att du uppgraderar till Meddelandehubbar strax efter att migreringen är klar.

Under tiden kan alla äldre push-inställningar (med viktiga undantag för APN-certifikatet) som är tillgängliga i App-inställningar.  Uppdatera APN-certifikatet genom att ersätta filen på filsystemet.

### <a name="app-settings"></a>Andra Appinställningar
Följande ytterligare inställningar är migrerade från din Mobiltjänst och är tillgängliga under *inställningar* > *Appinställningar*:

| Tillämpningsinställning | Beskrivning |
|:--- |:--- |
| **MS\_MobileServiceName** |Namnet på din app |
| **MS\_MobileServiceDomainSuffix** |Domänprefixet. dvs azure-mobile.net |
| **MS\_ApplicationKey** |Din programnyckel |
| **MS\_MasterKey** |Din app huvudnyckel |

Programnyckel och huvudnyckeln är identiska med nycklar för programmet från din ursprungliga mobiltjänst.  I synnerhet skickas Programnyckeln av mobila klienter att verifiera deras användning av mobila API: et.

### <a name="cliequivalents"></a>Kommandoradsverktyget motsvarigheter
Du kan använda längre den *azure mobila* kommando för att hantera dina Azure Mobile Services-platsen.  I stället många funktioner har ersatts med den *azure plats* kommando.  Använd följande tabell för att hitta motsvarigheter för vanliga kommandon:

| *Azure Mobile* kommando | Motsvarande *Azure Site* kommando |
|:--- |:--- |
| mobila platser |platslistan för plats |
| mobila lista |webbplatslista för företagsläge |
| mobila show *namn* |plats show *namn* |
| mobila omstart *namn* |plats omstart *namn* |
| mobila omdistribution *namn* |Distribuera om distributionen av site *commitId* *namn* |
| mobila nyckeluppsättning *namn* *typ* *värde* |ta bort platsen appsetting *nyckel* *namn* <br/> Lägg till plats appsetting *nyckel*=*värdet* *namn* |
| mobilkonfiguration lista *namn* |appsetting platslistan *namn* |
| Hämta mobilkonfiguration *namn* *nyckel* |plats appsetting show *nyckel* *namn* |
| mobila config set *namn* *nyckel* |ta bort platsen appsetting *nyckel* *namn* <br/> Lägg till plats appsetting *nyckel*=*värdet* *namn* |
| mobila domänlistan *namn* |plats domänlistan *namn* |
| Lägg till mobila domän *namn* *domän* |plats domän lägger du till *domän* *namn* |
| ta bort mobila domän *namn* |platsen domain delete *domän* *namn* |
| mobila skala show *namn* |plats show *namn* |
| mobila skala ändra *namn* |plats Skalningsläge *läge* *namn* <br /> plats skala instanserna *instanser* *namn* |
| mobila appsetting lista *namn* |appsetting platslistan *namn* |
| Lägg till mobila appsetting *namn* *nyckel* *värde* |Lägg till plats appsetting *nyckel*=*värdet* *namn* |
| ta bort mobila appsetting *namn* *nyckel* |ta bort platsen appsetting *nyckel* *namn* |
| mobila appsetting show *namn* *nyckel* |ta bort platsen appsetting *nyckel* *namn* |

Uppdatera autentisering eller inställningar för push-meddelanden genom att uppdatera lämpliga inställningen.
Redigera filer och publicera din webbplats via ftp eller git.

### <a name="diagnostics"></a>Diagnostik- och loggning
Diagnostisk loggning är normalt inaktiverat i en Azure App Service.  Aktivera Diagnostisk loggning:

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Bladet inställningar öppnas som standard.
4. Välj **diagnostikloggar** under menyn funktioner.
5. Klicka på **på** för följande loggar: **Programinloggning (filsystem)**, **detaljerade felmeddelanden**, och **spårning av misslyckade begäranden**
6. Klicka på **filsystem** för webbserver-loggning
7. Klicka på **Spara**

Visa loggarna:

1. Logga in på [Azure Portal].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på din migrerade mobiltjänst.
3. Klicka på den **verktyg** knappen
4. Välj **Log Stream** under menyn Följ.

Loggarna visas i fönstret som de skapas.  Du kan också hämta loggar för senare analys med dina autentiseringsuppgifter för distribution. Mer information finns i den [loggning] dokumentation.

## <a name="known-issues"></a>Kända problem
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Tar bort en migreras Mobile App klon orsakar ett eventuellt strömavbrott
Om du klona migrerade mobiltjänsten med hjälp av Azure PowerShell och sedan ta bort klonen, tas DNS-posten för produktionstjänsten bort.  Webbplatsen är inte längre vara tillgänglig från Internet.  

Lösning: Om du vill klona din webbplats kan göra det via portalen.

### <a name="changing-webconfig-does-not-work"></a>Ändra Web.config fungerar inte
Om du har en ASP.NET-webbplats kan ändras till den `Web.config` filen inte tillämpas.  Azure App Service bygger ett lämpligt `Web.config` fil under starten för mobiltjänster-runtime.  Du kan åsidosätta vissa inställningar (till exempel anpassade huvuden) med hjälp av en XML-transformation.  Skapa en fil i kallas `applicationHost.xdt` -den här filen måste hamnar i den `D:\home\site` på Azure-tjänsten.  Ladda upp den `applicationHost.xdt` filen via ett anpassat distributionsskript eller direkt med hjälp av Kudu.  Nedan visas ett exempel dokument:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Mer information finns i den [XDT Transform-exempel] dokumentation på GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Migrerade mobiltjänster kan inte läggas till Traffic Manager
När du skapar en Traffic Manager-profil väljer du det går inte att direkt en migrerade Mobiltjänst till profilen.  Använda en ”extern slutpunkt”.  Den externa slutpunkten kan endast läggas till via PowerShell.  Mer information finns i den [Traffic Manager självstudien](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Nästa steg
Nu när ditt program har migrerats till App Service, finns det fler funktioner som du kan använda:

* Distribution [mellanlagringsplatser] gör att du kan mellanlagra ändringar på webbplatsen och genomföra A / B-testning.
* [WebJobs] Ange en ersättning för på begäran-schemalagda jobb.
* Du kan [kontinuerligt distribuera] webbplatsen genom att länka din webbplats till GitHub, TFS eller Mercurial.
* Du kan använda [Application Insights] att övervaka din webbplats.
* Hantera en webbplats och en Mobile API från samma kod.

### <a name="upgrading-your-site"></a>Uppgradera din Mobile Services-plats till Azure SDK för Mobile Apps
* För Node.js-baserade server-projekt, den nya [Mobile Apps Node.js SDK] har flera nya funktioner. Exempelvis kan kan du nu göra lokal utveckling och felsökning, använda valfri Node.js version ovan 0.10 och anpassa med alla Express.js mellanprogram.
* För. NET-baserade server-projekt, den nya [Mobile Apps SDK NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) har mer flexibilitet i NuGet-beroenden.  Dessa paket stödja den nya App Service-autentiseringen och sätt ihop med alla ASP.NET-projekt. Mer information om hur du uppgraderar finns [uppgradera din befintliga .NET-Mobiltjänst till App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Prissättning för App Service]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Automatisk skalning]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-web-overview.md
[Klassisk Azure-portal]: https://manage.windowsazure.com
[Azure Portal]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[Azure Scheduler-planer]: ../scheduler/scheduler-plans-billing.md
[kontinuerligt distribuera]: ../app-service/app-service-continuous-deployment.md
[Omvandla dina blandat namnområden]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: https://curl.haxx.se/
[Anpassade domännamn]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: https://www.telerik.com/fiddler
[allmän tillgänglighet för Azure App Service]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[Loggning]: ../app-service/web-sites-enable-diagnostic-log.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Mobile Services jämfört med App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Prestandaövervakning]: ../app-service/web-sites-monitor.md
[Postman]: https://www.getpostman.com/
[mellanlagringsplatser]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[XDT Transform-exempel]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funktioner]: ../azure-functions/functions-overview.md
