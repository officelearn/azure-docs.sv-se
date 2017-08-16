Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en grafdatabas. 

1. Klicka på **Data Explorer (förhandsversion)** på den vänstra navigeringsmenyn i Azure Portal. 
2. På bladet **Datautforskaren (förhandsversion)** klickar du på **New Graph** (Ny graf) och fyller sedan i sidan med följande information.

    ![Datautforskaren i Azure-portalen](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|sample-database|ID för din nya databas. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Graf-id|sample-graph|ID för din nya graf. Samma teckenkrav gäller för grafnamn som databas-id.
    Lagringskapacitet| 10 GB|Låt standardvärdet vara kvar. Det här är databasens lagringskapacitet.
    Dataflöde|400 RU:er|Låt standardvärdet vara kvar. Du kan skala upp dataflödet senare om du vill minska svarstiden.
    RU/m|Av|Låt standardvärdet vara kvar. Om du behöver hantera toppar i arbetsbelastningar senare kan du aktivera funktionen [RU/m](../articles/cosmos-db/request-units-per-minute.md) då.
    Partitionsnyckeln|/userid|En partitionsnyckel som ska distribuera data jämnt till varje partition. Det är viktigt att välja rätt partitionsnyckel när man skapar en performant graf. Läs mer om det i [Designing for partitioning](../articles/cosmos-db/partition-data.md#designing-for-partitioning) (Utforma för partitionering).

3. När formuläret är ifyllt klickar du på **OK**.