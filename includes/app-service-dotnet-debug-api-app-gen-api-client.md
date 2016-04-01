## Generieren eines API-App-Clients 

Die API-App-Tools in Visual Studio erleichtern das Generieren von C#-Code, der Ihre Azure-API-Apps aus Desktop-, Store und mobilen Apps aufruft. 

1. Öffnen Sie in Visual Studio die Projektmappe mit der API-app aus dem [Erstellen einer API-app](../article/app-service-api/app-service-dotnet-create-api-app.md) Thema. 

2. Von **Projektmappen-Explorer**, mit der rechten Maustaste auf die Projektmappe, und wählen Sie die **Hinzufügen** > **Neues Projekt**.

    ![Neues Projekt hinzufügen](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)

3. In der **Neues Projekt hinzufügen** im Dialogfeld die folgenden Schritte ausführen:

    1. Wählen Sie die **Windows-Desktop** Kategorie.
    
    2. Wählen Sie die **Konsolenanwendung** -Projektvorlage.
    
    3. Benennen Sie das Projekt.
    
    4. Klicken Sie auf **OK** in Ihrer vorhandenen Projektmappe das neue Projekt zu generieren.
    
    ![Neues Projekt hinzufügen](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)

4. Mit der rechten Maustaste in des neu erstellte Konsolenanwendungsprojekt, und wählen Sie **Hinzufügen** > **Azure API-App-Client**. 

    ![Neuen Client hinzufügen](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
    
5. In der **Microsoft Azure API-App-Client hinzufügen** im Dialogfeld die folgenden Schritte ausführen: 

    1. Wählen Sie die **herunterladen** Option. 
    
    2. Wählen Sie in der Dropdownliste die API-App aus, die Sie zuvor erstellt haben. 
    
    3. Klicken Sie auf **OK**. 

    ![Bildschirm zur Generierung](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)

    Der Assistent lädt die API-Metadatendatei herunter und generiert eine typisierte Schnittstelle zum Aufrufen der API-App.

    ![Generierungsvorgang wird ausgeführt](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)

    Nachdem die Codegenerierung abgeschlossen ist, wird im Projektmappen-Explorer ein neuer Ordner mit dem Namen der API-App angezeigt. Dieser Ordner enthält den Code, der den Client und die Datenmodelle implementiert. 

    ![Generierung abgeschlossen](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)

6. Öffnen Sie die **Program.cs** Datei aus dem Projektstamm, und Ersetzen Sie die **Main** -Methode durch den folgenden Code: 

        static void Main(string[] args)
        {
            var client = new ContactsList();
    
            // Send GET request.
            var contacts = client.Contacts.Get();
            foreach (var c in contacts)
            {
                Console.WriteLine("{0}: {1} {2}",
                    c.Id, c.Name, c.EmailAddress);
            }
    
            // Send POST request.
            client.Contacts.Post(new Models.Contact
            {
                EmailAddress = "lkahn@contoso.com",
                Name = "Loretta Kahn",
                Id = 4
            });
    
            Console.WriteLine("Finished");
            Console.ReadLine();
        }

## Testen des API-App-Clients

Nachdem die API-App codiert wurde, müssen Sie den Code testen.

1. Öffnen Sie **Projektmappen-Explorer**.

2. Klicken Sie mit der rechten Maustaste auf die Konsolenanwendung, die Sie im vorherigen Abschnitt erstellt haben.

3. Wählen Sie im Kontextmenü der Konsolenanwendung **Debuggen > neue Instanz starten**. 

4. Es sollte sich ein Konsolenfenster öffnen, in dem alle Kontakte angezeigt werden. 

    ![Ausführen einer Konsolen-App](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)

5. Drücken Sie **EINGABETASTE** an das Konsolenfenster zu schließen.          


