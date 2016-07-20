## Skicka meddelanden till Event Hubs

I det här avsnittet skriver du en Windows-konsolapp som skickar händelser till din Event Hub.

1. I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Avsändare**.

    ![][7]

2. Högerklicka på lösningen i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**. 

3. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus`. Kontrollera att projektnamnet (**Avsändare**) har angetts i rutan **Versioner**. Klicka på **Installera** och godkänn användningsvillkoren. 

    ![][8]

    <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">Azure Service Bus-bibliotekets NuGet-paket</a> hämtas och installeras, och en referens till det läggs till.

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Lägg till följande fält till **Program**-klassen, där du ersätter platshållarvärdena med namnet på den Event Hub du skapat i föregående avsnitt och anslutningssträngen på namnområdesnivå som du sparat tidigare.

    ```
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```

6. Lägg till följande metod till **Program**-klassen:

    ```
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }

            Thread.Sleep(200);
        }
    }
    ```

    Den här metoden skickar kontinuerligt händelser till din Event Hub med en fördröjning på 200 ms.

7. Slutligen lägger du till följande rader till **Main**-metoden:

    ```
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png


<!--HONumber=Jun16_HO2-->


