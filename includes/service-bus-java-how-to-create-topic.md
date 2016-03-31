## Was sind Service Bus-Themen und -Abonnements?

Service Bus-Themen und-Abonnements unterstützen ein *Veröffentlichen/Abonnieren*
Messaging Kommunikationsmodell. Bei der Verwendung von Themen und Abonnements
Komponenten einer verteilten Anwendung kommunizieren nicht direkt mit
andere; sondern tauschen Nachrichten über ein Thema, fungiert als ein
Vermittler.

![TopicConcepts](./media/service-bus-java-how-to-create-topic/sb-topics-01.png)

Im Gegensatz zu Service Bus-Warteschlangen, in dem jede Nachricht, indem verarbeitet wird ein
einzelnen Consumer, Themen und Abonnements bieten eine "eins zu viele"
Kommunikation, die mithilfe eines Veröffentlichen-Abonnieren-Musters. Es ist möglich
Registrieren Sie mehrere Abonnements zu einem Thema. Wenn eine Nachricht gesendet wird, um eine
Thema, es dann zur Verfügung gestellt wird jedes Abonnement verarbeitet /
unabhängig.

Ein Abonnement für ein Thema ähnelt eine virtuelle Warteschlange, Kopien
die Nachrichten, die an das Thema gesendet wurden. Sie können optional auch registrieren.
für ein Thema auf einer Basis pro Abonnement können Sie Filterregeln
Filtern/einschränken, welche Nachrichten an ein Thema vom Thema empfangen werden
-Abonnements verwenden.

Service Bus-Themen und Abonnements können Sie den Prozess ein
sehr große Anzahl von Nachrichten über eine sehr große Anzahl von Benutzern und
Clientanwendungen.

## Erstellen eines Dienstnamespaces

So verwenden Sie Service Bus-Themen und-Abonnements in Azure,
Sie müssen zuerst einen Dienstnamespace erstellen. Ein Dienstnamespace ist
eine Bereichseinheit zur Adressierung von Service Bus-Ressourcen innerhalb Ihrer
der Anwendung in der Stagingumgebung befindet.

So erstellen Sie einen Dienstnamespace:

1.  Melden Sie sich beim [klassischen Azure-Portal][] an.

2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf
    **Service Bus**.

3.  Klicken Sie im unteren Bereich des Portals auf **Erstellen**.
    ![][0]

4.  In der **einen neuen Namespace hinzufügen** Dialogfeld Geben Sie einen Namespace ein.
    Das System überprüft sofort, ob dieser Name verfügbar ist.
    ![][2]

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist, das
    Land oder die Region, wo dieser Namespace gehostet werden soll. (Stellen
    Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der sie Ihre Rechnerressourcen
    einsetzen).

    Wichtig: Wählen Sie die **derselben Region** die Sie einsetzen möchten
    Anwendung einsetzen möchten. Dies sorgt für die beste Leistung.

6.  Lassen Sie die anderen Felder im Dialogfeld die Standardwerte (**Messaging** und **Standardebene**), klicken Sie dann auf das Häkchen. Ihr Dienstnamespace wird nun erstellt
    und aktiviert. Möglicherweise müssen Sie einige Minuten warten,
    bis die Ressourcen für Ihr Konto bereitgestellt werden.

    ![][6]


## Abrufen der Standard-Anmeldeinformationen für den Namespace

Verwaltungsvorgänge, z. B. das Erstellen eines Themas oder
Abonnement für den neuen Namespace, müssen Sie die Verwaltung beziehen.
Anmeldeinformationen für den Namespace. Diese Anmeldeinformation erhalten Sie im Portal.

### So rufen Sie die Anmeldeinformationen im Verwaltungsportal ab

1.  Klicken Sie im linken Navigationsbereich auf die **Service Bus** Knoten
    die Liste der verfügbaren Namespaces anzuzeigen:
    ![][0]

2.  Klicken Sie auf den Namespace, den Sie gerade erstellt, in der angezeigten Liste haben:
    ![][3]

3.  Klicken Sie auf **konfigurieren** auf die freigegebenen Zugriffsrichtlinien für Ihren Namespace anzuzeigen.
    ![](./media/service-bus-java-how-to-create-topic/sb-queues-14.png)

4.  Notieren Sie den Primärschlüssel oder kopieren Sie ihn in die Zwischenablage.


  [Azure portal]: http://manage.windowsazure.com
  [0]: ./media/service-bus-java-how-to-create-topic/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-create-topic/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-create-topic/sb-queues-09.png
  [4]: ./media/service-bus-java-how-to-create-topic/sb-queues-06.png

  [6]: ./media/service-bus-java-how-to-create-topic/getting-started-multi-tier-27.png
  [34]: ./media/service-bus-java-how-to-create-topic/VSProperties.png


