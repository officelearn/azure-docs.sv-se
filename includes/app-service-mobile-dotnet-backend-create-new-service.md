1. Melden Sie sich bei der [Azure Portal].

2. In der oberen Rand des Fensters, klicken Sie auf die **+ Neu** Schaltfläche > **Web + Mobile** > **Mobile App**, geben Sie dann einen Namen für Ihre Mobile App-Back-End.

3. In der **Ressourcengruppe** Wählen Sie eine vorhandene Ressourcengruppe. Wenn Sie keine Ressourcengruppen besitzen, geben Sie den gleichen Namen wie für Ihre App ein. 
 
    An dieser Stelle wird der standardmäßige App Service-Plan ausgewählt, bei dem es sich um den kostenlosen Tarif handelt. Die Einstellungen für den App Service-Plan bestimmen den Standort, die Funktionen und die Computeressourcen Ihrer App. Sie können einen anderen vorhandenen App Service-Plan auswählen oder einen neuen Plan erstellen. Weitere Informationen zu App-Dienste Pläne und einen neuen Plan erstellen, finden Sie unter [Azure App Service-Pläne – detaillierte Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

4. Den Standard-App-Service-Plan verwenden, wählen Sie einen anderen Plan oder [einen neuen Plan erstellen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan), klicken Sie dann auf **Erstellen**. 
    
    Dadurch wird das Back-End für mobile Apps erstellt. Später werden Sie Ihr Serverprojekt in diesem Back-End bereitstellen. Eine Mobile App-Back-End-Bereitstellung kann einige Minuten dauern; die **Einstellungen** Blatt für die Mobile App-Back-End wird angezeigt, wenn Sie fertig sind. Bevor Sie das Back-End für mobile Apps verwenden können, müssen Sie auch eine Verbindung zu einem Datenspeicher definieren.

    > [AZURE.NOTE] Im Rahmen dieses Lernprogramms erstellen Sie eine neue Instanz der SQL-Datenbank und Server. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Falls Sie bereits eine Datenbank in demselben Speicherort wie die neue mobile app-Back-End haben, wählen Sie stattdessen **eine vorhandene Datenbank** und wählen Sie dann die Datenbank. Die Verwendung einer Datenbank an einem anderen Standort wird nicht empfohlen, da zusätzliche Kosten für Bandbreite anfallen können und es zu höherer Latenz kommen kann. Es stehen weitere Datenspeicheroptionen zur Verfügung. 

6. In der **Einstellungen** Blatt für die neue Mobile App-Back-End, klicken Sie auf **Schnellstart** > Ihrer Client-app-Plattform > **Verbinden eine Datenbank**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. In der **Datenverbindung hinzufügen** Blatt, klicken Sie auf **SQL-Datenbank** > **Erstellen einer neuen Datenbank**, geben Sie die Datenbank **Namen**, wählen Sie eine Preisstufe, und klicken Sie dann **Server**.  
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. In der **neue Server** Blatt, geben Sie einen eindeutigen Namen in der **Servername** Feld, das Bereitstellen einer sicheren **Server Admin-Anmeldung** und **Kennwort**, stellen Sie sicher, dass **Azure-Diensten den Zugriff auf Server** aktiviert ist, und klicken Sie dann **OK** zweimal. Dadurch werden die neue Datenbank und der neue Server erstellt.

10. In der **Datenverbindung hinzufügen** Blatt, klicken Sie auf **Verbindungszeichenfolge**, geben Sie die Werte für Benutzername und Kennwort für die Datenbank, und klicken Sie dann **OK** zweimal.

    Das Erstellen der Datenbank kann einige Minuten dauern.  Verwenden der **Benachrichtigungen** Bereich, um den Fortschritt der Bereitstellung überwachen.  Sie können erst fortfahren, wenn die Datenbank erfolgreich bereitgestellt wurde.


<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/


