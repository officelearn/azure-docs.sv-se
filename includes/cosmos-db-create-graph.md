Du kan nu använda datautforskaren för att skapa en grafbehållare och lägga till data i databasen. 

1. Klicka på **Datautforskaren** på navigeringsmenyn i Azure Portal. 
2. På bladet Datautforskaren klickar du på **Ny graf** och fyller sedan i sidan med följande information.

    ![Datautforskaren i Azure Portal](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|sample-database|ID för din nya databas. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Graf-id|sample-graph|ID för din nya graf. Samma teckenkrav gäller för grafnamn som databas-id.
    Lagringskapacitet| 10 GB|Låt standardvärdet vara kvar. Det här är databasens lagringskapacitet.
    Dataflöde|400 RU:er|Låt standardvärdet vara kvar. Du kan skala upp dataflödet senare om du vill minska svarstiden.
    Partitionsnyckeln|/userid|En partitionsnyckel som ska distribuera data jämnt till varje partition. Det är viktigt att välja rätt partitionsnyckel när man skapar en performant graf. Läs mer om det i [Designing for partitioning](../articles/cosmos-db/partition-data.md#designing-for-partitioning) (Utforma för partitionering).

3. När formuläret är ifyllt klickar du på **OK**.