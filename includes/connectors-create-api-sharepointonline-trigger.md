I det här exemplet jag visar hur du använder den **SharePoint Online – när ett nytt objekt skapas** utlösare för att initiera en logikapparbetsflöde när ett nytt objekt skapas i en SharePoint Online-lista.

> [!NOTE]
> Hämta uppmanas du att logga in på ditt konto för SharePoint om du inte redan har skapat en *anslutning* till SharePoint Online.  
> 
> 

1. Ange *sharepoint* i sökrutan på logic apps designer och markera den **SharePoint Online – när ett nytt objekt skapas** utlösaren.  
   ![Bild för SharePoint online-utlösare ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. Den **när ett nytt objekt skapas** kontroll visas.  
   ![Bild 2 till SharePoint online-utlösare](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Välj en **webbadress**. Det här är den online SharePoint-webbplats som du vill övervaka efter nya objekt som utlöser arbetsflödet.  
   ![Bild 3 till SharePoint online-utlösare](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Välj en **listnamn**. Det här är listan på SharePoint Online-webbplatsen du vill övervaka efter nya objekt som utlöser arbetsflödet.  
   ![Bild 4 till SharePoint online-utlösare](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Logikappen har nu konfigurerats med en utlösare som startar en körning av andra utlösare och åtgärder i arbetsflödet. Detta sker varje gång ett nytt objekt skapas i SharePoint Online-lista du valde.  

