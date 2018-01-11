

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **Nytt** > **Webb och mobilt** > **Notification Hub**.
   
      ![Azure Portal – skapa en Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. I rutan **Notification Hub** skriver du ett unikt namn. Välj din **region**, **prenumeration** och **resursgrupp** (om du redan har en). 
   
      Om du inte redan har ett namnområde för Service Bus kan du använda standardnamnet som skapas baserat på hubbnamnet (om namnområdesnamnet är tillgängligt).
    
      Om du redan har ett namnområde för Service Bus som du vill skapa hubben i följer du de här stegen

    a. I området **Namnområde** väljer du länken **Välj befintligt**. 
   
    b. Välj **Skapa**.
   
      ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. När du har skapat namnområdet och meddelandehubben öppnar du den genom att välja **Alla resurser** och sedan välja meddelandehubben som skapats i listan. 
   
      ![Azure Portal – portalsida för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-resources.png)

5. Välj **Åtkomstprinciper** i listan. Anteckna de två anslutningssträngarna som är tillgängliga för dig. Du behöver dem för att hantera push-meddelanden senare.

      >[!IMPORTANT]
      >Använd **INTE** DefaultFullSharedAccessSignature i ditt program. Detta är endast avsett att användas i din serverdel.
      >
   
      ![Azure Portal – anslutningssträngar för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

