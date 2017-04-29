

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **+NY** högst upp till vänster på skärmen.
2. Klicka på **Ny**, sedan **Webb + mobilt**. Bläddra ner vid behov och klicka på **Notification Hub**.
   
      ![Azure Portal – skapa Notification Hubs](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Se till att du anger ett unikt namn i **Notification Hub**-fältet. Välj din önskade **region**, **prenumeration** och **resursgrupp** (om du redan har en). 
   
    Om du redan har ett Service Bus-namnområde som du vill skapa hubben i, väljer du det via alternativet **Välj befintligt** i fältet **Namnområde**.  Annars kan du använda standardnamnet som kommer att skapas baserat på hubbnamnet, förutsatt att namnet på namnområdet är tillgängligt. 
   
    När du är klar klickar du på **Skapa**.
   
      ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. När namnområdet och meddelandehubben har skapats, kommer du att tas till respektive portalsida. 
   
      ![Azure Portal – portalsida för Meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)
5. Klicka på **Inställningar** och sedan på **Åtkomstprinciper** och anteckna de två anslutningssträngarna som finns tillgängliga för dig. Du kommer att behöva dem för att hantera push-meddelanden senare.
   
      ![Azure Portal – anslutningssträngar för Meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

