---
title: Hur du använder Notification Hubs med Java
description: Lär dig hur du använder Azure Notification Hubs från en Java-serverdel.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 45ff4ff79a443e49a7d01a41a249db9e4d0a070b
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560812"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Hur du använder Notification Hubs från Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Det här avsnittet beskriver de viktigaste funktionerna i den nya stöds fullt ut officiella Azure Notification Hub Java SDK.
Det här projektet är ett projekt med öppen källkod och du kan visa hela SDK-koden på [Java SDK].

I allmänhet du kan komma åt alla funktioner i Meddelandehubbar från en Java/PHP/Python/Ruby-serverdel med Notification Hub REST-gränssnittet enligt beskrivningen i avsnittet om MSDN [Notification Hubs REST API: er](https://msdn.microsoft.com/library/dn223264.aspx). Den här Java SDK tillhandahåller en tunn omslutning över dessa REST-gränssnitt i Java.

SDK stöder för närvarande:

* CRUD på Meddelandehubbar
* CRUD för registreringar
* Hantering av installation
* Import/Export-registreringar
* Vanliga skickar
* Schemalagda skickar
* Asynkrona åtgärder via Java NIO
* Plattformar som stöds: APNS (iOS), FCM (Android), WNS (Windows Store-appar), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android utan Google-tjänster)

## <a name="sdk-usage"></a>SDK-användning

### <a name="compile-and-build"></a>Kompilera och bygga

Använd [Maven 3.]

Att skapa:

    mvn package

## <a name="code"></a>Kod

### <a name="notification-hub-cruds"></a>Notification Hub CRUDs

**Skapa en NamespaceManager:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Skapa Meddelandehub:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 ELLER

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Hämta Notification Hub:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Uppdatera Notification Hub:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Ta bort Notification Hub:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Registrering CRUDs

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

**Skapa registreringar med skapa registrerings-ID + upsert mönster:**

Tar bort dubbletter på grund av eventuella förlorad svar om lagra registrerings-ID på enheten:

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

**Fråga efter registreringar:**

* **Få enkel registrering:**

    ```java
    hub.getRegistration(regid);
    ```

* **Hämta alla registreringar i hubben:**

    ```java
    hub.getRegistrations();
    ```

* **Hämta registreringar med tagg:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Hämta registreringar per kanal:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Alla samlingsfrågor stöder $top och fortsättning token.

### <a name="installation-api-usage"></a>Installation av API-användning

API-installationen är en alternativ mekanism för registreringshantering av. I stället för att underhålla flera registreringar som inte är enkelt och kan enkelt utföras felaktigt eller ineffektiv, är det nu möjligt att använda en enda Installation-objektet.

Installationen innehåller allt du behöver: push-kanal (enhetstoken), taggar, mallar, sekundära paneler (för WNS och APN). Du behöver inte anropa tjänsten för att hämta ID längre – bara generera GUID eller andra identifierare, förvara den på enheten och skicka till din serverdel tillsammans med push-kanal (enhetstoken).

På servern, bör du endast göra ett enda anrop till `CreateOrUpdateInstallation`; det helt är idempotenta, så passa på att försöka igen om det behövs.

Som exempel Amazon Kindle Fire:

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

Använd funktionen för deluppdatering, vilket gör för att ändra specifika egenskaper för objektet installation för avancerade scenarier. Deluppdatering är delmängd av JSON-korrigeringen åtgärder du kan köra mot objektet för Installation.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Ta bort installationen:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`, och `Delete` är konsekvent med `Get`. Den begärda åtgärden leder till kön system under samtalet bara och körs i bakgrunden. Hämta är inte avsedd för huvudsakliga runtime-scenario, men bara för felsökning och felsökning, den är nära begränsad av tjänsten.

Skicka flödet för installationer är desamma som för registreringar. Mål-meddelande till viss installationen – Använd bara tagg ”InstallationId: {desired-id}”. För det här fallet är koden:

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

### <a name="schedule-notifications-available-for-standard-tier"></a>Schemalägg meddelanden (tillgängligt för nivån STANDARD)

Samma som vanlig skicka men med en extra parameter - scheduledTime som innehåller texten när meddelandet ska levereras. Tjänsten accepterar helst mellan nu + 5 minuter och nu + 7 dagar.

**Schemalägg en intern Windows-meddelande:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Import/Export (tillgängligt för nivån STANDARD)

Du kan behöva utföra bulkåtgärd mot registreringar. Det är vanligtvis för integrering med ett annat system eller en omfattande lösning för att uppdatera taggar. Vi rekommenderar inte hämta/uppdatera flödet om tusentals registreringar ingår. Systemets Import/Export-funktionen har utformats för att täcka scenariot. Du ger åtkomst till en blob-behållare under ditt storage-konto som en källa för inkommande data och plats för utdata.

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

**Vänta tills ett jobb är klar:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Hämta alla jobb:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI: N med SAS-signaturen:**

 Denna URL är Webbadressen till en blobfil eller blob-behållare plus en uppsättning parametrar som behörigheter och förfallotid samt signaturen för alla dessa saker som gjordes med kontots SAS-nyckel. Azure Storage-Java-SDK har omfattande funktioner, inklusive hur du skapar dessa URI: er. Enkla alternativ är att ta en titt på de `ImportExportE2E` testa klass (från platsen som GitHub) som har en basic- och compact implementering av Signeringsalgoritm.

### <a name="send-notifications"></a>Skicka meddelanden

Meddelande-objektet är helt enkelt en brödtext med rubriker, vissa verktygsmetoderna hjälp med att skapa objekt för interna och mallen meddelanden.

* **Windows Store och Windows Phone 8.1 (utan Silverlight)**

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

* **Kindle Fire**

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

* **Skicka till Tagguttryck**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Skicka**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Köra Java-kod ska nu skapa ett meddelande som visas på din målenhet.

## <a name="next-steps"></a>Nästa steg

Det här avsnittet såg du hur du skapar en enkel Java REST-klient för Meddelandehubbar. Härifrån kan du:

* Ladda ned hela [Java SDK], som innehåller hela SDK-koden.
* Experimentera med exemplen:
  * [Kom igång med Notification Hubs]
  * [Skicka de senaste nyheterna]
  * [Skicka lokaliserade de senaste nyheterna]
  * [Skicka meddelanden till autentiserade användare]
  * [Skicka plattformsoberoende meddelanden till autentiserade användare]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Kom igång med Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Skicka de senaste nyheterna]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Skicka lokaliserade de senaste nyheterna]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Skicka meddelanden till autentiserade användare]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Skicka plattformsoberoende meddelanden till autentiserade användare]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven 3.]: http://maven.apache.org/
