Det här villkoret kommer att utvärdera fältet e-postadress för varje ny Salesforce lead. Om e-postadressen innehåller *amazon.com*, blir resultatet villkoret *SANT*.

1. Välj **+ nytt steg**.  
   ![Salesforce villkoret bild 1](./media/connectors-create-api-salesforce/condition-1.png)   
2. Välj **Lägg till ett villkor**.    
   ![Bild 2 till Salesforce villkor](./media/connectors-create-api-salesforce/condition-2.png)  
3. Välj **väljer ett värde**.    
   ![Bild 3 till Salesforce villkor](./media/connectors-create-api-salesforce/condition-3.png)  
4. Välj den *e-post* token från lead av utlösaren.    
   ![Bild 4 till Salesforce villkor](./media/connectors-create-api-salesforce/condition-4.png)  
5. Välj *innehåller*.      
   ![Bild 5 till Salesforce villkor](./media/connectors-create-api-salesforce/condition-5.png)  
6. Välj **väljer ett värde** längst ned i kontrollen.     
   ![Bild 6 till Salesforce villkor](./media/connectors-create-api-salesforce/condition-6.png)  
7. Ange *amazon.com* leda för som du vill utvärdera e-postadressen för det nya värdet. Om e-postadressen innehåller *amazon.com*, villkoret utvärderas till *SANT* och andra steg i din logikapp kan fortsätta.    
   ![Bild 7 till Salesforce villkor](./media/connectors-create-api-salesforce/condition-7.png)  
8. Spara dina logic apps.  

