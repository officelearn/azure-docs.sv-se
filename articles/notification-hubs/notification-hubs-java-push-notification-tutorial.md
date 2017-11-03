---
title: "Hur du använder Notification Hubs med Java"
description: "Lär dig hur du använder Azure Notification Hubs från Java backend."
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 41f978750ddef9f7e878c65b0017e909720154aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-notification-hubs-from-java"></a>Hur du använder Notification Hubs från Java
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Det här avsnittet beskrivs viktiga funktioner i den nya stöds fullt ut officiella Azure Notification Hub Java SDK. Det här är ett projekt med öppen källkod och du kan visa hela SDK-koden i [Java SDK]. 

I allmänhet kan du har åtkomst till alla funktioner i Notification Hubs från en Java/PHP/Python/Ruby serverdel med Notification Hub REST-gränssnitt som beskrivs i avsnittet MSDN [Notification Hub REST API: er](http://msdn.microsoft.com/library/dn223264.aspx). Den här Java SDK ger en tunn omslutning över dessa REST-gränssnitt i Java. 

SDK stöder för närvarande:

* CRUD på Notification hub 
* CRUD på registreringar
* Hantering av installation
* Importera och exportera registreringar
* Vanliga skickar
* Schemalagda skickar
* Asynkrona åtgärder via Java NIO
* Plattformar som stöds: APN (iOS), GCM (Android), WNS (Windows Store-appar), MPNS (Windows Phone), ADM (Amazon Kindle brand), Baidu (Android utan Google services) 

## <a name="sdk-usage"></a>SDK-användning
### <a name="compile-and-build"></a>Kompilera och bygga
Använd [Maven]

Att skapa:

    mvn package

## <a name="code"></a>Kod
### <a name="notification-hub-cruds"></a>Notification Hub CRUDs
**Skapa en NamespaceManager:**

    NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Skapa Notification Hub:**

    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);

 ELLER

    hub = new NotificationHub("connection string", "hubname");

**Hämta Meddelandehubben:**

    hub = namespaceManager.getNotificationHub("hubname");

**Uppdatera Meddelandehubben:**

    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);

**Ta bort Meddelandehubben:**

    namespaceManager.deleteNotificationHub("hubname");

### <a name="registration-cruds"></a>Registrering CRUDs
**Skapa en Notification Hub-klient:**

    hub = new NotificationHub("connection string", "hubname");

**Skapa Windows-registrering:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");    
    hub.createRegistration(reg);

**Skapa iOS-registrering:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

På liknande sätt kan du skapa registreringar för Android (GCM), Windows Phone (MPNS) och Kindle brand (ADM).

**Skapa mallen registreringar:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**Skapa registreringar med skapa registrationid + upsert mönster**

Tar bort dubbletter på grund av eventuella förlorade svar om ID: n som lagras på enheten:

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**Uppdatera registreringar:**

    hub.updateRegistration(reg);

**Ta bort registreringar:**

    hub.deleteRegistration(regid);

**Fråga registreringar:**

* **Hämta enkel registrering:**
  
    hub.getRegistration(regid);
* **Visa alla registreringar hubb:**
  
    hub.getRegistrations();
* **Hämta registreringar med tagg:**
  
    hub.getRegistrationsByTag("myTag");
* **Hämta registreringar per kanal:**
  
    hub.getRegistrationsByChannel("devicetoken");

Alla samlingsfrågor stöder $top och fortsättning token.

### <a name="installation-api-usage"></a>Installation av API-användning
API-installationen är en alternativ metod för hantering av registreringen. I stället för att underhålla flera registreringar som inte är trivial och enkelt kan göras felaktigt eller ineffektiv går nu att använda en enda Installation-objekt. Installationen innehåller allt du behöver: push-kanal (enhetstoken), taggar, mallar, sekundära paneler (för WNS och APN). Behöver du inte att anropa tjänsten för att hämta Id längre – bara generera GUID eller andra identifierare, hålla på enheten och skicka till din serverdel tillsammans med push-kanal (enhetstoken). På serverdelen ska du bara göra en enda anrop: CreateOrUpdateInstallation, är det fullständigt idempotent, så passa på att försöka igen om det behövs.

Exempel för Amazon Kindle brand ser ut så här:

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

Om du vill uppdatera den: 

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

För avancerade scenarier har vi deluppdatering funktion som gör för att ändra endast särskilda egenskaper i objektet installation. I princip är deluppdatering delmängd av JSON-korrigering åtgärder du kan köra mot objektet för Installation.

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Ta bort installationen:

    hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, korrigeringsfil och ta bort är överensstämmelse med Get. Den begärda åtgärden kan bara flyttas till kön systemet under anropet och körs i bakgrunden. Observera att hämta inte är avsedd för huvudsakliga runtime scenariot men bara för felsökning och felsökning, begränsas nära av tjänsten.

Skicka flödet för installationer är desamma som för registreringar. Vi har precis introducerat möjlighet till mål meddelande till viss installationen - bara använda taggen ”InstallationId: {desired-id}”. För ovanstående fall det skulle se ut så här:

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

För en av flera mallar:

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>Schemalägga meddelanden (tillgängligt för standardnivån)
Samma som regelbundet skicka men med en ytterligare parameter - scheduledTime som säger när meddelanden ska levereras. Tjänsten accepterar helst mellan nu + 5 minuter och nu + 7 dagar.

**Schemalägg en intern Windows-avisering:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);    
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>Importera och exportera (tillgängligt för standardnivån)
Ibland är det krävs för att utföra massredigering mot registreringar. Vanligtvis är den för integrering med en annan dator eller en omfattande lösning att säga uppdatera taggarna. Det rekommenderas starkt inte att använda Get/uppdatera flödet om vi pratar om tusentals registreringar. Import/Export-funktionen är avsedd att täcka scenariot. I praktiken ger en åtkomst till vissa blob-behållare under ditt lagringskonto som en källa för inkommande data och plats för utdata.

**Skicka exportjobb:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**Skicka importjobbet:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);

**Vänta tills jobbet är klar:**

    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }       

**Hämta alla jobb:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**URI: N med SAS-signatur:** detta är Webbadressen till vissa blob-fil eller blob-behållaren plus uppsättning parametrar som behörigheter och förfallotid plus signaturen för dessa saker som skapats med hjälp av kontots SAS-nyckel. Azure Storage Java SDK: N har omfattande funktioner inklusive skapandet av denna typ av URI: er. Du kan ta en titt på ImportExportE2E TestKlass (från platsen som github) som har en mycket enkel och compact implementering av Signeringsalgoritm som enkelt alternativ.

### <a name="send-notifications"></a>Skicka meddelanden
Meddelande-objekt är helt enkelt en brödtext med rubriker, vissa metoder att skapa objekt för intern och mallen för meddelanden.

* **Windows Store och Windows Phone 8.1 (utan Silverlight)**
  
        String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
        Notification n = Notification.createWindowsNotification(toast);
        hub.sendNotification(n);
* **iOS**
  
        String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
        Notification n = Notification.createAppleNotification(alert);
        hub.sendNotification(n);
* **Android**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createGcmNotification(message);
        hub.sendNotification(n);
* **Windows Phone 8.0 och 8.1 Silverlight**
  
        String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>Hello from Java!</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
        Notification n = Notification.createMpnsNotification(toast);
        hub.sendNotification(n);
* **Kindle brand**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **Skicka till taggar**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **Skicka etikettuttrycket**       
  
        hub.sendNotification(n, "foo && ! bar");
* **Skicka meddelande för mallen**
  
        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

Kör Java-kod ska nu skapa ett meddelande som visas på målenheten.

## <a name="next-steps"></a>Nästa steg
Vi visar hur du skapar en enkel RESTEN av Java-klient för Meddelandehubbar i det här avsnittet. Härifrån kan du:

* Hämta fullständigt [Java SDK], som innehåller hela SDK-koden. 
* Spela upp med exemplen:
  * [Kom igång med Notification Hubs]
  * [Skicka de senaste nyheterna]
  * [Skicka lokaliserade senaste nyheterna]
  * [Skicka meddelanden till autentiserade användare]
  * [Skicka plattformsoberoende meddelanden till autentiserade användare]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Kom igång med Notification Hubs]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Skicka de senaste nyheterna]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Skicka lokaliserade senaste nyheterna]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Skicka meddelanden till autentiserade användare]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Skicka plattformsoberoende meddelanden till autentiserade användare]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/

