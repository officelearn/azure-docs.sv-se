In diesem Thema wird das Erstellen einer App sowohl mit einem mobilen Client als auch mit einem Webclient veranschaulicht. Sie erstellen eine mobile App und eine Web-App und verwenden für beide dieselbe zugrunde liegende Datenbank.

Zunächst erstellen Sie eine neue mobile app-Back-End und eine einfache *Aufgabenliste* app, app-Daten im neuen mobilen app-Back-End speichert. Das mobile App-Back-End verwendet die unterstützten .NET-Sprachen für die serverseitige Geschäftslogik. Die Client-App kann alle von der mobilen App unterstützten Clientplattformen nutzen, einschließlich iOS, Windows, Xamarin iOS und Xamarin Android.

Anschließend erstellen Sie eine Web-App, die dieselbe Datenbank wie die mobile App verwendet. Am Ende des Lernprogramms verfügen Sie über einen Webclient und einen mobilen Client, die mit denselben Daten arbeiten.

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren, bevor Sie sich für ein Azure-Konto anmelden möchten, wechseln Sie zu [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751), wo Sie eine kurzlebige Starter-Web-app sofort in App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen eines neuen mobilen App-Back-Ends und -Clients

* Führen Sie die Schritte in diesem Lernprogramm [Create a mobile app] zum Erstellen einer mobilen app-Back-End und einem Client. Sie können alle von den mobilen Apps unterstützten Clientplattformen nutzen, einschließlich iOS, Windows, Xamarin iOS und Xamarin Android.

* Stellen Sie sicher, dass Sie Ihr mobiles App-Back-End auf Azure bereitgestellt haben und dass Ihre mobile Client-App mit dem gehosteten Back-End verbunden ist. Das Codeprojekt für die mobile App verwendet Entity Framework Code First und initialisiert die Datenbank nach der ersten REST-Anforderung von einer mobilen Client-App.

## Veröffentlichen einer TodoList-Web-API in Visual Studio

In diesem Abschnitt erstellen Sie eine neue Web-App mithilfe einer Beispiellösung für die Web-App. Sie bearbeiten das Beispiel für die Verwendung desselben Datenbankschemanamens und derselben Verbindungszeichenfolge wie die mobile App.

>[AZURE.NOTE] Bevor Sie diese Schritte ausführen, stellen Sie sicher, dass Sie Ihre mobile app-Back-End-Datenbank initialisiert wurde, indem Sie einen Client aus eine Verbindung herstellen. Andernfalls kann die Web-App keine Verbindung zu derselben Datenbank herstellen.

1. In der [Azure-Portal](https://portal.azure.com/), erstellen Sie eine neue Web-app mithilfe derselben Ressourcengruppe und Webhosting-Plan wie Ihre mobile app.

2. Laden Sie die Beispielprojektmappe [MultiChannelToDo] und in Visual Studio geöffnet. Die Lösung enthält ein Web-API-Projekt und ein Web-App-Projekt für die Web-Client-Benutzeroberfläche.

3. Bearbeiten Sie im Web-API-Projekt die Datei "MultiChannelToDoContext.cs". Ändern Sie in `OnModelCreating` den Schemanamen in den Namen Ihrer mobilen App:

        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore

4. Als Nächstes rufen wir die Verbindungszeichenfolge der mobilen App aus dem Azure-Portal ab:

    - Wählen Sie Ihre mobile app im Portal, und klicken Sie auf den Teil mit der Bezeichnung **Benutzercode**.

    - Wählen Sie im daraufhin geöffneten Blatt **Alle Einstellungen**, dann **Anwendungseinstellungen**.

    - Klicken Sie unter **Connection Strings** Klicken Sie auf **Verbindungszeichenfolgen anzeigen**. Kopieren Sie den Wert für die Einstellung **MS_TableConnectionString**. Dies ist die Verbindungszeichenfolge, die von der mobilen App verwendet wird, um eine Verbindung mit der SQL-Datenbank herzustellen.

5. Klicken Sie in Visual Studio Web API-Projekt mit der rechten Maustaste, und wählen Sie **Veröffentlichen**. Wählen Sie **Azure Web-Apps** als Veröffentlichungsziel aus, und wählen Sie die Webanwendung, die Sie soeben erstellt haben. Klicken Sie auf **Weiter** bis Sie zum Abrufen der **Einstellungen** Abschnitt des Assistenten Web veröffentlichen.

6. In der **Datenbanken** Abschnitt, fügen Sie die Verbindungszeichenfolge für mobile app als Wert für **multichanneltodocontext ein**. Wählen Sie nur das Kontrollkästchen **verwenden diese Verbindungszeichenfolge zur Laufzeit**.

7. Sobald Ihre Web-API erfolgreich in Azure veröffentlicht wurde, wird eine Bestätigungsseite angezeigt. Kopieren Sie die URL für den veröffentlichten Dienst.

## Veröffentlichen einer Benutzeroberfläche für einen TodoList-Webclient aus Visual Studio

In diesem Abschnitt verwenden Sie eine Beispiel-Webclient-App, die mit AngularJS implementiert wird. Anschließend veröffentlichen Sie das Projekt mit Visual Studio in einer neuen gehosteten App Service-Web-App in Azure.

1. Öffnen Sie in Visual Studio das Projekt **MultiChannelToDo.Web**. Bearbeiten Sie die Datei `js/service/ToDoService.js`, und fügen Sie die URL der gerade veröffentlichten Web-API hinzu:

        var apiPath = "https://your-web-api-site-name.azurewebsites.net";

2. Klicken Sie mit der mit der rechten Maustaste auf das Projekt **MultiChannelToDo.Web** und wählen Sie **Veröffentlichen**.

3. In der **Web veröffentlichen** Assistenten **Azure Web App** bei der Veröffentlichung und erstellen Sie eine neue Web-app ohne eine Datenbank.

4. Sobald das Projekt erfolgreich veröffentlicht wurde, wird die Webbenutzeroberfläche in Ihrem Browser angezeigt.

## Testen von mobilen und Web-Apps

1. Fügen Sie in der Webbenutzeroberfläche Aufgaben hinzu, oder bearbeiten Sie einige.

    ![Ansicht der Web-App im Browser](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)

2. Führen Sie die mobile app, die Sie in erstellt die [Create a mobile app] Lernprogramm. Sie sehen dieselben Aufgabenelemente wie in der Web-App.

    ![Ansicht der mobilen Xamarin-App](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714)
* Eine Anleitung für die Änderung des dem alten zum neuen Portal finden Sie unter: [Referenz zur Navigation im Azure-Portals](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[Create a mobile app]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md


