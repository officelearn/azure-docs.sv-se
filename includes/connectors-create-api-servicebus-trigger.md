Så här använder du den **Service Bus - när ett meddelande tas emot i en kö** trigger för att initiera en logik app arbetsflödet när ett nytt objekt skickas till en Service Bus-kö.  

> [!NOTE]
> Du uppmanas att logga in med ditt Service Bus-anslutningssträng om du inte redan har skapat en anslutning till Service Bus.  
> 
> 

1. Skriv i sökrutan på logic apps designer **service bus**. Välj sedan den **Service Bus - när ett meddelande tas emot i en kö** utlösare.  
   ![Service Bus utlösarbild 1](./media/connectors-create-api-servicebus/trigger-1.png)   
2. Den **när ett meddelande i en kö** dialogrutan visas.  
   ![Bild 2 till Service Bus-utlösare](./media/connectors-create-api-servicebus/trigger-2.png)   
3. Ange namnet på Service Bus-kö som du vill att utlösaren ska övervakas.   
   ![Bild 3 till Service Bus-utlösare](./media/connectors-create-api-servicebus/trigger-3.png)   

Din logikapp har nu konfigurerats med en utlösare. När du har fått ett nytt objekt i kön som du har valt börjar utlösaren en körning av andra utlösare och åtgärder i arbetsflödet.    

