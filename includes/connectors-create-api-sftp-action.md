Nu när du har lagt till en utlösare intressanta dess tid att göra något med de data som genereras av utlösaren. Följ dessa steg för att lägga till en den **SFTP - extrahera mappen** åtgärd. Den här åtgärden extraherar innehållet i en fil om villkoren är uppfyllda. 

Så här konfigurerar du den här åtgärden, måste du ange följande information. Du kommer att märka att det är lätt att använda data som genereras av utlösaren som indata för vissa av egenskaperna för den nya filen:

| SFTP - extrahera mappen egenskapen | Beskrivning |
| --- | --- |
| Sökvägen till källfilen Arkiv |Det här är sökvägen för den fil som extraheras. Du kan välja en token från en tidigare åtgärd eller bläddra SFTP-server för att hitta sökvägen till filen. |
| Målmappens sökväg |Det här är den sökväg där de extraherade filerna ska placeras. Du kan välja en token från en tidigare åtgärd som målsökvägen eller bläddra SFTP-servern och välj en sökväg. |
| Vill du ersätta? |Anger om en fil med samma namn som den hämtade filen finns i sökvägen till målmappen om ska skrivas över den befintliga filen eller inte. |

Vi börjar med att lägga till åtgärden för att extrahera filerna om villkoret som definierats tidigare evalueras till *SANT*. 

1. Välj **lägga till en åtgärd**.        
   ![SFTP åtgärd villkoret bild 6](./media/connectors-create-api-sftp/condition-6.png)   
2. Välj den **SFTP - extrahera mappen** åtgärd      
   ![Bild 7 till SFTP åtgärd villkor](./media/connectors-create-api-sftp/condition-7.png)   
3. Välj **sökvägen till källfilen Arkiv**              
   ![SFTP åtgärd villkoret bild 9](./media/connectors-create-api-sftp/condition-9.png)   
4. Välj den **filsökväg** token. Detta anger att du vill använda filsökvägen till den fil som utlösaren hittades som sökvägen till källfilen Arkiv.           
   ![SFTP åtgärd villkoret bild 10](./media/connectors-create-api-sftp/condition-10.png)   
5. Välj **målmappens sökväg**           
   ![Bild av SFTP åtgärder för villkoret 11](./media/connectors-create-api-sftp/condition-11.png)   
6. Välj den **filsökväg** token. Detta anger att du använder filsökvägen till den fil som utlösaren hittades som målsökvägen för de extraherade filerna.   
7. Ange *\ExtractedFile* i den **målmappsökvägen** kontroll. Gör det bara efter filen sökväg token i kontrollen mål mapp-sökväg.         
   ![Bild av SFTP åtgärder för villkoret 12](./media/connectors-create-api-sftp/condition-12.png)   
8. Ange *SANT* i den **överskrivning?* kontroll för att indikera att befintliga filer ska skrivas över om de har samma namn som de extraherade filerna.      
   ![Bild av SFTP åtgärder för villkoret 13](./media/connectors-create-api-sftp/condition-13.png)   
9. Spara ändringarna i arbetsflödet  

