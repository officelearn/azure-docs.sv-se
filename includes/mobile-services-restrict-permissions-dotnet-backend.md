

Standardmäßig sind sämtliche Anfragen an die Ressourcen des mobilen Diensts auf Clients beschränkt, die den Anwendungsschlüssel vorweisen. Der sichere Zugriff auf die Ressourcen ist dadurch allerdings nicht gegeben. Um die Ressourcen zu schützen, müssen Sie den Zugriff auf authentifizierte Clients einschränken.

1. Öffnen Sie das mobile Dienstprojekt in Visual Studio, erweitern Sie den Ordner Controller, und öffnen **TodoItemController.cs**. Die **TodoItemController** Klasse implementiert den Datenzugriff für die TodoItem-Tabelle. Fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Übernehmen Sie das folgende _AuthorizeLevel_ -Attribut auf die **TodoItemController** Klasse. 

        [AuthorizeLevel(AuthorizationLevel.User)]

    Dadurch wird sichergestellt, dass alle Vorgänge für die _TodoItem_ Tabelle einen authentifizierten Benutzer erfordern. Sie können auch anwenden der *AuthorizeLevel* Attribut auf Methodenebene.

3. (Optional) Wenn Sie die Authentifizierung lokal debuggen möchten, erweitern Sie die `App_Start` Öffnen Sie im Ordner **WebApiConfig.cs**, und fügen Sie den folgenden Code der **registrieren** Methode.  

        config.SetIsHosted(true);

    Dies weist das lokale mobile Dienstprojekt so ausgeführt, als ob dieses in Azure, einschließlich berücksichtigt gehostet wird die *AuthorizeLevel* Einstellungen. Ohne diese Einstellung dürfen sich alle HTTP-Anforderungen an Localhost ohne Authentifizierung trotz der *AuthorizeLevel* festlegen. Wenn Sie den selbstgehosteten Modus aktivieren, müssen Sie auch einen Wert für den lokalen Anwendungsschlüssel festlegen.

4. (Optional) Legen Sie in der web.config-Projektdatei einen Zeichenfolgenwert für die Anwendungseinstellung `MS_ApplicationKey` fest. 

    Dies ist das Kennwort, mit dem Sie die API-Hilfeseiten testen (ohne Benutzername), wenn Sie den Dienst lokal ausführen.  Dieser Zeichenfolgenwert wird nicht von der Live-Website in Azure verwendet, und Sie müssen nicht den tatsächlichen Anwendungsschlüssel verwenden; es kann eine beliebige gültige Zeichenfolge verwendet werden.
 
4. Veröffentlichen Sie das Projekt erneut.


