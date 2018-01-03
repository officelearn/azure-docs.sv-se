Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en grafdatabas. 

1. Välj i Azure-portalen på menyn till vänster, **Data Explorer (förhandsgranskning)**.

2. Under **Data Explorer (förhandsgranskning)**väljer **nytt diagram**. Fyll i sidan med hjälp av följande information:

    ![Datautforskaren i Azure-portalen](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|sample-database|Ange *sample-database* som namn på den nya databasen. Databasnamn måste vara mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Graf-id|sample-graph|Ange *sample-graph* som namnet på den nya samlingen. Samma teckenkrav gäller för grafnamn som databas-id.
    Lagringskapacitet| 10 GB|Låt standardvärdet vara kvar. Det här är databasens lagringskapacitet.
    Dataflöde|400 RU:er|Låt standardvärdet vara kvar. Du kan skala upp dataflödet senare om du vill minska svarstiden.

3. När formuläret fylls, väljer du **OK**.
