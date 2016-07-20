## Skapa en händelsehubb

1. Logga in på den [klassiska Azure-portalen][] och klicka på **NY** längst ner på skärmen.

2. Klicka på **Apptjänster**, sedan **Service Bus**, sedan **Händelsehubb** och sedan **Snabbregistrering**.

    ![][1]

3. Ange ett namn för din händelsehubb, välj önskad region och klicka sedan på **Skapa en ny händelsehubb**.

    ![][2]

4. Om du inte uttryckligen väljer ett befintligt namnområde i en viss region, skapar portalen ett namnområde åt dig (vanligtvis ***namnet på händelsehubben*-ns**). Klicka på namnområdet (**eventhub-ns** i det här exemplet).

    ![][3]

5. Längst ner på sidan klickar du på **Anslutningsinformation**. Klicka på kopieringsknappen (visas i följande bild) för att kopiera anslutningssträngen **RootManageSharedAccessKey** till Urklipp. Spara den här anslutningssträngen för användning senare guiden.

    ![][4]

Din händelsehubb har nu skapats och du har de anslutningssträngar som du behöver för att skicka och ta emot händelser.

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png


<!--HONumber=Jun16_HO2-->


