Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en databas och samling. 

1. Klicka på **Data Explorer** > **ny samling**. 
    
    Den **lägga till samlingen** området visas längst till höger kan du behöva rulla för att se den.

    ![Azure-portalen Data Explorer, lägga till samlingen bladet](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. I den **lägga till samlingen** anger du inställningarna för den nya samlingen.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|Uppgifter|Ange *uppgifter* som namn på den nya databasen. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla /, \\, #, ? eller avslutande blanksteg.
    Samlings-id|Objekt|Ange *objekt* som namn på den nya samlingen. Samlings-ID har samma krav på tecken som databasnamn.
    Lagringskapacitet| Fast (10 GB)|Ändra värdet till **fast (10 GB)**. Det här värdet är databasens lagringskapacitet.
    Dataflöde|400 RU|Ändra genomflödet till 400 frågeenheter per sekund (RU/s). Lagringskapacitet måste anges till **fast (10 GB)** för att kunna tilldelas 400 RU/s genomströmning. Du kan skala upp dataflödet senare om du vill minska svarstiden. 
    Partitionsnyckeln|/category|Ange */category* som partitionsnyckel. En partitionsnyckel distribuerar data jämnt till varje partition i databasen. Mer information om partitionering finns [utformning för partitionering](../articles/cosmos-db/partition-data.md#designing-for-partitioning).

    Klicka på **OK**.

    Data Explorer visar den nya databasen och samlingen.

    ![Azure-portalen Data Explorer visar ny databas och samling](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)