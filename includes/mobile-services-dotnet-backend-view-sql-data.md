
Der letzte optionale Schritt dieses Lernprogramms besteht darin, die mit dem mobilen Dienst verknüpfte Datenbank und die darin gespeicherten Daten zu überprüfen. 

1. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie zum Verwalten der Datenbank mit Ihrem mobilen Dienst verknüpft ist.
 
    ![Anmelden zum Verwalten der SQL-Datenbank](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. Führen Sie im Portal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht etwa wie die folgende Abfrage nur Ihren Datenbanknamen anstelle von verwenden <code>todolist</code>.</p>

        SELECT * FROM [todolist].[todoitems]

    ![gespeicherte Elemente der SQL-Datenbank abfragen](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

    Beachten Sie, dass die Tabelle die Spalten "Id", "__createdAt", "__updatedAt" und "__version" enthält. Diese Spalten ermöglichen die offline-Daten synchronisieren und implementiert werden, der [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx) Basisklasse. Weitere Informationen finden Sie unter [Erste Schritte mit der Synchronisierung von Offlinedaten].

