## Senden von Nachrichten an Ereignis-Hubs

In diesem Abschnitt schreiben wir eine Java-Konsolenanwendung, um Ereignisse an den Ereignis-Hub zu senden. Wir nutzen den JMS AMQP-Anbieter aus der [Apache Qpid-Projekt](http://qpid.apache.org/). Dies ist analog zur Verwendung von Servicebuswarteschlangen und-Themen mit AMQP über Java, siehe [hier](../service-bus/service-bus-java-how-to-use-jms-api-amqp.md). Weitere Informationen finden Sie unter der [Qpid JMS-Dokumentation](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) und [Java Messaging Service](http://www.oracle.com/technetwork/java/jms/index.html).

1. Installieren Sie in Eclipse die [Azure Toolkit für Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx). Diese beinhaltet die Qpid JMS AMQP-Clientbibliotheken.

2. Erstellen Sie in Eclipse ein neues Java-Projekt namens **Absender**.

3. Eclipse-Paket-Explorer mit der rechten Maustaste die **Absender** Projekt, und wählen **Eigenschaften**. Klicken Sie im linken Bereich des Dialogfelds auf **Java-Buildpfad**, klicken Sie dann auf die **Bibliotheken** Registerkarte und dann die **Add Library** Schaltfläche. Wählen Sie **Package for Apache Qpid Client Libraries for JMS (by MS Open Tech)**, klicken Sie auf **Weiter**, und klicken Sie dann auf **Fertig stellen**.

    ![][8]

4. Erstellen Sie eine Datei mit dem Namen **servicebus.properties** im Stammverzeichnis der **Absender** -Projekt mit dem folgenden Inhalt. Vergessen Sie nicht das Ersetzen der Werte für:
    - den Ereignis-Hub-Namen.
    - Namespacenamen (letzterer lautet in der Regel `{event hub name}-ns`).
    - URL-codierte **SendRule** -Schlüssel (Sie haben Sie sich diesen Schlüssel bei der Erstellung des Ereignis-Hubs). URL-Codierung können sie [hier](http://www.w3schools.com/tags/ref_urlencode.asp).

            # servicebus.properties - sample JNDI configuration

            # Register a ConnectionFactory in JNDI using the form:
            # connectionfactory.[jndi_name] = [ConnectionURL]
            connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

            # Register some queues in JNDI using the form
            # queue.[jndi_name] = [physical_name]
            # topic.[jndi_name] = [physical_name]
            queue.EventHub = {event hub name}

5. Erstellen Sie eine neue Klasse namens **Absender**. Fügen Sie die folgenden `import`-Anweisungen ein:

        import java.io.BufferedReader;
        import java.io.IOException;
        import java.io.InputStreamReader;
        import java.io.UnsupportedEncodingException;
        import java.util.Hashtable;

        import javax.jms.BytesMessage;
        import javax.jms.Connection;
        import javax.jms.ConnectionFactory;
        import javax.jms.Destination;
        import javax.jms.JMSException;
        import javax.jms.MessageProducer;
        import javax.jms.Session;
        import javax.naming.Context;
        import javax.naming.InitialContext;
        import javax.naming.NamingException;

6. Fügen Sie dann den folgenden Code hinzu:

        public static void main(String[] args) throws NamingException,
                JMSException, IOException, InterruptedException {
            // Configure JNDI environment
            Hashtable<String, String> env = new Hashtable<String, String>();
            env.put(Context.INITIAL_CONTEXT_FACTORY,
                    "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
            env.put(Context.PROVIDER_URL, "servicebus.properties");
            Context context = new InitialContext(env);

            ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

            Destination queue = (Destination) context.lookup("EventHub");

            // Create Connection
            Connection connection = cf.createConnection();

            // Create sender-side Session and MessageProducer
            Session sendSession = connection.createSession(false,
                    Session.AUTO_ACKNOWLEDGE);
            MessageProducer sender = sendSession.createProducer(queue);

            System.out.println("Press Ctrl-C to stop the sender process");
            System.out.println("Press Enter to start now");
            BufferedReader commandLine = new java.io.BufferedReader(
                    new InputStreamReader(System.in));
            commandLine.readLine();

            while (true) {
                sendBytesMessage(sendSession, sender);
                Thread.sleep(200);
            }
        }

        private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
            BytesMessage message = sendSession.createBytesMessage();
            message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
            sender.send(message);
            System.out.println("Sent message");
        }



<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png


