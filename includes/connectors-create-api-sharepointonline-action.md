Nu när du har lagt till en utlösare intressanta dess tid att göra något med de data som genereras av utlösaren. Följ dessa steg för att lägga till den **SharePoint Online - skapa fil** åtgärd. Den här åtgärden skapar en fil i SharePoint Online varje gång den nya artikel utlösaren utlöses. 

Så här konfigurerar du den här åtgärden, måste du ange följande information. När du anger den här informationen ser du att det är lätt att använda data som genereras av utlösaren som indata för vissa av egenskaperna för den nya filen:

| Skapa filen egenskap | Beskrivning |
| --- | --- |
| Webbadress |Detta är Webbadressen till SharePoint Online-webbplats där du vill skapa den nya filen. Välj platsen i listan som visas. |
| Mappsökväg |Detta är mappen (på webbplatsens URL i föregående steg) där den nya filen ska placeras. Bläddra efter och Välj mappen. |
| Filnamn |Detta är namnet på filen som skapas. |
| Filinnehåll |Det innehåll som ska skrivas till filen. |

1. Välj **+ nytt steg** att lägga till åtgärden.  
   ![SharePoint online åtgärd bild 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Välj den **lägga till en åtgärd** länk. Den här öppnas sökrutan där du kan söka efter något du vill göra. I det här exemplet är SharePoint åtgärder av intresse.    
   ![Bild 2 till SharePoint online åtgärd](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Ange *sharepoint* att söka efter åtgärder som rör SharePoint.
4. Välj **SharePoint Online - skapa filen** som åtgärden som ska vidtas.   **Obs**: uppmanas du att auktorisera logikappen åtkomst till SharePoint-konto om du inte har skapat en anslutning till SharePoint Online tidigare.    
   ![Bild 3 till SharePoint online åtgärd](./media/connectors-create-api-sharepointonline/action-3.png)    
5. Den **skapa fil** styra öppnas.   
   ![Bild 4 till SharePoint online åtgärd](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Välj **Webbadress** och bläddra till platsen där du vill skapa filen.     
   ![Bild 5 till SharePoint online åtgärd](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Välj **mappsökväg** och bläddra till mappen där den nya filen ska placeras.  
   ![Bild 6 till SharePoint online åtgärd](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Välj den **filnamn** styr och ange namnet på filen som du vill skapa. Här kan du kan ange filnamnet direkt eller du kan använda någon av egenskaperna från utlösare som du skapade tidigare. Det gör du genom att välja egenskaper från listan över **utdata från när ett nytt objekt skapas**. Den här listan visas endast när du har valt den **filnamn** kontroll. I den här walkthough jag valt ID (ID för det nya listobjektet) som namnet på filen som skapas av den **SharePoint Online - skapa fil** åtgärd.    
   ![Bild 7 till SharePoint online åtgärd](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Välj den **filen innehåll** styr och ange det innehåll som ska skrivas till filen som ska skapas. Observera att du kan använda någon av egenskaperna från utlösare som du skapade tidigare för filinnehåll. Välj bara egenskaperna i listan som visas. Alternativt kan du ange den **filen innehåll** text direkt i kontrollen. I det här exemplet jag valt vissa egenskaper och lägga till blanksteg och ett bindestreck mellan varje egenskap.        
   ![SharePoint online åtgärd bild 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Spara ändringarna i arbetsflödet  
11. Grattis, du har nu en fullt fungerande logikappen som utlöses när ett nytt objekt läggs till en lista med SharePoint Online. Appen skapar sedan en fil med hjälp av vissa av egenskaperna från det nya listobjektet.  Nu kan du testa den genom att skapa ett nytt objekt i SharePoint-listan. 

