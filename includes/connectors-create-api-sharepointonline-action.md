Nu när du har lagt till en utlösare, intressant tiden att göra något med de data som genereras av utlösaren. Följ dessa steg för att lägga till den **SharePoint Online – skapa fil** åtgärd. Den här åtgärden skapar en fil i SharePoint Online varje gång den nya objekt utlösaren utlöses. 

Konfigurera den här åtgärden, måste du ange följande information. När du anger den här informationen kan att du upptäcka att det är lätt att använda data som genereras av utlösaren som indata för vissa av egenskaperna för den nya filen:

| Skapa filegenskap | Beskrivning |
| --- | --- |
| Webbplats-URL |Det här är URL: en för SharePoint Online-webbplats där du vill skapa den nya filen. Välj platsen i listan som visas. |
| Mappsökväg |Detta är mappen (på webbplatsens URL i föregående steg) där den nya filen ska placeras. Bläddra efter och Välj mappen. |
| Filnamn |Det här är namnet på filen som skapas. |
| Filinnehåll |Det innehåll som ska skrivas till filen. |

1. Välj **+ nytt steg** att lägga till åtgärden.  
   ![SharePoint online åtgärd bild 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Välj den **Lägg till en åtgärd** länk. Den här öppnas sökrutan där du kan söka efter alla åtgärder du vill göra. I det här exemplet är SharePoint åtgärder av intresse.    
   ![Bild av SharePoint online åtgärder 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Ange *sharepoint* att söka efter åtgärder som är relaterade till SharePoint.
4. Välj **SharePoint Online – skapa fil** som åtgärden som ska vidtas.   **Obs**: uppmanas du att godkänna din logikapp för att komma åt ditt konto för SharePoint om du inte har skapat en anslutning till SharePoint Online tidigare.    
   ![Bild av SharePoint online åtgärder 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. Den **skapa fil** styra öppnas.   
   ![Bild 4 av SharePoint online åtgärder](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Välj **webbplatsens URL** och bläddra till platsen där du vill skapa filen.     
   ![SharePoint online åtgärd bild 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Välj **mappsökväg** och bläddra till mappen där den nya filen ska placeras.  
   ![SharePoint online åtgärd bild 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Välj den **filnamn** kontroll och ange namnet på den fil som du vill skapa. Här kan du kan antingen ange filnamnet direkt eller du kan använda någon av egenskaperna från utlösaren som du skapade tidigare. Det gör du genom att välja egenskaper från listan över **utdata från när ett nytt objekt skapas**. Den här listan är endast visas när du har valt den **filnamn** kontroll. I den här genomgång jag valt ID (ID för det nya listobjektet) som namnet på filen som skapas av den **SharePoint Online – skapa fil** åtgärd.    
   ![Bild av SharePoint online åtgärder 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Välj den **filinnehåll** kontroll och ange det innehåll som ska skrivas till filen som ska skapas. Observera att du kan använda någon av egenskaperna från utlösaren som du skapade tidigare för filinnehåll. Välj bara egenskaperna i listan som visas. Alternativt kan du ange den **filinnehåll** text direkt i kontrollen. I det här exemplet jag valt vissa egenskaper och blanksteg och ett bindestreck mellan varje egenskap har lagts till.        
   ![Bild av SharePoint online åtgärder 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Spara ändringarna i ditt arbetsflöde  
11. Grattis, nu har du en helt funktionella logikapp som utlöses när ett nytt objekt läggs till i en SharePoint Online-lista. Appen skapar sedan en fil med hjälp av några av egenskaperna från det nya listobjektet.  Nu kan du testa den genom att skapa ett nytt objekt i SharePoint-listan. 

