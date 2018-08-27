Nu när du har lagt till en utlösare, intressant tiden att göra något med de data som genereras av utlösaren. Följ dessa steg för att lägga till en den **SFTP - extrahera mappen** åtgärd. Den här åtgärden extraherar innehållet i en fil om villkoren är uppfyllda. 

Konfigurera den här åtgärden, måste du ange följande information. Du kommer att märka att det är lätt att använda data som genereras av utlösaren som indata för vissa av egenskaperna för den nya filen:

| SFTP - extrahera mappegenskap | Beskrivning |
| --- | --- |
| Sökväg till källarkivfil |Det här är sökvägen för den fil som extraheras. Du kan välja en token från en tidigare åtgärd eller bläddra SFTP-server för att hitta sökvägen till filen. |
| Sökväg till målmappen |Det här är den sökväg där de extrahera filerna ska placeras. Du kan välja en token från en tidigare åtgärd som sökvägen eller bläddra SFTP-servern och välj en sökväg. |
| Vill du skriva över? |Anger om en fil med samma namn som den hämtade filen finns i sökvägen till målmappen om den befintliga filen ska skrivas över eller inte. |

Låt oss komma igång med att lägga till åtgärden för att extrahera filerna om det villkor som definierats tidigare utvärderas till *SANT*. 

1. Välj **Lägg till en åtgärd**.        
   ![SFTP åtgärd villkor bild 6](./media/connectors-create-api-sftp/condition-6.png)   
2. Välj den **SFTP - extrahera mappen** åtgärd      
   ![Bild av SFTP åtgärder för villkoret 7](./media/connectors-create-api-sftp/condition-7.png)   
3. Välj **filsökväg till källarkiv**              
   ![SFTP åtgärd villkor bild 9](./media/connectors-create-api-sftp/condition-9.png)   
4. Välj den **filsökväg** token. Detta anger att du vill använda sökvägen till filen som utlösaren hittas som sökvägen till källarkivfil.           
   ![Bild av SFTP åtgärder för villkoret 10](./media/connectors-create-api-sftp/condition-10.png)   
5. Välj **sökväg till målmappen**           
   ![Bild av SFTP åtgärder för villkoret 11](./media/connectors-create-api-sftp/condition-11.png)   
6. Välj den **filsökväg** token. Detta anger att du ska använda sökvägen till filen som utlösaren hittades som sökväg för de extrahera filerna.   
7. Ange *\ExtractedFile* i den **sökväg till målmappen** kontroll. Du kan göra detta efter filen sökväg token i kontrollen mål mapp-sökväg.         
   ![Bild av SFTP åtgärder för villkoret 12](./media/connectors-create-api-sftp/condition-12.png)   
8. Ange *SANT* i den **överskrivning?* kontroll som visar att befintliga filer ska skrivas över om de har samma namn som de extrahera filerna.      
   ![Bild av SFTP åtgärder för villkoret 13](./media/connectors-create-api-sftp/condition-13.png)   
9. Spara ändringarna i ditt arbetsflöde  

