# Verwenden von CDN für Azure

Das Azure Content Delivery Network (CDN) bietet Entwicklern eine
globale Lösung für die Übermittlung breitbandiger Inhalte. Dazu werden Blobs
und statische Inhalte von Serverinstanzen an physischen Knotenpunkten in den USA,
Europa, Asien, Australien und Südamerika zwischengespeichert. Eine aktuelle Liste von
CDN-knotenstandorten finden Sie unter [Azure CDN Node Locations].

Diese Aufgabe umfasst die folgenden Schritte:

* [Schritt 1: Erstellen eines Speicherkontos](#Step1)
* [Schritt 2: Erstellen eines neuen CDN-Endpunkts für das Speicherkonto](#Step2)
* [Schritt 3: Auf die CDN-Inhalte zugreifen](#Step3)
* [Schritt 4: Entfernen von CDN-Inhalten](#Step4)

Die Verwendung von CDN zum Zwischenspeichern von Azure-Daten bietet folgende Vorteile:

-   Bessere Leistung und Benutzerfreundlichkeit für Endbenutzer, die von einer Inhaltsquelle weit entfernt sind und Anwendungen verwenden, die viele Verbindungen im Internet benötigen, um Inhalte zu laden.
-   Große verteilte Skalierung, um hohe Lasten etwa zu Beginn eines Ereignisses, z. B. bei einer Produkteinführung, besser verarbeiten zu können

Aktuelle CDN-Kunden können jetzt das Azure CDN in der [Azure classic portal]. Das CDN ist eine Zusatzfunktion Ihres Abonnements und hat einen eigenen [billing plan].

<a id="Step1"> </a>
<h2>Schritt 1: Erstellen eines Speicherkontos</h2>

Gehen Sie folgendermaßen vor, um für ein Azure-Abonnement ein neues
Azure-Abonnement. Ein Speicherkonto ermöglicht den Zugriff auf 
Azure-Speicherdienste. Das Speicherkonto stellt die höchste Ebene
des Namespace für den Zugriff auf die verschiedenen Komponenten der Speicherdienste von Azure
Komponenten: Blob-Dienste, Warteschlangendienste und tabellenspeicherdienste. Weitere
Informationen zu den Azure-Speicherdienste finden Sie unter [mithilfe der
Azure-Speicherdienste](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Um ein Speicherkonto zu erstellen, müssen Sie entweder der
Dienstadministrator oder ein Co-Administrator für das zugehörige Abonnement sein.

> [AZURE.NOTE] Informationen zum Ausführen dieses Vorgangs mit der
Azure Servicemanagement-API finden Sie unter der [Speicherkonto erstellen](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) Referenzthema.

**So erstellen Sie ein Speicherkonto für ein Azure-Abonnement**

1.  Melden Sie sich bei der [Azure classic portal].
2.  Klicken Sie in der unteren linken Ecke auf **Neu**. In der **Neu** Wählen Sie im Dialogfeld **Data Services**, klicken Sie dann auf **Speicher**, dann **Schnellerfassung**.

    Die **Speicherkonto erstellen** Dialogfeld wird angezeigt.

    ![Speicherkonto erstellen][create-new-storage-account]

4. In der **URL** Geben einen Unterdomänennamen ein. Dieser Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten.

    Dieser Wert wird der Hostname in dem URI, der
    zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird. To
   
    Adressieren einer Containerressource im Blob-Dienst verwenden Sie einen
    URI im folgenden Format, wobei *& Lt; Speicherkontobezeichnung & Gt;* verweist
 mit dem Wert eingegebenen **Geben Sie eine URL**:

    http://*& Lt; Speicherkontobezeichnung & Gt;*.blob.core.windows.net/*& Lt; Mycontainer & Gt;*

    **Wichtig:** die URL-Bezeichnung bildet die Unterdomäne des Speichers
    Speicherkonto-URI und muss unter allen gehosteten Diensten in 
    Azure.

    Dieser Wert wird auch als Name für dieses Speicherkonto im Portal verwendet oder beim programmatischen Zugriff auf dieses Konto.

5.  Aus der **Region/Affinitätsgruppe** Dropdown-Liste Wählen Sie eine Region oder Affinitätsgruppe für das Speicherkonto zu gruppieren. Wählen Sie eine Affinitätsgruppe statt einer Region aus, wenn sich Ihre Speicherdienste im selben Rechenzentrum befinden sollen wie Ihre anderen Azure-Dienste. Dadurch wird möglicherweise die Leistung verbessert, und es fallen keine Kosten für Ausgangsverkehr an.  

    **Hinweis:** um eine Affinitätsgruppe zu erstellen, öffnen Sie die **Einstellungen** Bereich des Verwaltungsportals, klicken Sie auf **Affinitätsgruppen**, und klicken Sie dann auf **Affinitätsgruppe hinzufügen** oder **Hinzufügen**. Sie können Affinitätsgruppen auch mithilfe der Azure Service-Verwaltungs-API erstellen und verwalten. Weitere Informationen finden Sie unter [Vorgänge für Affinitätsgruppen].

6. Aus der **Abonnement** Dropdown-Liste, wählen Sie das Abonnement, die mit das Speicherkonto verwendet werden.
7.  Klicken Sie auf **Speicherkonto erstellen**. Das Erstellen des Speicherkontos kann einige Minuten dauern.
8.  Um sicherzustellen, dass das Speicherkonto erfolgreich erstellt wurde, stellen Sie sicher, dass das Konto in der aufgeführten Elementen wird **Speicher** mit dem Status **Online**.

<a id="Step2"> </a>
<h2>Schritt 2: Erstellen eines neuen CDN-Endpunkts für das Speicherkonto</h2>

Nachdem Sie den CDN-Zugriff auf ein Speicherkonto oder einen gehosteten Dienst aktivieren, kommen alle
öffentlich verfügbaren Objekte für den CDN-Edgezwischenspeicher infrage. Wenn Sie
ein Objekt ändern, das derzeit im CDN zwischengespeichert ist, stehen die neuen Inhalte
erst dann über das CDN zur Verfügung, wenn das CDN seine Inhalte aktualisiert,
wenn die Lebensdauer der zwischengespeicherten Inhalte abläuft.

**So erstellen Sie einen neuen CDN-Endpunkt für das Speicherkonto**

1. In der [Azure classic portal], klicken Sie im Navigationsbereich auf **CDN**.

2. Klicken Sie auf der Multifunktionsleiste auf **Neu**. In der **Neu** Dialogfeld **Anwendungsdienste**, dann **CDN**, dann **Schnellerfassung**.

3. In der **Ursprungsdomäne** Dropdown-Liste Wählen Sie den Speicher im vorherigen Abschnitt aus der Liste der verfügbaren Speicherkonten erstellten Speicherkontos. 

4. Klicken Sie auf die **Erstellen** um den neuen Endpunkt zu erstellen.

5. Sobald der Endpunkt erstellt wurde, wird er in einer Liste von Endpunkten für das Abonnement angezeigt. In der Listenansicht werden der URL für den Zugriff auf zwischengespeicherte Inhalte sowie die Ursprungsdomäne angezeigt. 

    Die Ursprungsdomäne ist der Standort, von dem das CDN Inhalte
    zwischenspeichert. Bei der Ursprungsdomäne kann es sich um ein Speicherkonto oder einen Clouddienst handeln. In diesem Beispiel wird ein Speicherkonto verwendet. Speicherinhalte werden entsprechend einer von Ihnen festgelegten Cache-Steuereinstellung oder entsprechend der Standardheuristik des Zwischenspeicherungsnetzwerks auf Edgeservern zwischengespeichert. 


    > [AZURE.NOTE] Die für den Endpunkt erstellte Konfiguration steht nicht
    sofort zur Verfügung. Es kann bis zu 60 Minuten dauern, bis
    die Registrierung über das CDN-Netzwerk verteilt wurde. Benutzer, die versuchen,
    den CDN-Domänennamen sofort zu verwenden, erhalten u. U. den Statuscode 400
    (ungültige Anforderung), bis die Inhalte über das CDN verfügbar sind.

<a id="Step3"> </a>
<h2>Schritt 3: Zugriff auf CDN-Inhalte</h2> 

Verwenden Sie für den Zugriff auf die im CDN zwischengespeicherten Inhalte den im Portal bereitgestellten CDN-URL. Die Adresse eines zwischengespeicherten Blobs hat etwa folgendes Format:

http://&lt*CDNNamespace*\ >.vo.msecnd.net/ <*Meinoeffentlichercontainer*\ > / <*BlobName*\ >

<a id="Step4"> </a>
<h2>Schritt 4: Entfernen von Inhalten aus CDN</h2>

Wenn Sie ein Objekt nicht länger im Azure Content
Delivery Network (CDN) zwischenspeichern möchten, können Sie einen der folgenden Schritte ausführen:

-   Ein Azure-Blob können Sie aus dem öffentlichen Container
    löschen.
-   Sie können den öffentlichen Container zu einem privaten machen. Finden Sie unter [Beschränken des Zugriffs auf Container und Blobs](http://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) Weitere Informationen.
-   Sie können den CDN-Endpunkt mit dem Verwaltungsportal deaktivieren oder
    löschen.
-   Sie können den gehosteten Dienst so ändern, dass er nicht mehr auf Anforderungen des
    Objekts antwortet.

Ein bereits im CDN zwischengespeichertes Objekt bleibt zwischengespeichert, bis die
Lebensdauer des Objekts abgelaufen ist. Wenn die Lebensdauer abläuft,
prüft das CDN, ob der CDN-Endpunkt weiterhin gültig ist
und ob weiterhin anonym auf das Objekt zugegriffen werden kann. Ist dies nicht der Fall,
wird das Objekt nicht mehr zwischengespeichert.

Die Möglichkeit zum sofortigen Löschen von Inhalten wird im Azure-Verwaltungsportal momentan nicht unterstützt. Wenden Sie sich an [Azure-Support](http://azure.microsoft.com/support/options/)  Wenn Sie Inhalte sofort löschen müssen. 

## Zusätzliche Ressourcen

-   [Erstellen einer Affinitätsgruppe in Azure]
-   [Vorgehensweise: Verwalten von Speicherkonten für ein Azure-Abonnement]
-   [Über die Dienstverwaltungs-API]
-   [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Azure CDN Node Locations]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Azure classic portal]: https://manage.windowsazure.com/
  [billing plan]: /pricing/calculator/?scenario=full
  [How to Create an Affinity Group in Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [About the Service Management API]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [How to Map CDN Content to a Custom Domain]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png


