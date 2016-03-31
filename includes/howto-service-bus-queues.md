## Was sind Service Bus-Warteschlangen?

Service Bus-Warteschlangen unterstützen eine **brokermessaging** Kommunikationsmodell. Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über eine Warteschlange aus, die als Zwischenstufe (Broker) fungiert. Ein Nachrichtenproducer (Absender) übergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort. Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der Warteschlange ab und verarbeitet sie. Der Producer muss nicht auf eine Antwort vom Consumer warten, um seine Funktion fortzusetzen und weitere Nachrichten zu schicken. Warteschlangen bieten **First In, First Out (FIFO)** -Verfahren an einen oder mehrere Consumer. Die Nachrichten werden also normalerweise in der gleichen Reihenfolge von den Consumern empfangen und verarbeitet, wie sie in die Warteschlange übergeben wurden, und jede Nachricht wird nur von einem Consumer verarbeitet.

![Konzepte für Warteschlangen](./media/howto-service-bus-queues/sb-queues-08.png)

Service Bus-Warteschlangen sind eine Allzwecktechnologie für viele unterschiedliche Szenarien:

-   Kommunikation zwischen Web- und Workerrollen in Azure-Anwendungen mit mehreren Ebenen
-   Kommunikation zwischen lokalen Apps und von Azure gehosteten Apps in einer Hybridlösung
-   Kommunikation zwischen Komponenten einer verteilten lokalen Anwendung, die in verschiedenen Organisationen oder Abteilungen einer Organisation laufen

Warteschlangen unterstützen Sie bei der einfacheren Skalierung Ihrer Anwendungen und führen zu einer robusteren Architektur.

## Erstellen eines Dienstnamespaces

Um mit der Verwendung von Service Bus-Warteschlangen in Azure beginnen zu können, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Namespace ist ein Bereichscontainer für die Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Dienstnamespace:

1.  Melden Sie sich auf die [Azure-Portal][].

2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf **Service Bus**.

3.  Klicken Sie im unteren Bereich des Portals auf **Erstellen**.
    ![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  In der **einen neuen Namespace hinzufügen** Dialogfeld Geben Sie einen Namespace ein. Das System überprüft sofort, ob dieser Name verfügbar ist.   
    ![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist, das Land oder die Region, wo dieser Namespace gehostet werden soll. (Stellen Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der sie Ihre Rechnerressourcen einsetzen.)

     > [AZURE.IMPORTANT] Wählen Sie die **derselben Region** wählen Sie für Ihre Anwendung bereitstellen möchten. Dies sorgt für die beste Leistung.

6.  Lassen Sie die anderen Felder im Dialogfeld die Standardwerte (**Messaging** und **Standardebene**), klicken Sie dann auf OK-Häkchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Eventuell müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto bereitgestellt werden.

    ![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

Der neue Namespace wird innerhalb kurzer Zeit aktiviert und anschließend im Azure-Portal angezeigt. Warten Sie, bis der namespacestatus ist **Active** bevor Sie fortfahren.

## Abrufen der Standard-Anmeldeinformationen für den Namespace

Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen. Diese Anmeldeinformation erhalten Sie im Azure-Portal.

###So rufen Sie die Anmeldeinformationen im Verwaltungsportal ab

1.  Klicken Sie im linken Navigationsbereich auf die **Service Bus** Knoten, um die Liste der verfügbaren Namespaces anzuzeigen:   
    ![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Wählen Sie in der angezeigten Liste den Namespace, den Sie gerade erstellt haben:   
    ![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Klicken Sie auf **Verbindungsinformationen**.   
    ![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  In den **Zugriff auf die Verbindungsinformationen** Bereich finden Sie die Verbindungszeichenfolge, die den SAS-Schlüssel und den Schlüsselnamen enthält.   

    ![](./media/howto-service-bus-queues/multi-web-45.png)
    
5.  Notieren Sie den Schlüssel oder kopieren Sie ihn in die Zwischenablage.

  [Azure portal]: http://manage.windowsazure.com



