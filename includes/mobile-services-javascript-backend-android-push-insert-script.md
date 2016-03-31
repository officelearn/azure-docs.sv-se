
1. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf die **Daten** Registerkarte, und klicken Sie dann auf die **TodoItem** Tabelle. 
 
2. In **Todoitem**, klicken Sie auf die **Skript** Registerkarte, und wählen Sie **Einfügen**.
   
    Die Funktion angezeigt, die aufgerufen wird, wenn ein Einfügevorgang in erfolgt die **TodoItem** Tabelle.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code, und klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
        // Define a simple payload for a GCM notification.
        var payload = {
            "data": {
                "message": item.text
            }
        };      
        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    Dadurch wird ein neues Insert-Skript, das verwendet registriert die [Gcm-Objekt](http://go.microsoft.com/fwlink/p/?LinkId=282645) eine Push-Benachrichtigung an alle registrierten Geräte senden, nachdem der Einfügevorgang erfolgreich war. 

