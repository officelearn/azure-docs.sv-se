Det här villkoret utvärderas e-adressfältet i varje ny lead i Salesforce. Om e-postadressen innehåller *amazon.com*, blir resultatet för villkoret *SANT*.

1. Välj **+ nytt steg**.  
   ![Salesforce villkor bild 1](./media/connectors-create-api-salesforce/condition-1.png)   
2. Välj **Lägg till ett villkor**.    
   ![Salesforce villkor bild 2](./media/connectors-create-api-salesforce/condition-2.png)  
3. Välj **Välj ett värde**.    
   ![Salesforce villkor bild 3](./media/connectors-create-api-salesforce/condition-3.png)  
4. Välj den *e-post* token från lead för utlösaren.    
   ![Salesforce villkor bild 4](./media/connectors-create-api-salesforce/condition-4.png)  
5. Välj *innehåller*.      
   ![Salesforce villkor bild 5](./media/connectors-create-api-salesforce/condition-5.png)  
6. Välj **Välj ett värde** längst ned i kontrollen.     
   ![Salesforce villkor bild 6](./media/connectors-create-api-salesforce/condition-6.png)  
7. Ange *amazon.com* som värdet som du vill utvärdera e-postadressen till den nya leda för. Om e-postadressen innehåller *amazon.com*, villkoret utvärderas till *SANT* och andra steg i logikappen kan fortsätta.    
   ![Salesforce villkor bild 7](./media/connectors-create-api-salesforce/condition-7.png)  
8. Spara dina logic apps.  

