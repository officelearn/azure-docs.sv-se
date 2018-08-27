I den här genomgången kommer du lära dig hur du använder den **Salesforce - när ett objekt skapas** utlösare för att initiera en logikapparbetsflöde när ett nytt lead skapas i Salesforce.

> [!NOTE]
> Hämta uppmanas du att logga in på ditt Salesforce-konto om du inte redan har skapat en *anslutning* till Salesforce.  
> 
> 

1. Ange *salesforce* i sökrutan på logic apps designer och markera den **Salesforce - när ett objekt skapas** utlösaren.  
   ![Salesforce-utlösarbild 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. Den **när ett objekt skapas** kontroll visas.  
   ![Salesforce-utlösarbild 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Välj den **objekttyp** därefter *leda* i listan med objekt. I det här steget anger du att du skapar en utlösare som meddelar din logikapp när en ny lead skapas i Salesforce.   
   ![Bild 3 till Salesforce-utlösare](./media/connectors-create-api-salesforce/trigger-3.png)   
4. Det var allt. Du har skapat utlösaren. Dock måste du skapa minst en åtgärd för att göra detta en giltig logikapp.    
   ![Bild 4 till Salesforce-utlösare](./media/connectors-create-api-salesforce/trigger-4.png)   

Logikappen har nu konfigurerats med en utlösare som börjar en körning av andra utlösare och åtgärder i arbetsflödet när ett nytt objekt skapas i Salesforce.  

