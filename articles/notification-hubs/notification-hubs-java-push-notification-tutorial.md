---
title: Så här använder du Azure Notification Hubs med Java
description: Läs om hur du använder Azure Notification Hubs från en Java-backend.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d48973cc7c5ed1fc7ae3f96128d488f3f1df3a05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263871"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Så här använder du meddelandehubbar från Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

I det här avsnittet beskrivs de viktigaste funktionerna i den nya officiella Azure Notification Hub Java SDK som stöds.
Det här projektet är ett projekt med öppen källkod och du kan visa hela SDK-koden på [Java SDK].

I allmänhet kan du komma åt alla Notification Hubs-funktioner från en Java/PHP/Python/Ruby-backend med hjälp av REST-gränssnittet Notification Hub enligt beskrivningen i [MSDN-avsnittet Notification Hubs REST API:er](https://msdn.microsoft.com/library/dn223264.aspx). Denna Java SDK ger ett tunt omslag över dessa REST-gränssnitt i Java.

SDK stöder för närvarande:

* CRUD om meddelandehubbar
* CRUD om registreringar
* Installationshantering
* Importera/exportera registreringar
* Regelbundna sändnings-
* Schemalagda sändningar
* Async-verksamhet via Java NIO
* Plattformar som stöds: APNS (iOS), FCM (Android), WNS (Windows Store-appar), MPNS(Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android utan Google-tjänster)

## <a name="sdk-usage"></a>SDK-användning

### <a name="compile-and-build"></a>Kompilera och bygga

Använd [Maven]

Så här bygger du:

    mvn package

## <a name="code"></a>Kod

### <a name="notification-hub-cruds"></a>CRUD-meddelanden för meddelandehubben

**Skapa en NamespaceManager:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Skapa meddelandehubb:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 ELLER

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Hämta aviseringshubben:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Hub för uppdateringsmeddelanden:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Ta bort meddelandehubben:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Crud registrering

**Skapa en Notification Hub-klient:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Skapa Windows-registrering:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**Skapa iOS-registrering:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

På samma sätt kan du skapa registreringar för Android (FCM), Windows Phone (MPNS) och Kindle Fire (ADM).

**Skapa mallregistreringar:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Skapa registreringar med hjälp av skapa registrerings-ID + upsert mönster:**

Tar bort dubbletter på grund av eventuella förlorade svar om registrerings-ID:et lagras på enheten:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Uppdatera registreringar:**

    ```java
    hub.updateRegistration(reg);
    ```

**Ta bort registreringar:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Frågeregistreringar:**

* **Få en enda registrering:**

    ```java
    hub.getRegistration(regid);
    ```

* **Få alla registreringar i navet:**

    ```java
    hub.getRegistrations();
    ```

* **Få registreringar med tagg:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Få registreringar per kanal:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Alla samlingsfrågor stöder $top och fortsättningstoken.

### <a name="installation-api-usage"></a>Användning av installations-API

Installations-API är en alternativ mekanism för registreringshantering. Istället för att upprätthålla flera registreringar, som inte är triviala och kan enkelt göras felaktigt eller ineffektivt, är det nu möjligt att använda en enda installation objekt.

Installationen innehåller allt du behöver: push-kanal (enhetstoken), taggar, mallar, sekundära paneler (för WNS och APNS). Du behöver inte ringa tjänsten för att få ID längre - bara generera GUID eller någon annan identifierare, hålla den på enheten och skicka till din backend tillsammans med push-kanal (enhet token).

På backend bör du bara göra `CreateOrUpdateInstallation`ett enda samtal till ; det är helt idempotent, så känn dig fri att försöka om det behövs.

Som exempel för Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Om du vill uppdatera den:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

För avancerade scenarier använder du funktionen partiell uppdatering, vilket gör det möjligt att ändra endast vissa egenskaper för installationsobjektet. Partiell uppdatering är delmängd av JSON Patch-åtgärder som du kan köra mot installationsobjektet.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Ta bort installation:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`och `Delete` är så `Get`småningom förenliga med . Den begärda åtgärden går bara till systemkön under samtalet och körs i bakgrunden. Get är inte utformad för huvudkörningsscenariot, men bara för felsökning och felsökning är det hårt begränsat av tjänsten.

Skicka flöde för installationer är samma som för registreringar. Så här riktar du meddelande till den aktuella installationen – använd bara taggen "InstallationId:{desired-id}". I det här fallet är koden:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

För en av flera mallar:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Schemalägg meddelanden (tillgängligt för STANDARD-nivå)

Samma som vanlig skicka men med en ytterligare parameter - scheduledTime, som säger när anmälan ska levereras. Tjänsten accepterar någon tidpunkt mellan nu + 5 minuter och nu + 7 dagar.

**Schemalägg ett inbyggt Windows-meddelande:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Import/export (tillgängligt för STANDARD-nivå)

Du kan behöva utföra massåtgärder mot registreringar. Vanligtvis är det för integration med ett annat system eller en massiv fix för att uppdatera taggarna. Vi rekommenderar inte att du använder flödet Hämta/uppdatera om tusentals registreringar är inblandade. Systemets import-/exportkapacitet är utformad för att täcka scenariot. Du ger åtkomst till en blob-behållare under ditt lagringskonto som en källa till inkommande data och plats för utdata.

**Skicka ett exportjobb:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Skicka ett importjobb:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Vänta tills ett jobb är klart:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Få alla jobb:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI med SAS-signatur:**

 Den här URL:en är url:en till en blob-fil eller blob-behållare plus en uppsättning parametrar som behörigheter och förfallotid plus signatur för alla dessa saker som gjorts med kontots SAS-nyckel. Azure Storage Java SDK har omfattande funktioner, inklusive skapandet av dessa URI:er. Som enkelt alternativ, ta `ImportExportE2E` en titt på testklassen (från GitHub-platsen) som har grundläggande och kompakt implementering av signeringsalgoritm.

### <a name="send-notifications"></a>Skicka meddelanden

Notification-objektet är helt enkelt en brödtext med rubriker, vissa verktygsmetoder hjälper till att skapa inbyggda och mallmeddelanden objekt.

* **Windows Store och Windows Phone 8.1 (ej Silverlight)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 och 8.1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Kindle Brand**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Skicka till taggar**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Skicka till tagguttryck**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Skicka mallavisering**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Om du kör Java-koden ska du nu skapa ett meddelande som visas på målenheten.

## <a name="next-steps"></a><a name="next-steps"></a>Nästa steg

Det här avsnittet visade hur du skapar en enkel Java REST-klient för Notification Hubs. Här kan göra du följande:

* Ladda ner hela [Java SDK], som innehåller hela SDK-koden.
* Spela med proverna:
  * [Komma igång med meddelandehubbar]
  * [Skicka nyheter]
  * [Skicka lokaliserade nyheter]
  * [Skicka meddelanden till autentiserade användare]
  * [Skicka meddelanden över flera plattformar till autentiserade användare]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Komma igång med meddelandehubbar]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Skicka nyheter]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Skicka lokaliserade nyheter]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Skicka meddelanden till autentiserade användare]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Skicka meddelanden över flera plattformar till autentiserade användare]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
