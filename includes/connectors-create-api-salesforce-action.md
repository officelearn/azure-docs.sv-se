Nu när du har lagt till ett villkor, intressant tiden att göra något med de data som genereras av utlösaren. Följ dessa steg för att lägga till den **Salesforce - hämta objekt** åtgärd. Den här åtgärden hämta data varje gång ett nytt lead skapas. Du kan också lägga till en andra åtgärd som ska använda data från Salesforce - Get en händelse för objektet att skicka ett e-postmeddelande med Office 365-kopplingen.  

Konfigurera den här åtgärden, måste du ange följande information. Du kommer att märka att det är lätt att använda data som genereras av utlösaren som indata för vissa av egenskaperna för den nya filen:

| Skapa filegenskap | Beskrivning |
| --- | --- |
| Objekttyp |Det här är typ av Salesforce-objekt som du är intresserad av. Exempel är Lead, konto, osv. |
| Objekt-ID |Detta representerar en identifierare för objektet. |

1. Välj **Lägg till en åtgärd** länk. Den här öppnas sökrutan där du kan söka efter alla åtgärder du vill göra. I det här exemplet är Salesforce-åtgärder av intresse.      
   ![Bild av åtgärder för Salesforce 1](./media/connectors-create-api-salesforce/action-1.png)  
2. Ange *salesforce* att söka efter åtgärder som rör salesforce.
3. Välj **Salesforce - hämta objekt** som åtgärden som ska vidtas.   **Obs**: uppmanas du att godkänna din logikapp för att få åtkomst till ditt Salesforce-konto om du inte har gjort det tidigare.    
   ![Bild av åtgärder för Salesforce 2](./media/connectors-create-api-salesforce/action-2.png)    
4. Den **hämta objekt** styra öppnas.  
5. Välj *leda* som objekttyp.
6. Välj den **objekt-ID** kontroll.
7. Välj **...**  att expandera listan över tokens som kan användas som indata för åtgärder.       
   ![Bild av åtgärder för Salesforce 3](./media/connectors-create-api-salesforce/action-3.png)    
8. Välj **leda ID** styra öppnas.   
   ![Bild av åtgärder för Salesforce 4](./media/connectors-create-api-salesforce/action-4.png)     
9. Observera att leda ID-token är nu i objekt-ID-kontroll, som anger att åtgärden Get-objektet ska söka efter ett lead med ett ID som är lika med potentiell kund-ID för lead som utlöste den här logikappen.  
   ![Bild av åtgärder för Salesforce 5](./media/connectors-create-api-salesforce/action-5.png)  
10. Spara ditt arbete. Det var allt – du har lagt till åtgärden Get-objektet till din logikapp. Din kontroll för Get-objektet ska se ut så här:    
    ![Bild av åtgärder för Salesforce 6](./media/connectors-create-api-salesforce/action-6.png)  

Nu när du har lagt till en åtgärd för att få ett lead kan du göra något intressant med det nyligen skapade leadet. Företag kan du skicka ett e-postmeddelande för att meddela en distributionslista att ett nytt lead har skapats. Vi använder Office 365-anslutning för att skicka ett e-postmeddelande med några av relevant information från det nya objektet lead i Salesforce.  

1. Välj **Lägg till en åtgärd** ange *e-post* för search-kontrollen. Detta filtrerar åtgärderna som de som är relaterade till att skicka och ta emot e-post.  
2. Välj den **Office 365 Outlook – skicka ett e-postmeddelande** listobjekt. Om du inte redan har skapat en *anslutning* till ditt Office 365-konto uppmanas du att ange dina autentiseringsuppgifter för Office 365 för att skapa den nu. När du är klar kan den **skicka ett e-postmeddelande** styra öppnas.        
   ![Bild av åtgärder för Salesforce 7](./media/connectors-create-api-salesforce/action-7.png)  
3. Ange e-postadress som du vill skicka e-post till i den **till** kontroll.
4. I den **ämne** genom att ange *nytt Lead skapas* – och välj sedan den *företagets* token. Detta visar den *företagets* från ny lead skapas i Salesforce.  
5. I den **brödtext** kontroll, kan du välja någon av token från det nya lead-objektet och du kan också ange den text som du vill visa i brödtexten i e-postmeddelandet. Här är ett exempel:  
   ![Bild av åtgärder för Salesforce 8](./media/connectors-create-api-salesforce/action-8.png)   
6. Spara ditt arbetsflöde.  

Det var allt. Logikappen har nu slutförts.  

Nu kan du testa din logikapp: skapa ett nytt lead som uppfyller de villkor som du skapade i Salesforce.  Om du har följt den här genomgången fullständigt bara skapa ett lead med en e-postadress som innehåller *amazon.com* i den. Efter några sekunder logikappen ska aktiveras och resultatet kan se ut ungefär så här:  
![Bild av åtgärder för Salesforce 9](./media/connectors-create-api-salesforce/action-9.png)  

