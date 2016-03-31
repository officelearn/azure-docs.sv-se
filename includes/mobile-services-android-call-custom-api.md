
###<a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API

1. Wir fügen neben der vorhandenen Schaltfläche eine Schaltfläche namens "Complete All" ein und verschieben beide Schaltflächen um eine Zeile nach unten. Öffnen Sie in Android Studio die *res\layout\activity_to_do.xml* Datei in Ihrem schnellstartprojekt, suchen Sie die **LinearLayout** -Element, enthält die **Schaltfläche** Element mit dem Namen `buttonAddToDo`. Kopieren der **LinearLayout** und fügen Sie ihn sofort nach dem ursprünglichen ein. Löschen Sie die **Schaltfläche** Element aus dem ersten **LinearLayout**.

2. In der zweiten **LinearLayout**, löschen Sie die **EditText** -Element, und fügen Sie folgenden Code direkt nach dem vorhandenen **Schaltfläche** Element: 

        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />

    Dadurch wird auf der Seite in einer separaten Zeile neben der vorhandenen Schaltfläche eine neue Schaltfläche hinzugefügt.

3. Die zweite **LinearLayout** jetzt wie folgt aussieht:

         <LinearLayout
            android:layout_width="match_parent" 
            android:layout_height="wrap_content" 
            android:background="#71BCFA"
            android:padding="6dip"  >
            <Button
                android:id="@+id/buttonAddToDo"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="addItem"
                android:text="@string/add_button_text" />
            <Button
                android:id="@+id/buttonCompleteItem"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="completeItem"
                android:text="@string/complete_button_text" />
        </LinearLayout>
    

4. Öffnen Sie die Datei res\values\string.xml file, und fügen Sie die folgende Codezeile hinzu:

        <string name="complete_button_text">Complete All</string>



5. Klicken Sie im Projekt-Explorer mit der rechten Maustaste auf den Namen des Projekts in der *Src* Ordner (`com.example.{your projects name}`), wählen Sie **Neu** dann **Klasse**. Geben Sie im Dialogfeld **MarkAllResult** in das klassennamensfeld ein, wählen Sie OK aus, und Ersetzen Sie die resultierende Klassendefinition durch den folgenden Code:

        import com.google.gson.annotations.SerializedName;
        
        public class MarkAllResult {
            @SerializedName("count")
            public int mCount;
            
            public int getCount() {
                return mCount;
            }
        
            public void setCount(int count) {
                    this.mCount = count;
            }
        }

    Diese Klasse dient zur Aufnahme des von der benutzerdefinierten API zurückgegebenen Zeilenanzahlwerts. 

6. Suchen Sie die **RefreshItemsFromTable** -Methode in der **ToDoActivity.java** Datei, und stellen Sie sicher, dass die erste Codezeile in der `try` Block sieht folgendermaßen aus:

        final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();

    Hierdurch werden die Elemente so gefiltert, dass abgeschlossene Elemente von der Abfrage nicht zurückgegeben werden.

7. Stellen Sie sicher, dass **ToDoActivity.java** am Anfang der Datei die folgenden Befehle enthält:

        import com.google.common.util.concurrent.FutureCallback;
        import com.google.common.util.concurrent.Futures;
        import com.google.common.util.concurrent.ListenableFuture;

8. In der **ToDoActivity.java** Datei, fügen Sie die folgende Methode hinzu:

    public void completeItem(View view) {
        
        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll2", MarkAllResult.class ); 
            
            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }
                
                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();    
                }
            });
        }
    
    Diese Methode verarbeitet das **Klicken Sie auf** -Ereignis für die Schaltfläche "Neu". Die **InvokeApi** Methode wird aufgerufen, auf dem Client, der eine POST-Anforderung an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Meldungsdialogfeld angezeigt, ebenso wie jegliche Fehler.

### Testen der App

1. Von der **Ausführen** Menü klicken Sie auf **app ausführen** um das Projekt im Android-Emulator oder auf einem angeschlossenen Android-Gerät zu starten.

    Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.


2. Geben Sie in der app Text in **Insert a TodoItem**, klicken Sie dann auf **Hinzufügen**.

3. Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4. Klicken Sie auf die **Complete All** Schaltfläche.

    ![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)

    Ein Meldungsdialogfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, um alle Elemente aus der Liste zu löschen.

