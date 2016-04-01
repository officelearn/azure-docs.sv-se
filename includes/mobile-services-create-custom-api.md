

1. Melden Sie sich bei der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf **Mobile Services**, und wählen Sie dann Ihren mobilen Dienst.

2. Klicken Sie auf die **API** Registerkarte, und klicken Sie dann auf **Erstellen**. Dies zeigt die **Erstellen Sie eine neue benutzerdefinierte API** Dialogfeld.

3. Typ _Completeall_ in **API-Name**, und klicken Sie dann auf die Schaltfläche mit dem Häkchen, um die neue API zu erstellen.

    > [AZURE.TIP] Mit den Standardberechtigungen kann jeder mit dem app-Schlüssel die benutzerdefinierte API aufrufen. Der Anwendungsschlüssel wird jedoch nicht verteilt bzw. sicher gespeichert und gilt daher nicht als sichere Anmeldeinformation. Erwägen Sie, den Zugriff nur authentifizierten Benutzern zu gestatten, um die Sicherheit zu erhöhen.

4. Klicken Sie auf **Completeall** in der API-Tabelle.

5. Klicken Sie auf die **Skript** ersetzen Sie den vorhandenen Code durch den folgenden Code und anschließend auf **Speichern**.    Dieser Code verwendet die [mssql object] für den Zugriff auf die **Todoitem** Tabelle direkt zum Festlegen der `complete` Flag für alle Elemente. Da die **exports.post** Funktion verwendet wird, senden die Clients eine POST-Anforderung zum Ausführen des Vorgangs. Die Anzahl der geänderten Zeilen wird an den Client als Ganzzahl zurückgegeben.


        exports.post = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "UPDATE todoitem SET complete = 1 " +
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
            mssql.query(sql, {
                success: function(results) {
                    if(results.length == 1)
                        response.send(200, results[0]);
                }
            })
        };


> [AZURE.NOTE] Die [Anforderung](http://msdn.microsoft.com/library/windowsazure/jj554218.aspx) und [Antwort](http://msdn.microsoft.com/library/windowsazure/dn303373.aspx) angegebenen benutzerdefinierten-API-Funktionen werden implementiert, indem die [Express.js-Bibliothek](http://go.microsoft.com/fwlink/p/?LinkId=309046). 

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[mssql object]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx


