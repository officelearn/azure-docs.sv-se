Lägg till en utlösare.

1. Ange *sftp* i sökrutan på logic apps designer väljer den **SFTP - när en fil har lagts till eller ändrats** utlösare   
   ![SFTP utlösarbild 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. Den **när en fil har lagts till eller ändrats** kontrollen öppnas  
   ![Bild 2 till SFTP utlösare](./media/connectors-create-api-sftp/trigger-2.png)  
3. Välj den **...**  finns på höger sida av kontrollen. Då öppnas väljarkontrollen mapp  
   ![Bild 3 till SFTP utlösare](./media/connectors-create-api-sftp/action-1.png)  
4. Välj den **SFTP** välja rotmappen som mappen för att övervaka för nya eller ändrade filer. Meddelande rotmappen visas nu i den **mappen** kontroll.  
   ![Bild 4 till SFTP utlösare](./media/connectors-create-api-sftp/action-2.png)   

Din logikapp har nu konfigurerats med en utlösare som börjar på andra utlösare och åtgärder i arbetsflödet körs när en fil ändras eller skapas i mappen specifika SFTP. 

> [!NOTE]
> Det måste innehålla minst en utlösare och en åtgärd för en logikapp ska fungera. Följ stegen i nästa avsnitt för att lägga till en åtgärd.  
> 
> 

