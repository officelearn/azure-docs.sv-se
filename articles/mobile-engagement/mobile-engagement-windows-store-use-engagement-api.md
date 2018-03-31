---
title: Hur du använder Engagement API på Windows Universal
description: Hur du använder Engagement API på Windows Universal
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: bb501fca-9cfe-4495-81df-b5efd6e0137b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4f8f211764646bc53319f435d74a16a026329039
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-windows-universal"></a>Hur du använder Engagement API på Windows Universal
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Det här dokumentet är ett tillägg till dokumentet [hur du integrerar Engagement för universella Windows-](mobile-engagement-windows-store-integrate-engagement.md): ger i djup information om hur du använder Engagement API för att rapportera programmet-statistik.

Kom ihåg att om du bara vill Engagement att rapportera programmets sessioner, aktiviteter, krascher och teknisk information sedan det enklaste sättet är att se alla dina `Page` underordnade klasser ärver från den `EngagementPage` klass.

Om du vill göra mer, till exempel om du behöver rapportera programmet specifika händelser, fel och jobb, eller om du vill rapportera ditt programs aktiviteter i ett annat sätt än den som implementerats i den `EngagementPage` klasser, måste du använda Engagement-API.

Engagement-API som tillhandahålls av den `EngagementAgent` klass. Du har åtkomst till de här metoderna via `EngagementAgent.Instance`.

Även om modulen agent inte har initierats, varje anrop till API: et skjuts upp och köras igen när agenten är tillgänglig.

## <a name="engagement-concepts"></a>Koncept i engagement
Följande delar förfina vanliga [koncept i Mobile Engagement](mobile-engagement-concepts.md) för universella Windows-plattformen.

### <a name="session-and-activity"></a>`Session` och `Activity`
En *aktiviteten* beror vanligtvis på en sida av programmet, det vill säga den *aktiviteten* när sidan visas och stoppas när sidan stängs: så är fallet när Engagement-SDK är integrerad med hjälp av den `EngagementPage` klass.

Men *aktiviteter* kan också kontrolleras manuellt med hjälp av Engagement-API. På så sätt kan du dela upp en viss sida i flera sub delar för att få mer information om användning av den här sidan (till exempel vill veta hur ofta och hur länge dialogrutor används i den här sidan).

## <a name="reporting-activities"></a>Rapportering
### <a name="user-starts-a-new-activity"></a>Användaren startar en ny aktivitet
#### <a name="reference"></a>Referens
            void StartActivity(string name, Dictionary<object, object> extras = null)

Du måste anropa `StartActivity()` varje gång aktiviteten användarändringar. Det första anropet till funktionen startar en ny session.

> [!IMPORTANT]
> SDK anropar metoden EndActivity automatiskt när programmet stängs. Därför rekommenderas att anropa metoden StartActivity när aktiviteten ändringar som användaren och till aldrig anropa metoden EndActivity eftersom den här metoden anropas tvingar den aktuella sessionen avslutas.
> 
> 

#### <a name="example"></a>Exempel
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Användaren avslutar sin aktuella aktiviteten
#### <a name="reference"></a>Referens
            void EndActivity()

Detta avslutar aktiviteten och sessionen. Du bör inte anropa den här metoden om du inte verkligen vet vad du gör.

#### <a name="example"></a>Exempel
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Rapportering av jobb
### <a name="start-a-job"></a>Starta ett jobb
#### <a name="reference"></a>Referens
            void StartJob(string name, Dictionary<object, object> extras = null)

Du kan använda jobbet för att spåra vissa aktiviteter under en viss tidsperiod.

#### <a name="example"></a>Exempel
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Avsluta ett jobb
#### <a name="reference"></a>Referens
            void EndJob(string name)

När en aktivitet som spåras av ett jobb har avslutats, bör du anropa metoden EndJob för det här jobbet genom att ange namnet på det jobb.

#### <a name="example"></a>Exempel
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Rapporteringshändelser
Det finns tre typer av händelser:

* Fristående händelser
* Sessionshändelser
* Jobbhändelser

### <a name="standalone-events"></a>Standalone Events
#### <a name="reference"></a>Referens
            void SendEvent(string name, Dictionary<object, object> extras = null)

Fristående händelser kan inträffa för en session.

#### <a name="example"></a>Exempel
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Sessionshändelser
#### <a name="reference"></a>Referens
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Sessionshändelser används vanligtvis för att rapportera åtgärder som utförs av en användare under sin session.

#### <a name="example"></a>Exempel
**Utan data:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Med data:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Jobbhändelser
#### <a name="reference"></a>Referens
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Jobbhändelser används vanligtvis för att rapportera åtgärder som utförs av en användare när ett jobb.

#### <a name="example"></a>Exempel
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Rapporterat fel
Det finns tre typer av fel:

* Fristående fel
* Sessionen fel
* Jobbfel

### <a name="standalone-errors"></a>Fristående fel
#### <a name="reference"></a>Referens
            void SendError(string name, Dictionary<object, object> extras = null)

Strider mot session fel inträffa fristående fel utanför ramen för en session.

#### <a name="example"></a>Exempel
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Sessionen fel
#### <a name="reference"></a>Referens
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Sessionen fel används vanligtvis för att rapportera fel som påverkar användaren under sin session.

#### <a name="example"></a>Exempel
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Jobbfel
#### <a name="reference"></a>Referens
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Fel kan vara relaterad till ett jobb som körs i stället för som rör den aktuella användarsessionen.

#### <a name="example"></a>Exempel
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Rapportering krascher
Agenten erbjuder två metoder för att hantera kraschar.

### <a name="send-an-exception"></a>Skicka ett undantag
#### <a name="reference"></a>Referens
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Exempel
Du kan skicka ett undantag när som helst genom att anropa:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Du kan också använda en valfri parameter för att avsluta sessionen engagement samtidigt än att skicka kraschen. Det gör du genom att anropa:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Om du gör det stängs sessionen och jobb när du skickar kraschen.

### <a name="send-an-unhandled-exception"></a>Skicka ett ohanterat undantag
#### <a name="reference"></a>Referens
            void SendCrash(Exception e)

Engagement innehåller också en metod för att skicka ohanterade undantag om du har **inaktiverad** Engagement automatisk **krasch** reporting. Detta är särskilt användbar när den används i programmet UnhandledException händelsehanteraren.

Den här metoden kommer **alltid** avslutas sessionen för engagement och jobb efter anropas.

#### <a name="example"></a>Exempel
Du kan använda den för att implementera din egen UnhandledExceptionEventArgs-hanterare. Till exempel lägga till den `Current_UnhandledException` metod för den `App.xaml.cs` filen:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

Lägg till i App.xaml.cs i ”gemensamma App() {}”:

            Application.Current.UnhandledException += Current_UnhandledException;

## <a name="device-id"></a>Enhets-ID
            String EngagementAgent.Instance.GetDeviceId()

Du kan hämta engagement enhets-id genom att anropa den här metoden.

## <a name="extras-parameters"></a>Tillägg parametrar
Diverse uppgifter kan kopplas till en händelse, ett fel, en aktivitet eller ett jobb. Dessa data strukturerade med hjälp av en ordlista. Nycklar och värden som kan vara av valfri typ.

Extra data serialiseras så om du vill infoga en egen typ i tillägg du vill lägga till ett datakontrakt för den här typen.

### <a name="example"></a>Exempel
Vi skapar en ny klass ”Person”.

            using System.Runtime.Serialization;

            namespace Microsoft.Azure.Engagement
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Sedan lägger vi till en `Person` instans till en extra.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> Om du placerar andra typer av objekt, kontrollera att deras toString ()-metoden implementeras för att returnera en mänsklig läsbar sträng.
> 
> 

### <a name="limits"></a>Begränsningar
#### <a name="keys"></a>Nycklar
Varje nyckel i objektet måste matcha följande reguljära uttryck:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Det innebär att nycklar måste börja med minst en bokstav, följt av bokstäver, siffror eller understreck (\_).

#### <a name="size"></a>Storlek
Tillägg är begränsade till **1024** tecken per anrop.

## <a name="reporting-application-information"></a>Rapportering programinformation
### <a name="reference"></a>Referens
            void SendAppInfo(Dictionary<object, object> appInfos)

Du kan manuellt rapportera spåra information (eller andra program specifik information) med hjälp av SendAppInfo() funktion.

Observera att dessa data kan skickas inkrementellt: endast det senaste värdet för en viss nyckel sparas för en viss enhet. Använda en ordlista som händelsen tillägg\<objekt, objektet\> att koppla data.

### <a name="example"></a>Exempel
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
              };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Begränsningar
#### <a name="keys"></a>Nycklar
Varje nyckel i objektet måste matcha följande reguljära uttryck:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Det innebär att nycklar måste börja med minst en bokstav, följt av bokstäver, siffror eller understreck (\_).

#### <a name="size"></a>Storlek
Information om programmet är begränsad till **1024** tecken per anrop.

I exemplet ovan är JSON som skickas till servern 44 tecken:

            {"birthdate":"1983-12-07","gender":"female"}

## <a name="logging"></a>Loggning
### <a name="enable-logging"></a>Aktivera loggning
SDK kan konfigureras för att skapa test loggar i IDE-konsolen.
Dessa loggar är inte aktiverade som standard. Anpassa detta genom att uppdatera egenskapen `EngagementAgent.Instance.TestLogEnabled` till ett värde som är tillgängliga från den `EngagementTestLogLevel` uppräkning, till exempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

