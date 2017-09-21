

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **Nytt** > **Webb och mobilt** > **Notification Hub**.
   
      ![Azure Portal – skapa en Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. I rutan **Notification Hub** skriver du ett unikt namn. Välj din **region**, **prenumeration** och **resursgrupp** (om du redan har en). 
   
    Om du redan har ett namnområde för Service Bus som du vill skapa hubben i gör du följande:

    a. I området **Namnområde** väljer du länken **Välj befintligt**. 
   
    b. Välj **Skapa**.

    Om du inte redan har ett namnområde för Service Bus kan du använda standardnamnet som skapas baserat på hubbnamnet (om namnområdesnamnet är tillgängligt).
   
      ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

    När du har skapat namnområdet och en meddelandehubb öppnas Azure Portal. 
   
      ![Azure Portal – portalsida för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)

4. Välj **Inställningar** > **Åtkomstprinciper**. Anteckna de två anslutningssträngarna som är tillgängliga för dig. Du behöver dem för att hantera push-meddelanden senare.
   
      ![Azure Portal – anslutningssträngar för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

