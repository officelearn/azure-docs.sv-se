---
title: "Hur du använder Engagement API på Windows Phone Silverlight"
description: "Hur du använder Engagement API på Windows Phone Silverlight"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ae2ba2e8-f75b-4dee-a164-a7dd65d35a23
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ec8b6c13ea052c8063dfde4321cdd286ab6cb817
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Hur du använder Engagement API på Windows Phone Silverlight
Det här dokumentet är ett tillägg till dokumentet [hur du integrerar Mobile Engagement i din app för Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Det ger i djup information om hur du använder Engagement API för att rapportera programmet-statistik.

Om du bara vill Engagement att rapportera programmets sessioner, aktiviteter, krascher och teknisk information, så det enklaste sättet att se alla dina `PhoneApplicationPage` underordnade klasser ärver från den `EngagementPage` klass.

Om du vill göra mer, till exempel om du behöver rapportera programmet specifika händelser, fel och jobb, eller om du vill rapportera ditt programs aktiviteter i ett annat sätt än den som implementerats i den `EngagementPage` klasser, måste du använda Engagement-API.

Engagement-API som tillhandahålls av den `EngagementAgent` klass. Du har åtkomst till de här metoderna via `EngagementAgent.Instance`.

Även om modulen agent inte har initierats, varje anrop till API: et skjuts upp och köras igen när agenten är tillgänglig.

## <a name="engagement-concepts"></a>Koncept i engagement
Följande delar förfina den koncept i Mobile Engagement för Windows Phone-plattformen.

### <a name="session-and-activity"></a>`Session` och `Activity`
En *aktiviteten* beror vanligtvis på en sida av programmet, det vill säga den *aktiviteten* när sidan visas och stoppas när sidan stängs: så är fallet när Engagement-SDK är integrerad med hjälp av den `EngagementPage` klass.

Men *aktiviteter* kan också kontrolleras manuellt med hjälp av Engagement-API. Detta gör för att dela en viss sida i flera sub delar för att få mer information om användning av den här sidan (till exempel och hur ofta kända och hur länge dialogrutor används i den här sidan).

## <a name="reporting-activities"></a>Rapportering
### <a name="user-starts-a-new-activity"></a>Användaren startar en ny aktivitet
#### <a name="reference"></a>Referens
            void StartActivity(string name, Dictionary<object, object> extras = null)

Du måste anropa `StartActivity()` varje gång aktiviteten användarändringar. Det första anropet till funktionen startar en ny session.

> [!IMPORTANT]
> SDK anropa metoden EndActivity automatiskt när programmet stängs. Därför rekommenderas att anropa metoden StartActivity när aktiviteten för användaren och till aldrig anropa metoden EndActivity eftersom den här metoden anropas tvingar den aktuella sessionen avslutas.
> 
> 

#### <a name="example"></a>Exempel
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Användaren avslutar sin aktuella aktiviteten
#### <a name="reference"></a>Referens
            void EndActivity()

Du måste anropa `EndActivity()` minst en gång när användaren är klar användarens sista aktivitet. Det informerar Engagement SDK att användaren är för närvarande inaktiv och användarsessionen måste stängas när tidsgränsen för sessionen upphör att gälla (om du anropar `StartActivity()` innan tidsgränsen för sessionen upphör sessionen bara fortsätter).

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

### <a name="standalone-events"></a>Fristående händelser
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
            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement tillhandahåller också en metod för att skicka ett ohanterat undantag. Detta är särskilt användbar när den används i händelsehanteraren silverlight UnhandledException.

Den här metoden kommer **alltid** avslutas sessionen för engagement och jobb efter anropas.

#### <a name="example"></a>Exempel
Du kan använda den för att implementera din egen UnhandledException hanterare (särskilt om du har inaktiverat automatisk kraschen reporting-funktionen i Engagement). Till exempel i den `Application_UnhandledException` metod för den `App.xaml.cs` filen:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code

              EngagementAgent.Instance.SendCrash(e);
            }

## <a name="onactivated"></a>OnActivated
### <a name="reference"></a>Referens
            void OnActivated(ActivatedEventArgs e)

När användaren navigerar framåt, från ett program när inaktiverad händelsen utlöses försöker operativsystemet placera programmet i en vilande tillstånd. Programmet är tombstone-borttagning. I den här processen avslutas ett program, men vissa data om tillståndet för programmet och enskilda sidor i programmet bevaras.

Du måste infoga `EngagementAgent.Instance.OnActivated(e)` i den `Application_Activated` metoden från filen App.xaml.cs att återställa Engagement agenten när programmet har tombstone.

### <a name="example"></a>Exempel
            // Inside your App.xaml.cs file

            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

## <a name="device-id"></a>Enhets-Id
            String GetDeviceId()

Du kan hämta engagement enhets-id genom att anropa den här metoden.

## <a name="extras-parameters"></a>Tillägg parametrar
Diverse uppgifter kan kopplas till en händelse, ett fel, en aktivitet eller ett jobb. Dessa data strukturerade med hjälp av en ordlista. Nycklar och värden som kan vara av valfri typ.

Extra data serialiseras så om du vill infoga en egen typ i tillägg du vill lägga till ett datakontrakt för den här typen.

### <a name="example"></a>Exempel
Vi skapar en ny klass ”Person”.

            using System.Runtime.Serialization;

            namespace Engagement.Agent
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

Observera att denna information kan skickas inkrementellt: endast det senaste värdet för en viss nyckel sparas för en viss enhet. Använda en ordlista som händelsen tillägg\<objekt, objektet\> bifoga information.

### <a name="example"></a>Exempel
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Begränsningar
#### <a name="keys"></a>Nycklar
Varje nyckel i objektet måste matcha följande reguljära uttryck:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Det innebär att nycklar måste börja med minst en bokstav, följt av bokstäver, siffror eller understreck (\_).

#### <a name="size"></a>Storlek
Information om programmet är begränsade till **1024** tecken per anrop.

I exemplet ovan är JSON som skickas till servern 44 tecken:

            {"subscription":"2013-12-07","premium":"true"}

## <a name="logging"></a>Loggning
### <a name="enable-logging"></a>Aktivera loggning
SDK kan konfigureras för att skapa test loggar i IDE-konsolen.
Dessa loggar är inte aktiverade som standard. Anpassa detta genom att uppdatera egenskapen `EngagementAgent.Instance.TestLogEnabled` till ett värde som är tillgängliga från den `EngagementTestLogLevel` uppräkning, till exempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
