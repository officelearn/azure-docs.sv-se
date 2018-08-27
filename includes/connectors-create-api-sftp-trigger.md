Vi lägger till en utlösare.

1. Ange *sftp* i sökrutan på logic apps designer och markera den **SFTP - när en fil läggs till eller ändras** utlösare   
   ![SFTP-utlösarbild 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. Den **när en fil läggs till eller ändras** kontrollen öppnas  
   ![Bild 2 till SFTP-utlösare](./media/connectors-create-api-sftp/trigger-2.png)  
3. Välj den **...**  finns på höger sida av kontrollen. Då öppnas mappen väljarkontrollen  
   ![Bild 3 till SFTP-utlösare](./media/connectors-create-api-sftp/action-1.png)  
4. Välj den **SFTP** väljer rotmappen som mappen som ska övervakas för nya eller ändrade filer. Observera rotmappen visas nu i den **mappen** kontroll.  
   ![Bild 4 till SFTP-utlösare](./media/connectors-create-api-sftp/action-2.png)   

Logikappen har nu konfigurerats med en utlösare som börjar en körning av andra utlösare och åtgärder i arbetsflödet när en fil ändras eller skapas i den specifika SFTP-mappen. 

> [!NOTE]
> Det måste innehålla minst en utlösare och en åtgärd för en logikapp ska fungera. Följ stegen i nästa avsnitt för att lägga till en åtgärd.  
> 
> 

