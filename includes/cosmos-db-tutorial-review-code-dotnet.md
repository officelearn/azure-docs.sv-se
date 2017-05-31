Vi gör en snabb genomgång av vad som händer i appen. Öppna filen DocumentDBRepository.cs så ser du att de här kodraderna skapar DocumentDB-resurserna. 

* DocumentClient initieras.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* En ny databas skapas.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* En ny samling skapas.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```
