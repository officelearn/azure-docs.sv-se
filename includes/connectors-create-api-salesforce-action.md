Nu när du har lagt till ett villkor intressanta dess tid att göra något med de data som genereras av utlösaren. Följ dessa steg för att lägga till den **Salesforce - Get-objektet** åtgärd. Den här åtgärden kommer att hämta data varje gång en ny lead skapas. Du kan också lägga till en andra åtgärd som ska använda data från Salesforce - Get objektet åtgärden att skicka ett e-postmeddelande med hjälp av Office 365-kopplingen.  

Så här konfigurerar du den här åtgärden, måste du ange följande information. Du kommer att märka att det är lätt att använda data som genereras av utlösaren som indata för vissa av egenskaperna för den nya filen:

| Skapa filen egenskap | Beskrivning |
| --- | --- |
| Objekttyp |Detta är typ av Salesforce-objekt som du är intresserad av. Exempel är Lead, konto, osv. |
| Objekt-ID |Detta representerar en identifierare för objektet. |

1. Välj **lägga till en åtgärd** länk. Den här öppnas sökrutan där du kan söka efter något du vill göra. I det här exemplet är Salesforce åtgärder av intresse.      
   ![Bild av åtgärder för Salesforce 1](./media/connectors-create-api-salesforce/action-1.png)  
2. Ange *salesforce* att söka efter åtgärder som rör salesforce.
3. Välj **Salesforce - Get-objektet** som åtgärden som ska vidtas.   **Obs**: uppmanas du att auktorisera logikappen åtkomst till ditt Salesforce-konto om du inte har gjort det tidigare.    
   ![Bild av åtgärder för Salesforce 2](./media/connectors-create-api-salesforce/action-2.png)    
4. Den **Get objektet** styra öppnas.  
5. Välj *leda* som objekttyp.
6. Välj den **objekt-ID** kontroll.
7. Välj **...**  att expandera listan över token som kan användas som indata för åtgärder.       
   ![Bild av åtgärder för Salesforce 3](./media/connectors-create-api-salesforce/action-3.png)    
8. Välj **leda ID** styra öppnas.   
   ![Bild 4 till Salesforce åtgärd](./media/connectors-create-api-salesforce/action-4.png)     
9. Observera att leda ID-token är nu i kontrollen objekt-ID som anger att åtgärden Get-objekt att söka efter ett lead med ett ID som är lika med lead-ID för lead som utlöses den här logikapp.  
   ![Bild 5 till Salesforce åtgärd](./media/connectors-create-api-salesforce/action-5.png)  
10. Spara ditt arbete. Det stämmer, du har lagt till händelse för Get-objektet till din logikapp. Get-objektet kontrollen ska se ut så här:    
    ![Bild av åtgärder för Salesforce 6](./media/connectors-create-api-salesforce/action-6.png)  

Nu när du har lagt till en åtgärd för att få ett lead kan behöva du göra något intressant med det nyligen skapade leadet. I ett företag kanske du vill skicka ett e-postmeddelande för att meddela en distributionslista som en ny lead har skapats. Nu ska vi använda Office 365-kopplingen för att skicka ett e-postmeddelande med en del av relevant information från det nya lead objektet i Salesforce.  

1. Välj **lägga till en åtgärd** ange *e-post* i sökkontrollen. Detta filtrerar åtgärderna som är relaterade till e- posten.  
2. Välj den **Office 365 Outlook - skicka ett e-post** listobjekt. Om du inte redan har skapat en *anslutning* till ditt Office 365-konto uppmanas du att ange dina autentiseringsuppgifter för Office 365 för att skapa den nu. När du är klar i **skickar ett e-** styra öppnas.        
   ![Bild 7 till Salesforce åtgärd](./media/connectors-create-api-salesforce/action-7.png)  
3. Ange e-postadress som du vill skicka e-post till i den **till** kontroll.
4. I den **ämne** styra, ange *nya leda skapade* – Välj den *företagets* token. Detta visas den *företagets* från den nya lead som skapats i Salesforce.  
5. I den **brödtext** kontroll, kan du välja någon av token från objektet lead och du kan också ange den text som du vill visa i brödtexten i e-postmeddelandet. Här är ett exempel:  
   ![Bild av åtgärder för Salesforce 8](./media/connectors-create-api-salesforce/action-8.png)   
6. Spara ditt arbetsflöde.  

Det var allt. Din logikapp är slutförd.  

Nu kan du testa logikappen: skapa en ny lead som uppfyller de villkor som du skapade i Salesforce,.  Om du har följt den här genomgången fullständigt bara skapa ett lead med en e-postadress som innehåller *amazon.com* i den. Efter några sekunder logikappen ska utlösas och resultatet kan se ut ungefär så här:  
![Bild av åtgärder för Salesforce 9](./media/connectors-create-api-salesforce/action-9.png)  

