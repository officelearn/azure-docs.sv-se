<a id="what-are-service-bus-queues"></a>
## Was sind Service Bus-Warteschlangen?

Service Bus-Warteschlangen unterstützen eine **brokermessaging** Kommunikation
Modell. Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht
direkt miteinander kommunizieren; Tauschen sie stattdessen Nachrichten über
eine Warteschlange, die als Zwischenstufe (Broker) fungiert. Ein Nachrichtenproducer (Absender)
übergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort.
Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der
die Warteschlange und verarbeitet es. Der Producer muss nicht auf eine Antwort vom
vom Consumer, um seine Funktion fortzusetzen und weitere
Nachrichten nicht zulassen können. Warteschlangen bieten **First In, First Out (FIFO)** Übermittlung
an einen oder mehrere Consumer. Die Nachrichten werden also normalerweise
von den Empfängern in der Reihenfolge, in der sie wurden, empfangen und verarbeitet
zur Warteschlange hinzugefügt, und jede Nachricht wird vom empfangen und verarbeitet nur
von einem Consumer.

![Konzepte für Warteschlangen](./media/service-bus-java-how-to-create-queue/sb-queues-08.png)

Service Bus-Warteschlangen sind eine Allzwecktechnologie
eine Vielzahl von Szenarien:

-   Kommunikation zwischen Web- und Workerrollen in Azure-Anwendungen
    Azure-Anwendung.
-   Kommunikation zwischen lokalen apps und Azure gehosteten apps
    in einer hybridlösung.
-   Kommunikation zwischen Komponenten einer verteilten Anwendung
    lokal ausgeführt wird, in verschiedenen Organisationen oder Abteilungen einer
    die Organisation.

Verwenden von Warteschlangen können Sie leichter, Ihre Anwendung skalieren und
zu einer robusteren Architektur.

## Erstellen eines Dienstnamespaces

Um mit der Verwendung von Service Bus-Features in Azure zu beginnen, müssen Sie zuerst
einen Dienstnamespace erstellen. Ein Namespace ist eine Bereichseinheit
zur Adressierung von Servicebus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Dienstnamespace:

1.  Melden Sie sich auf die [klassischen Azure-Portal][].

2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf
    **Service Bus**.

3.  Klicken Sie im unteren Bereich des Portals auf **Erstellen**.
    ![](./media/service-bus-java-how-to-create-queue/sb-queues-03.png)

4.  In der **einen neuen Namespace hinzufügen** Dialogfeld Geben Sie einen Namespace ein.
    Das System überprüft sofort, ob dieser Name verfügbar ist.
    ![](./media/service-bus-java-how-to-create-queue/sb-queues-04.png)

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist, das
    Land oder die Region, wo dieser Namespace gehostet werden soll. (Stellen
    Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der sie Ihre Rechnerressourcen
    einsetzen).

    Wichtig: Wählen Sie die **derselben Region** die Sie einsetzen möchten
    Anwendung einsetzen möchten. Dies sorgt für die beste Leistung.

6.  Lassen Sie die anderen Felder im Dialogfeld die Standardwerte (**Messaging** und **Standardebene**), klicken Sie dann auf das Häkchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Eventuell müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto bereitgestellt werden.

    ![](./media/service-bus-java-how-to-create-queue/getting-started-multi-tier-27.png)

Der neue Namespace wird innerhalb kurzer Zeit aktiviert und anschließend im Azure-Portal angezeigt. Warten Sie, bis der namespacestatus ist **Active** bevor Sie fortfahren.

## Abrufen der Standard-Anmeldeinformationen für den Namespace

Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im
neuen Namespace, müssen Sie die Anmeldeinformationen für den
Namespace abrufen. Diese Anmeldeinformation erhalten Sie im Portal.

###So rufen Sie die Anmeldeinformationen im Verwaltungsportal ab

1.  Klicken Sie im linken Navigationsbereich auf die **Service Bus** Knoten an
    die Liste der verfügbaren Namespaces anzuzeigen:
    ![](./media/service-bus-java-how-to-create-queue/sb-queues-13.png)

2.  Klicken Sie in der angezeigten Liste auf den Namespace, den Sie gerade erstellt haben.

3.  Klicken Sie auf **konfigurieren** auf die freigegebenen Zugriffsrichtlinien für Ihren Namespace anzuzeigen.
    ![](./media/service-bus-java-how-to-create-queue/sb-queues-14.png)

4.  Notieren Sie den Primärschlüssel oder kopieren Sie ihn in die Zwischenablage.

  [Azure classic portal]: http://manage.windowsazure.com

  [34]: ./media/service-bus-java-how-to-create-queue/VSProperties.png


