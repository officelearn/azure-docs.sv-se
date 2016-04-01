Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf **Mobile Services**, und klicken Sie dann auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die **Daten** und anschließend auf **+ Create**.

    Dies zeigt die **erstellt eine neue Tabelle** Dialogfeld.

3. In **Tabellenname** Typ _TodoItem_, klicken Sie dann auf das Häkchen. Dies erstellt eine neue Speichertabelle **TodoItem** mit den Standardberechtigungen festgelegt. Dies bedeutet, dass jeder mit dem Anwendungsschlüssel (der mit Ihrer App verteilt wird) auf die Daten in der Tabelle zugreifen und diese ändern kann. 

    >[AZURE.NOTE] Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.

4. Klicken Sie auf die neue **TodoItem** Tabelle, und stellen Sie sicher, dass keine Datenzeilen vorhanden sind.

5. Klicken Sie auf die **Spalten** Registerkarte. Überprüfen Sie, dass folgende Standardspalten automatisch erstellt werden: 
    
    <table border="1" cellpadding="10">
    <tr>
    <th>Spaltenname</th>
    <th>Typ</th>
    <th>Index</th>
    </tr>
    <tr>
    <td>ID</td>
    <td>string</td>
    <td>Indiziert</td>
    </tr>
    <tr>
    <td>__createdAt</td>
    <td>date</td>
    <td>Indiziert</td>
    </tr>
    <tr>
    <td>__updatedAt</td>
    <td>date</td>
    <td><font color="transparent">-</font></td>
    </tr>
    <tr>
    <td>__version</td>
    <td>timestamp (MSSQL)</td>
    <td><font color="transparent">-</font></td>
    </tr>   
    </table>    
        

    This is the minimum requirement for a table in Mobile Services. 

    > [AZURE.NOTE] When dynamic schema is enabled on your mobile service, new columns are created automatically when JSON objects are sent to the mobile service by an insert or update operation.

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.


