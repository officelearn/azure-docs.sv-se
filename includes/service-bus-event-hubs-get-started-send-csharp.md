## Senden von Nachrichten an Ereignis-Hubs

In diesem Abschnitt schreiben wir eine Windows-Konsolenanwendung, die Ereignisse an den Ereignis-Hub sendet.

1. Erstellen Sie in Visual Studio ein neues Visual C#-Desktop-App-Projekt, mit dem **Konsolenanwendungsprojekte** -Projektvorlage. Nennen Sie das Projekt **Absender**.

    ![][7]

2. Im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**. 

    Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

3. Suchen Sie nach `Microsoft Azure Service Bus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Vereinbarung. 

    ![][8]

    Daraufhin wird das NuGet-Paket <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">Azure Service Bus</a> mit allen Abhängigkeiten heruntergeladen und installiert und dem Projekt ein Verweis auf das Paket hinzugefügt.

4. Fügen Sie die folgenden `using` Anweisung am Anfang der **Program.cs** Datei:

        using System.Threading;
        using Microsoft.ServiceBus.Messaging;

5. Fügen Sie die folgenden Felder mit den **Programm** -Klasse, und Ersetzen Sie dabei die Platzhalterwerte durch den Namen der im vorherigen Abschnitt erstellten Ereignis-Hub und die Verbindungszeichenfolge mit **Senden** Rechte:

        static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. Fügen Sie die folgende Methode, die die **Programm** Klasse:

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

    Diese Methode sendet kontinuierlich Ereignisse mit einer Verzögerung von 200 ms an den Ereignis-Hub.

7. Schließlich fügen Sie folgende Zeilen, die **Main** Methode:

        Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png

