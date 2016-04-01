Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1. Von der **Ausführen** Menü klicken Sie auf **Ausführen** um das Projekt zu starten.

    Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

5. Geben Sie wie zuvor einen sinnvollen Text ein, und klicken Sie dann **Hinzufügen**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. Sie können auch die Datenbank mit der Azure-Portal überprüfen: die nächsten beiden Schritten führen Sie diese Option, um die Änderungen in der Datenbank anzuzeigen.


4. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie zum Verwalten der Datenbank mit Ihrem mobilen Dienst verknüpft ist.

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)

5. Führen Sie im klassischen Azure-Portal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

Dies schließt die **Erste Schritte mit Daten** Android-Lernprogramm.

