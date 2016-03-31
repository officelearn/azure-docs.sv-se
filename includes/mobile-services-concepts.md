## <a name="what-is"></a>Was ist Mobile Services

Azure Mobile Services ist eine hochgradig skalierbare mobile Anwendungsentwicklungsplattform, mit der Sie Ihren mobilen Anwendungen mithilfe von Azure erweiterte Funktionalität hinzufügen können. 

Mit Mobile Services haben Sie folgende Möglichkeiten: 

+ **Erstellen Sie systemeigene und cross-Plattform-apps** -Verbinden Sie Ihre iOS, Android, Windows oder plattformübergreifenden Xamarin oder Cordova (Phonegap) mit dem mobilen Back-End-Dienst mithilfe der systemeigenen SDKs.  
+ **Senden von Pushbenachrichtigungen an Ihre Benutzer** -Senden von Pushbenachrichtigungen an Ihre Benutzer Ihrer App.
+ **Benutzer authentifizieren** -nutzen Sie beliebte Identitätsanbieter wie Facebook und Twitter, um Ihre app-Benutzer zu authentifizieren.
+ **Speichern von Daten in der Cloud** -Speichern von Benutzerdaten in einer SQL-Datenbank (standardmäßig) oder in Mongo DB, DocumentDB, Azure-Tabellen oder Azure-Blobs. 
+ **Erstellen von offlinefähigen apps mit Synchronisierung** -machen Sie Ihre apps offline arbeiten und Mobile Services verwenden, um Daten im Hintergrund synchronisieren.
+ **Überwachen und skalieren Sie Ihre apps** - app-Nutzung zu überwachen und Skalieren von Ihrem Back-End bei wachsendem Bedarf. 

## <a name="concepts"> </a>Mobile Services-Konzepte

Mobile Services umfasst folgende wichtige Funktionen und basiert auf den folgenden Konzepten:

+ **Anwendungsschlüssel:** ein eindeutiger Wert, der verwendet wird, um den Zugriff auf den mobilen Dienst von zufälligen Clients beschränken dieser "Schlüssel" ist kein Sicherheitstoken und wird zur Authentifizierung von Benutzern Ihrer App nicht verwendet.    
+ **Back-End:** der mobilen Dienstinstanz, die Ihre app unterstützt. Ein mobiler Dienst implementiert wird entweder als ASP.NET Web API-Projekt (*.NET Back-End-* ) oder als Node.js-Projekt (*JavaScript-Back-End*).
+ **Identitätsanbieter:** ein externer Dienst vertrauenswürdige mit Mobile Services, die Ihre app-Benutzer authentifiziert. Folgende Anbieter werden unterstützt: Facebook, Twitter, Google, Microsoft-Konto und Azure Active Directory. 
+ **Pushbenachrichtigung:** Dienst initiierte Nachricht, die an ein registriertes Gerät oder Benutzer mit Azure Notification Hubs gesendet wird.
+ **Skalierung:** für die zusätzlichen Kosten, mehr Leistung, Performance und Speicher hinzufügen, wie Ihre app beliebter wird.
+ **Geplanter Auftrag:** benutzerdefinierter Code, der anhand eines vorab geplanten Termins oder auf Anforderung ausgeführt wird.

Weitere Informationen finden Sie unter [Mobile Services-Konzepte](mobile-services-concepts-links.md).

