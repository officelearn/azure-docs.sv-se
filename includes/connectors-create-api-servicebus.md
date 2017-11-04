### <a name="prerequisites"></a>Krav
Du måste ha en [Service Bus](https://azure.microsoft.com/services/service-bus/) konto.  

Innan du kan använda Azure Service Bus-konto i en logikapp, måste du godkänna logik för att ansluta till ditt service bus-konto. Lyckligtvis kan du göra detta enkelt från i din logikapp på Azure-portalen.  

Här följer stegen för att verifiera din logikapp för att ansluta till ditt Service Bus-konto:  

1. Om du vill skapa en anslutning till Service Bus i logik app designer **visa Microsoft hanterade API: er** i den nedrullningsbara listan. Ange **service bus** i sökrutan. Välj utlösare eller åtgärden som du vill använda.  
    ![Bild 1 Service Bus](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Om du inte har skapat alla anslutningar till Service Bus innan du kan uppmanas att ange autentiseringsuppgifter för Service Bus. Dessa autentiseringsuppgifter används för att verifiera din logikapp för att ansluta till och komma åt data i ditt Service Bus-konto. Service Bus-anslutningen måste anslutningssträngen för Service Bus-namnrymd. Det kräver också **hantera** behörigheter. Ett bra sätt att veta om anslutningssträngen är för namnområdet eller en specifik entitet om den innehåller den `EntityPath` parameter. Om den finns, men det är inte rätt anslutningssträngen för en logikapp.  
    ![Service Bus-anslutningssträng](./media/connectors-create-api-servicebus/connectionstring.png)
3. När du har fått anslutningssträngen för namnområdet, kan du använda den för API-anslutningen i Logic Apps.  
    ![Bild 2 Service Bus](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. Observera att anslutningen har skapats och du kan nu välja att fortsätta med andra steg i din logikapp.  
    ![Bild 3 Service Bus](./media/connectors-create-api-servicebus/servicebus-3.png)   

