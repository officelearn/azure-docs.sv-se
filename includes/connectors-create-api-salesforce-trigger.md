I den här genomgången kommer du lära dig hur du använder den **Salesforce - när ett objekt skapas** trigger för att initiera en logik app arbetsflödet när en ny lead skapas i ditt Salesforce.

> [!NOTE]
> Hämta uppmanas du att logga in på ditt Salesforce-konto om du inte redan har skapat en *anslutning* till Salesforce.  
> 
> 

1. Ange *salesforce* i sökrutan på logic apps designer väljer den **Salesforce - när ett objekt skapas** utlösare.  
   ![Salesforce utlösarbild 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. Den **när ett objekt skapas** kontrollen visas.  
   ![Bild 2 till Salesforce-utlösare](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Välj den **objekttyp** Välj *leda* från listan över objekt. I det här steget anger du att du skapar en utlösare som meddelar logikappen när en ny lead skapas i Salesforce.   
   ![Bild 3 till Salesforce-utlösare](./media/connectors-create-api-salesforce/trigger-3.png)   
4. Det var allt. Du har skapat utlösaren. Dock måste du skapa minst en åtgärd för att göra detta en giltig logikapp.    
   ![Bild 4 till Salesforce-utlösare](./media/connectors-create-api-salesforce/trigger-4.png)   

Din logikapp har nu konfigurerats med en utlösare som börjar på andra utlösare och åtgärder i arbetsflödet körs när ett nytt objekt skapas i ditt Salesforce.  

