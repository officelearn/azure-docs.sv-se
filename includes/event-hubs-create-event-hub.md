## Skapa en händelsehubb
1. Logga in på [Azure Portal][Azure Portal] och klicka på **Ny** högst upp till vänster på skärmen.
2. Klicka på **Data + Analytics** och därefter på **Händelsehubbar**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)
3. I bladet **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)
4. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic eller Standard). Välj även en Azure-prenumeration, resursgrupp och plats där du vill skapa resursen. 
5. Klicka på **Skapa** för att skapa namnområdet.
6. I listan över händelsehubbarnas namnområden klickar du på det nyligen skapade namnområdet.      
   
    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)
7. Klicka på namnområdesbladet och på **Händelsehubbar**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)
8. Klicka på **Lägg till händelsehubb** överst på bladet.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)
9. Skriv ett namn för din händelsehubb och klicka sedan på **Skapa**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)
10. I listan över händelsehubbar klickar du på den nyligen skapade Händelsehubben. 
    
     ![](./media/event-hubs-create-event-hub/create-event-hub6.png)
11. Tillbaka i namnområdesbladet (inte i det specifika händelsehubbladet) klickar du på **Principer för delad åtkomst** och klickar därefter på **RootManageSharedAccessKey**.
    
     ![](./media/event-hubs-create-event-hub/create-event-hub7.png)
12. Klicka på kopieringsknappen för att kopiera anslutningssträngen **RootManageSharedAccessKey** till Urklipp. Spara den här anslutningssträngen för användning senare guiden.
    
     ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

Din händelsehubb har nu skapats och du har de anslutningssträngar som du behöver för att skicka och ta emot händelser.

[Azure Portal]: https://portal.azure.com/

<!--HONumber=Sep16_HO3-->


