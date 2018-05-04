---
title: Migrera från Mobile Services i en Apptjänst-Mobilapp
description: Lär dig hur du enkelt migrera Mobile Services programmet till en App Service-Mobilapp
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
ms.openlocfilehash: 5001704f47af0c7b07744f1dceb7aa58bdb6448c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="article-top"></a>Migrera din befintliga Azure-Mobiltjänst till Azure App Service
Med den [allmän tillgång till Azure App Service], Azure Mobile Services-platser kan enkelt migrera i för att kunna utnyttja alla funktioner i Azure App Service.  Det här dokumentet beskrivs vad som händer när du migrerar din webbplats från Azure Mobile Services till Azure App Service.

## <a name="what-does-migration-do"></a>Vad gör migrering till din webbplats
Migrering av din Azure-Mobiltjänst stängs din Mobiltjänst i ett [Azure App Service] app utan att påverka koden.  Din Notification Hubs, SQL-anslutningen, autentiseringsinställningar, schemalagda jobb och domännamn ändras inte.  Mobila klienter som använder ditt Azure-Mobiltjänst fortsätta att fungera normalt.  Migreringen startar om tjänsten när den överförs till Azure App Service.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Varför du bör migrera din plats
Microsoft rekommenderar att du migrerar din Azure-Mobiltjänst för att dra nytta av funktionerna i Azure Apptjänst, bland annat:

* Nya värdfunktioner, inklusive [WebJobs] och [anpassade domännamn].
* Övervakning och felsökning med [Programinsikter].
* Inbyggda DevOps-tooling, inklusive [mellanlagring fack], återställning och i produktion testning.
* [Autoskala], belastningsutjämning, och [prestandaövervakning].

Mer information om fördelarna med Azure App Service finns det [jämfört med Mobile Services. Apptjänst] avsnittet.

## <a name="before-you-begin"></a>Innan du börjar
Innan du påbörjar något större arbete på din webbplats, bör du säkerhetskopiera din Mobiltjänst skript och SQL-databas.

## <a name="migrating-site"></a>Migrera dina platser
Alla platser inom en enda Azure-Region migrerar migreringsprocessen.

För att migrera din plats.

1. Logga in på den [klassiska Azure-portalen].
2. Välj en Mobiltjänst i den region som du vill migrera.
3. Klicka på den **migrera till App Service** knappen.

   ![Knappen migrera][0]
4. Läsa migrera till dialogrutan för Apptjänst.
5. Ange namnet på din Mobiltjänst i rutan.  Till exempel om ditt domännamn är contoso.azure mobile.net, ange *contoso* i rutan.
6. Klicka på knappen skalstreck.

Övervaka status för migrering i övervakaren aktivitet. Webbplatsen visas som *migrera* i den klassiska Azure-portalen.

  ![Aktivitetsövervakning för migrering][1]

Varje migrering kan ta allt från 3 till 15 minuter per mobil tjänst som migreras.  Webbplatsen finns kvar under migreringen.
Webbplatsen har startats om i slutet av migreringsprocessen.  Platsen är inte tillgänglig under omstarten, vilket kan senaste några sekunder.

## <a name="finalizing-migration"></a>Slutför migreringen
Planera att testa din plats från en mobil klient vid slutet av migreringsprocessen.  Se till att du kan utföra alla vanliga Klientåtgärder utan ändringar av den mobila klienten.  

### <a name="update-app-service-tier"></a>Välj en lämplig Apptjänst prisnivån
Har du mer flexibilitet i priser när du har migrerat till Azure App Service.

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Inställningsbladet öppnas som standard.
4. Klicka på **Apptjänstplan** på menyn Inställningar.
5. Klicka på den **prisnivån** panelen.
6. Klicka på panelen som är lämpligt för dina behov och klicka sedan på **Välj**.  Du kan behöva klicka **visa alla** att se tillgängliga prisnivåer.

Som en startpunkt rekommenderar vi följande nivåer:

| Mobiltjänst prisnivån | Prisnivån för Apptjänst |
|:--- |:--- |
| Kostnadsfri |F1 Kostnadsfri |
| Basic |B1 Basic |
| Standard |S1 Standard |

Det finns betydande flexibilitet i välja rätt prisnivån för ditt program.  Referera till [priser för Apptjänst] fullständig information om priser av din nya App Service.

> [!TIP]
> Standardnivån för App Service innehåller åtkomst till många funktioner som du kanske vill använda, inklusive [mellanlagring fack], automatiska säkerhetskopieringar och automatisk skalning.  Ta en titt på nya funktioner när du är det!
>
>

### <a name="review-migration-scheduler-jobs"></a>Granska migrerade schemaläggare
Schemaläggare visas inte förrän cirka 30 minuter efter migreringen.  Schemalagda jobb fortsätter att köras i bakgrunden.
Visa schemalagda jobb när de inte visas igen:

1. Logga in på [Azure-portalen].
2. Välj **Bläddra >**, ange **schema** i den *Filter* och sedan markera **Scheduler samlingar**.

Det finns ett begränsat antal kostnadsfria scheduler jobb tillgängliga efter migreringen.  Granska din användning och [Azure Scheduler planer].

### <a name="configure-cors"></a>Konfigurera CORS vid behov
Resursdelning för korsande ursprung är en teknik för att tillåta en webbplats att få åtkomst till webb-API i en annan domän.  Om du använder Azure Mobile Services med en associerad webbplats, måste du konfigurera CORS som en del av migreringen.  Om du använder Azure Mobile Services uteslutande på mobila enheter, behöver inte CORS konfigureras utom i sällsynta fall.

Inställningarna för migrerade CORS är tillgängliga som den **MS_CrossDomainWhitelist** Appinställningen.  För att migrera din plats till funktionen Apptjänst-CORS.

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Inställningsbladet öppnas som standard.
4. Klicka på **CORS** i API-menyn.
5. Ange några tillåtna ursprung i textrutan, tryck på RETUR efter varje kriterium.
6. Klicka på Spara när listan över tillåtna ursprung är korrekt.

> [!TIP]
> En av fördelarna med att använda en Azure App Service är att du kan köra din webbplats och Mobiltjänst på samma plats.  Mer information finns i [nästa steg](#next-steps) avsnitt.
>
>

### <a name="download-publish-profile"></a>Hämta en ny publiceringsprofilen
Publiceringsprofilen för din webbplats ändras när du migrerar till Azure App Service.  Om du vill publicera webbplatsen från Visual Studio måste en ny publiceringsprofilen.  Hämta nya publiceringsprofilen:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Klicka på **Get publiceringsprofil**.

PublishSettings-filen laddas ned till datorn.  Det kallas vanligtvis *sitename*. PublishSettings.  Importera publiceringsinställningarna till ditt befintliga projekt:

1. Öppna Visual Studio och Azure Mobile Service-projekt.
2. Högerklicka på projektet i den **Solution Explorer** och välj **publicera...**
3. Klicka på **Importera**
4. Klicka på **Bläddra** och välj din hämtade inställningsfilen för publicering.  Klicka på **OK**
5. Klicka på **Validera anslutningen** se till att publicera inställningarna fungerar.
6. Klicka på **publicera** att publicera webbplatsen.

## <a name="working-with-your-site"></a>Arbeta med din webbplats efter migrering
Börjar arbeta med din nya App Service i den [Azure-portalen] efter migreringen.  Följande är några anteckningar på specifika åtgärder som du använde för att utföra i den [klassiska Azure-portalen], tillsammans med motsvarande Apptjänst.

### <a name="publishing-your-site"></a>Ladda ned och publicera webbplatsen migrerade
Webbplatsen är tillgänglig via git och FTP- och kan publiceras med olika olika sätt, inklusive WebDeploy, TFS, ett, GitHub och FTP.  Autentiseringsuppgifter för distribution migreras med resten av platsen.  Om du inte har angett dina autentiseringsuppgifter för distribution eller om du inte kommer ihåg dem, kan du återställa dem:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Inställningsbladet öppnas som standard.
4. Klicka på **distributionsbehörigheterna** i publicering menyn.
5. Ange nya autentiseringsuppgifter för distribution i rutorna och klicka på Spara.

Du kan använda dessa autentiseringsuppgifter för att klona platsen med git eller konfigurera automatiserad distribution från GitHub, TFS eller ett.  Mer information finns i [Azure App Service dokumentationen].

### <a name="appsettings"></a>Programinställningar
De flesta inställningar för en migrerad Mobiltjänst finns tillgängliga via App-inställningar.  Du kan hämta en lista över app-inställningar från den [Azure-portalen].
Visa eller ändra inställningarna för appen:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Inställningsbladet öppnas som standard.
4. Klicka på **programinställningar** i menyn Allmänt.
5. Rulla ned till avsnittet App-inställningar och hitta appinställning.
6. Klicka på värdet för appinställningen redigera värdet.  Klicka på **spara** att spara värdet.

Du kan uppdatera flera appinställningar samtidigt.

> [!TIP]
> Det finns två inställningar för program med samma värde.  Du kan till exempel se *ApplicationKey* och *MS\_ApplicationKey*.  Uppdatera båda programinställningar på samma gång.
>
>

### <a name="authentication"></a>Autentisering
Alla inställningar för autentisering är tillgängliga som App-inställningar på din migrerade plats.  För att uppdatera autentiseringsinställningarna för, måste du ändra appinställningarna för lämplig.  I följande tabell visas lämpliga app-inställningar för din autentiseringsprovider:

| Leverantör | Klient-ID | Klienthemlighet | Andra inställningar |
|:--- |:--- |:--- |:--- |
| Microsoft-konto |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Obs: **MS\_AadTenants** lagras som en kommaavgränsad lista över klient domäner (fälten ”tillåtna klienter” i Mobile Services-portalen).

> [!WARNING]
> **Använd inte autentiseringsmekanismer i menyn Inställningar**
>
> Azure App Service tillhandahåller ett separat ”ingen kod” autentisering och auktorisering system under den *autentisering / auktorisering* menyn Inställningar och (föråldrad) *Mobile autentisering* alternativet under menyn Inställningar.  Dessa alternativ är inte kompatibel med en migrerade Azure-mobiltjänst.  Du kan [uppgraderar platsen](app-service-mobile-net-upgrading-from-mobile-services.md) dra nytta av Azure App Service-autentisering.
>
>

### <a name="easytables"></a>Data
Den *Data* fliken i Mobile Services har ersatts av *enkelt tabeller* i Azure-portalen.  Öppna enkelt tabeller:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Inställningsbladet öppnas som standard.
4. Klicka på **enkelt tabeller** mobila-menyn.

Du kan lägga till en tabell genom att klicka på den **Lägg till** knappen eller få åtkomst till dina befintliga tabeller genom att klicka på ett tabellnamn.  Det finns olika åtgärder som du kan göra från det här bladet, inklusive:

* Ändra tabellbehörigheter
* Redigera operativa skript
* Hantera tabellens schema
* Ta bort tabellen
* Rensa innehåll
* Ta bort specifika rader i tabellen

### <a name="easyapis"></a>API
Den *API* fliken i Mobile Services har ersatts av *enkelt API: er* i Azure-portalen.  Öppna enkelt API: er:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Inställningsbladet öppnas som standard.
4. Klicka på **enkelt API: er** mobila-menyn.

Dina migrerade API: er finns redan i bladet.  Du kan också lägga till en API från det här bladet.  Klicka på API: et för att hantera en specifika API: et.
Du kan justera behörigheterna och redigera skripten för API: et från det nya bladet.

### <a name="on-demand-jobs"></a>Jobb för Schemaläggaren
Alla jobb som Schemaläggaren är tillgängliga via avsnittet Scheduler-Jobbsamlingar.  Åtkomst till dina jobb för Schemaläggaren:

1. Logga in på [Azure-portalen].
2. Välj **Bläddra >**, ange **schema** i den *Filter* och sedan markera **Scheduler samlingar**.
3. Välj Jobbsamlingen för webbplatsen.  Det heter *sitename*-jobb.
4. Klicka på **inställningar**.
5. Klicka på **schemaläggare** under hantera.

Schemalagda jobb visas med den frekvens som du angett innan migreringen.  På begäran-jobben är inaktiverade.  Köra ett jobb på begäran:

1. Välj det jobb som du vill köra.
2. Om det behövs klickar du på **aktivera** att aktivera jobbet.
3. Klicka på **inställningar**, sedan **schema**.
4. Välj en upprepning av **när**, klicka på **spara**

Jobb på begäran finns i `App_Data/config/scripts/scheduler post-migration`.  Vi rekommenderar att du konverterar alla jobb för på-begäran till [WebJobs] eller [funktioner].  Skriva nya schemaläggare som [WebJobs] eller [funktioner].

### <a name="notification-hubs"></a>Notification Hubs
Mobile Services använder Notification Hubs för push-meddelanden.  Följande App-inställningar används för att länka Meddelandehubben till din Mobiltjänst efter migreringen:

| Tillämpningsinställning | Beskrivning |
|:--- |:--- |
| **MS\_PushEntityNamespace** |Namespace för Notification Hub |
| **MS\_NotificationHubName** |Namnet på Meddelandehubb |
| **MS\_NotificationHubConnectionString** |Anslutningssträngen för Notification Hub |
| **MS\_NamespaceName** |Ett alias för MS_PushEntityNamespace |

Din Meddelandehubb hanteras via den [Azure-portalen].  Notera namnet Notification Hub (du hittar detta med hjälp av App-inställningar):

1. Logga in på [Azure-portalen].
2. Välj **Bläddra**> och välj **Notification Hubs**
3. Klicka på Notification Hub-namn som associeras med den mobila tjänsten.

> [!NOTE]
> Om din Meddelandehubb är av typen ”Mixed”, är den inte synlig.  ”Blandade” Skriv notification hubs använder både Meddelandehubbar och äldre Service Bus-funktioner.  [Omvandla dina blandat namnområden] innan du fortsätter.  När konverteringen har slutförts, din meddelandehubb som visas i den [Azure-portalen].
>
>

Mer information finns i [Meddelandehubbar] dokumentation.

> [!TIP]
> Notification Hubs-hanteringsfunktioner i den [Azure-portalen] är fortfarande under förhandsgranskning.  Den [klassiska Azure-portalen] finns kvar för att hantera alla Meddelandehubbar.
>
>

### <a name="legacy-push"></a>Äldre Push-inställningar
Om du har konfigurerat Push på mobiltjänsten före införandet på Notification Hubs använder du *äldre push*.  Om du använder Push och du inte ser en Meddelandehubb som visas i konfigurationen och sedan är det troligt att du använder *äldre push*.  Den här funktionen har migrerats med andra funktioner.  Vi rekommenderar dock att du uppgraderar till Meddelandehubbar strax efter att migreringen är klar.

Under tiden kan alla äldre push-inställningar (med viktiga undantag av APN-certifikatet) som är tillgängliga i App-inställningar.  Uppdatera APN-certifikatet genom att ersätta filen på filsystemet.

### <a name="app-settings"></a>Andra Appinställningar
Följande ytterligare appinställningar har migrerats från din Mobiltjänst och är tillgängliga under *inställningar* > *Appinställningar*:

| Tillämpningsinställning | Beskrivning |
|:--- |:--- |
| **MS\_MobileServiceName** |Namnet på din app |
| **MS\_MobileServiceDomainSuffix** |Domänprefixet. engångsfaktorautentisering azure-mobile.net |
| **MS\_ApplicationKey** |Din nyckel för programmet |
| **MS\_MasterKey** |Huvudnyckeln för din app |

Program-tangenten och huvudnyckeln är identiska med nycklar för programmet från din ursprungliga mobiltjänst.  I synnerhet skickas nyckeln för programmet av mobila klienter att verifiera deras användning av mobila API: et.

### <a name="cliequivalents"></a>Kommandoradsverktyget motsvarigheter
Du kan använda längre den *azure mobila* kommando för att hantera Azure Mobile Services-platsen.  I stället många funktioner har ersatts av den *azure plats* kommando.  Använd följande tabell för att hitta motsvarigheter för vanliga kommandon:

| *Azure Mobile* kommando | Motsvarande *Azure Site* kommando |
|:--- |:--- |
| mobila platser |plats för webbplatslista |
| mobila lista |platslista |
| mobila visa *namn* |platsen visa *namn* |
| mobila omstart *namn* |platsen omstart *namn* |
| mobila Omdistributionen *namn* |Distribuera om distributionen av site *commitId* *namn* |
| mobila nyckeluppsättning *namn* *typen* *värde* |ta bort platsen appsetting *nyckeln* *namn* <br/> Lägg till webbplats appsetting *nyckeln*=*värdet* *namn* |
| mobila config listan *namn* |appsetting platslista *namn* |
| Hämta mobila config *namn* *nyckel* |platsen appsetting visa *nyckeln* *namn* |
| mobila konfigurationsuppsättning *namn* *nyckel* |ta bort platsen appsetting *nyckeln* *namn* <br/> Lägg till webbplats appsetting *nyckeln*=*värdet* *namn* |
| mobila domänlistan *namn* |domän platslista *namn* |
| lägga till mobila domän *namn* *domän* |Lägg till webbplats domän *domän* *namn* |
| ta bort mobila domän *namn* |ta bort plats-domänen *domän* *namn* |
| Visa mobila skala *namn* |platsen visa *namn* |
| mobila skala ändra *namn* |läget för webbplatsen skala *läge* *namn* <br /> platsen skala instanser *instanser* *namn* |
| mobila appsetting listan *namn* |appsetting platslista *namn* |
| Lägg till mobila appsetting *namn* *nyckeln* *värde* |Lägg till webbplats appsetting *nyckeln*=*värdet* *namn* |
| ta bort mobila appsetting *namn* *nyckel* |ta bort platsen appsetting *nyckeln* *namn* |
| mobila appsetting visa *namn* *nyckel* |ta bort platsen appsetting *nyckeln* *namn* |

Uppdatera autentisering eller inställningar för push-meddelanden genom att uppdatera inställningarna för programmet.
Redigera filerna och publicera webbplatsen via FTP- eller git.

### <a name="diagnostics"></a>Diagnostik- och loggning
Diagnostikloggning är normalt inaktiverat i en Azure App Service.  Så här aktiverar du diagnostikloggning:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Inställningsbladet öppnas som standard.
4. Välj **diagnostikloggar** funktioner-menyn.
5. Klicka på **ON** för följande loggar: **programloggning (filsystem)**, **detaljerade felmeddelanden**, och **spårning av misslyckade begäranden**
6. Klicka på **filsystemet** för Web server-loggning
7. Klicka på **Spara**

Visa loggarna:

1. Logga in på [Azure-portalen].
2. Välj **alla resurser** eller **Apptjänster** klicka sedan på namnet på den migrerade mobiltjänst.
3. Klicka på den **verktyg** knappen
4. Välj **loggström** Följ-menyn.

Loggarna visas i fönstret när de skapas.  Du kan också hämta loggarna för senare analys med dina autentiseringsuppgifter för distribution. Mer information finns i [loggning] dokumentation.

## <a name="known-issues"></a>Kända problem
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Om du tar bort en migreras Mobile App klon orsakar ett avbrott för platsen
Om du klona migrerade mobiltjänsten med hjälp av Azure PowerShell och sedan ta bort klonen, tas DNS-posten för produktionstjänsten bort.  Platsen är inte längre vara tillgänglig från Internet.  

Lösning: Om du vill klona webbplatsen göra det via portalen.

### <a name="changing-webconfig-does-not-work"></a>Om du ändrar Web.config fungerar inte
Om du har en ASP.NET-webbplats kan ändras till den `Web.config` filen inte tillämpas.  Azure App Service skapar ett lämpligt `Web.config` filen starten för att stödja Mobile Services runtime.  Du kan åsidosätta vissa inställningar (t.ex anpassade huvuden) med hjälp av en XML-transformation.  Skapa en fil i kallas `applicationHost.xdt` -filen måste hamna på den `D:\home\site` på Azure-tjänsten.  Överför den `applicationHost.xdt` filen via ett anpassat distributionsskriptet eller direkt via Kudu.  Nedan visas ett exempel dokument:

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

Mer information finns i [XDT transformera prover] dokumentation på GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Migrerade Mobile Services kan inte läggas till i Traffic Manager
Du kan inte direkt att välja en migrerade Mobiltjänst för profilen när du skapar en Traffic Manager-profil.  Använda en ”extern slutpunkt”.  Den externa slutpunkten kan endast läggas till via PowerShell.  Mer information finns i [Traffic Manager kursen](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Nästa steg
Nu när ditt program har migrerats till App Service finns ytterligare funktioner som du kan använda:

* Distribution [mellanlagring fack] kan du mellanlagra ändringar på webbplatsen och genomföra A / B-testning.
* [WebJobs] Ange en ersättning för på-begäran schemalagda jobb.
* Du kan [kontinuerligt distribuera] webbplatsen genom att länka din webbplats till GitHub, TFS eller ett.
* Du kan använda [Programinsikter] att övervaka din webbplats.
* Hantera en webbplats och ett Mobile-API från samma kod.

### <a name="upgrading-your-site"></a>Uppgradera din Mobile Services-plats till Azure Mobile Apps-SDK
* För Node.js-baserad server projekt, den nya [Mobile Apps Node.js SDK] innehåller flera nya funktioner. Exempelvis kan du nu göra lokal utveckling och felsökning, använda en Node.js-version ovan 0.10 och anpassa med någon Express.js mellanprogram.
* För. NET-baserad server-projekt, den nya [Mobile Apps SDK NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) har du mer flexibilitet i NuGet-beroenden.  Paketen stöd för den nya App Service-autentiseringen och skapa med ASP.NET-projekt. Mer information om hur du uppgraderar finns [uppgradera din befintliga mobiltjänst .NET till App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Priser för Apptjänst]: https://azure.microsoft.com/pricing/details/app-service/
[Programinsikter]: ../application-insights/app-insights-overview.md
[Autoskala]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-web-overview.md
[klassiska Azure-portalen]: https://manage.windowsazure.com
[Azure-portalen]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[Azure Scheduler planer]: ../scheduler/scheduler-plans-billing.md
[kontinuerligt distribuera]: ../app-service/app-service-continuous-deployment.md
[Omvandla dina blandat namnområden]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[anpassade domännamn]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: http://www.telerik.com/fiddler
[allmän tillgång till Azure App Service]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[loggning]: ../app-service/web-sites-enable-diagnostic-log.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[jämfört med Mobile Services. Apptjänst]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Meddelandehubbar]: ../notification-hubs/notification-hubs-push-notification-overview.md
[prestandaövervakning]: ../app-service/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[mellanlagring fack]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[XDT transformera prover]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[funktioner]: ../azure-functions/functions-overview.md
