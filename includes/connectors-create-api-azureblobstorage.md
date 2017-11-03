### <a name="prerequisites"></a>Krav
* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [Azure Blob Storage-konto](../articles/storage/common/storage-create-storage-account.md) inklusive lagringskontonamn och dess snabbtangent. Den här informationen visas i egenskaperna för storage-konto i Azure-portalen. Läs mer om [Azure Storage](../articles/storage/common/storage-introduction.md).

Innan du använder Azure Blob Storage-konto i en logikapp kan du ansluta till Azure Blob Storage-konto. Du kan göra detta enkelt i din logikapp på Azure-portalen.  

Anslut till ditt Azure Blob Storage-konto med följande steg:  

1. Skapa en logikapp. Lägg till en utlösare i Logic Apps-designer och sedan lägga till en åtgärd. Välj **visa Microsoft hanterade API: er** i nedrullningsbara listan, och ange sedan ”blob” i sökrutan. Välj något av åtgärderna som:  
   
    ![Azure Blob Storage anslutning skapas steg](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Om du inte tidigare har skapat alla anslutningar till Azure storage, tillfrågas om anslutningsinformationen:   
   
    ![Azure Blob Storage anslutning skapas steg](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. Ange information om lagringskonto. Egenskaper med en asterisk krävs.
   
   | Egenskap | Information |
   | --- | --- |
   | Anslutningsnamn * |Ange ett namn för anslutningen. |
   | Azure Storage-kontonamnet * |Ange namnet på lagringskontot. Lagringskontonamn visas i egenskaperna för lagring i Azure-portalen. |
   | Azure Storage åtkomstnyckel * |Ange lagringskontots åtkomstnyckel. Åtkomstnycklarna visas i egenskaperna för lagring i Azure-portalen. |
   
    Dessa autentiseringsuppgifter används för att verifiera din logikapp för att ansluta och komma åt dina data. 
4. Välj **Skapa**.
5. Observera att anslutningen har skapats. Nu kan fortsätta med andra steg i din logikapp: 
   
    ![Azure Blob Storage anslutning skapas steg](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

