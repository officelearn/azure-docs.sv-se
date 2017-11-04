I det här exemplet jag visar hur du använder den **SharePoint Online - när ett nytt objekt skapas** trigger för att initiera en logik app arbetsflödet när ett nytt objekt skapas i en SharePoint Online lista.

> [!NOTE]
> Hämta uppmanas du att logga in på ditt konto för SharePoint om du inte redan har skapat en *anslutning* till SharePoint Online.  
> 
> 

1. Ange *sharepoint* i sökrutan på logic apps designer väljer den **SharePoint Online - när ett nytt objekt skapas** utlösare.  
   ![SharePoint online utlösande bilden](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. Den **när ett nytt objekt skapas** kontrollen visas.  
   ![Bild 2 till SharePoint online-utlösare](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Välj en **webbplatsens URL**. Det här är den online SharePoint-webbplats som du vill övervaka för nya objekt som utlöser arbetsflödet.  
   ![Bild 3 till SharePoint online-utlösare](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Välj en **namn**. Detta är en lista på webbplatsen SharePoint Online som du vill övervaka för nya objekt som utlöser arbetsflödet.  
   ![Bild 4 till SharePoint online-utlösare](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Din logikapp har nu konfigurerats med en utlösare som startar andra utlösare och åtgärder i arbetsflödet körs. Detta sker varje gång en ny artikel skapas i SharePoint Online listan som du har valt.  

