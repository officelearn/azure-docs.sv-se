In diesem Abschnitt fügen Sie Ihrem Verzeichnis zwei neue Benutzer und die neue Gruppe "Sales" hinzu. Nur einem der Benutzer wird die Mitgliedschaft in der Gruppe "Sales" gewährt. Der andere Benutzer wird nicht als Mitglied in diese Gruppe aufgenommen. 

### Die Benutzer erstellen


1. In der [klassischen Azure-Portal](https://manage.windowsazure.com) Navigieren Sie zu dem Verzeichnis, das Sie zuvor für die Authentifizierung konfiguriert, wenn im Lernprogramm authentifizieren Ihrer app.
2. Klicken Sie auf **Benutzer** am oberen Rand der Seite. Klicken Sie dann auf die **Add User** unten. 
3. Abschließen der Dialogfelder des neuen Benutzer erstellen, die zum Erstellen eines Benutzers mit dem Namen **Bob**. Notieren Sie sich das temporäre Kennwort für den Benutzer. 
4. Erstellen Sie einen weiteren Benutzer namens **Dave**. Notieren Sie sich das temporäre Kennwort für den Benutzer.
5. Die neuen Benutzer sollten dem Beispiel unten ähneln.

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)    


### Die Gruppe "Sales" erstellen


1. Klicken Sie auf der Verzeichnisseite des **Gruppen** am oberen Rand der Seite. Klicken Sie dann auf die **Gruppe hinzufügen** unten. 
2. Geben Sie **Sales** für den Namen der Gruppe, und klicken Sie im Dialogfeld auf die Schaltfläche zum Fertigstellen zum Erstellen der Gruppe. 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### Der Gruppe "Sales" Benutzermitgliedschaften hinzufügen


1. Klicken Sie auf **Gruppen** am oberen Rand der Verzeichnisseite. Klicken Sie dann auf die **Sales** Gruppe auf die Gruppe "sales" zu gelangen. 
2. Klicken Sie auf der Seite "Gruppe" **Mitglieder hinzufügen**. Fügen Sie den Benutzer mit dem Namen **Bob** der Gruppe "sales". Der Benutzer mit dem Namen **Dave** sollte nicht Mitglied der Gruppe sein.

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. Klicken Sie auf der Seite "Gruppe" **Eigenschaften**, kopieren Sie die **Objekt-ID** für die Gruppe "sales" unten auf der Seite. 

   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

4. Navigieren Sie zurück zu Konfigurationsseite von mobile Services, und fügen Sie die Objekt-Id als app-Einstellung mit dem Namen **aad\_sales\_group\_id hinzu**. In diesem Lernprogramm wird die Objekt-ID der Gruppe als App-Einstellung verwendet, sodass die ID nicht anhand des Gruppennamens gesucht werden muss. Der Gruppenname kann sich ändern, die ID bleibt dagegen immer gleich.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)

