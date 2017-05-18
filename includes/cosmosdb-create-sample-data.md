Du kan nu lägga till data till din nya samling med datautforskaren.

1. Den nya databasen visas på panelen Samlingar i datautforskaren. Expandera **Objekt**-databasen, expandera **ToDoList**-samlingen, klicka på **Dokument** och klicka sedan på **Nya dokument**. 

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/cosmosdb-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. Lägg nu till några nya dokument i samlingen med följande struktur, där du infogar unika värden för ID i varje dokument och ändrar de andra egenskaperna som du vill. Dina nya dokument kan ha vilken struktur du vill eftersom Azure Cosmos DB inte kräver något schema för dina data.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     Du kan nu använda frågor i datautforskaren för att hämta dina data. Som standard använder datautforskaren SELECT * FROM c för att hämta alla dokument i samlingen, men du kan ändra det till SELECT * FROM c ORDER BY c.name ASC, för att returnera alla dokument i stigande bokstavsordning för namnegenskapen. 
 
     Du kan även använda datautforskaren för att skapa lagrade procedurer, UDF:er och utlösare för att utföra affärslogik på serversidan. Datautforskaren visar all den inbyggda programmässiga dataåtkomsten som finns tillgänglig i API:erna, men ger enkel åtkomst till dina data i Azure Portal.