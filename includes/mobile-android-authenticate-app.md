
1. In **Projekt-Explorer** in Android Studio, öffnen Sie die Datei ToDoActivity.java und fügen Sie die folgenden importanweisungen.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Fügen Sie die folgende Methode, um die **ToDoActivity** Klasse: 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    Auf diese Weise wird eine neue Methode zur Verarbeitung des Authentifizierungsprozesses erstellt. Der Benutzer wird mithilfe einer Google-Anmeldung authentifiziert. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. Ohne erfolgreiche Authentifizierung können Sie nicht fortfahren.

    > [AZURE.NOTE] Wenn Sie einen anderen Identitätsanbieter als Google verwenden, ändern Sie den Wert, der zum Übergeben der **Anmeldung** übergebenen Wert auf einen der folgenden: _MicrosoftAccount_, _Facebook_, _Twitter_, oder _Windowsazureactivedirectory_.

3. In der **OnCreate** -Methode, fügen Sie die folgende Codezeile nach dem Code, der instanziiert die `MobileServiceClient` Objekt.

        authenticate();

    Dieser Aufruf startet den Authentifizierungsprozess.

4. Verschieben Sie den verbleibenden Code nach `authenticate();` in der **OnCreate** Methode, um ein neues **CreateTable** -Methode, die so aussieht:

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. Aus der **Ausführen** im Menü klicken Sie dann auf **app ausführen** um die app und melden Sie sich mit dem Identitätsanbieter Ihrer Wahl zu starten. 

    Wenn Sie erfolgreich angemeldet sind, sollte die App ohne Fehler ausgeführt werden, und Sie sollten in der Lage sein, den Back-End-Dienst abzufragen und Daten zu aktualisieren.

