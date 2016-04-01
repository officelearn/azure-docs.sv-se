

1. Öffnen Sie die Projektdatei default.js und in der **app. OnActivated** Methode überladen, ersetzen Sie den letzten Aufruf der **RefreshTodoItems** -Methode durch den folgenden Code: 

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

    Wenn Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert, der zum Übergeben der <strong>Anmeldung</strong> übergebenen Wert auf einen der folgenden: _Microsoftaccount_, _twitter_, _Google_, oder _Windowsazureactivedirectory_.

    >[AZURE.NOTE]Wenn Sie Ihre Windows Store-app-Paketinformationen mit Mobile Services registriert haben, sollten Sie Aufrufen der <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">Anmeldung</a> Methode, indem der Wert des <strong>true</strong> für die <em>UseSingleSignOn</em> Parameter. Wenn Sie dies nicht tun, werden Ihre Benutzer jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird.

2. Klicken Sie im Windows Store-app-Projekt öffnen Sie die Datei "default.HTML", und fügen Sie die folgenden **Schaltfläche** Element vor dem Element, definiert die **Speichern** Schaltfläche:

        <button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3. Drücken Sie F5, um die App auszuführen und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden. 

    Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

