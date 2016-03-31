**1. Wie werden Kunden des eingestellten SDK benachrichtigt?**

Bei Deaktivierung eines SDK benachrichtigt Microsoft seine Kunden 12 Monate vor Beendigung der Unterstützung, um einen reibungslosen Übergang zu einem unterstützten SDK sicherzustellen. Kunden werden über verschiedene Kommunikationskanäle benachrichtigt: Azure-Verwaltungsportal, Developer Center, Blogbeitrag und direkter Austausch mit den Administratoren der entsprechenden Dienste.

**2. Können Kunden, die Anwendungen mit einer "mit" deaktivierte DocumentDB SDK während des Zeitraums von 12 Monaten erstellen?** 

Ja, mit einem solchen DocumentDB SDK können Kunden während der 12-monatigen Frist Anwendungen bei vollem Zugriff erstellen, bereitstellen und ändern. Während der 12-monatigen Frist wird Kunden empfohlen, ggf. zu einer neueren unterstützten Version des DocumentDB SDK zu wechseln.

**3. Können Kunden erstellen und Ändern von Anwendungen, die mit einer deaktivierten DocumentDB-SDK nach der Benachrichtigungszeitraum von 12 Monaten?**

Das SDK wird nach Ablauf des 12-monatigen Benachrichtigungszeitraums deaktiviert. Der Zugriff auf DocumentDB durch eine Anwendung mithilfe eines deaktivierten SDK wird von der DocumentDB-Plattform nicht zugelassen. Darüber hinaus bietet Microsoft keinen Kundensupport für das deaktivierte SDK.

**4. Was geschieht mit Kunden Programme, die nicht unterstützte DocumentDB SDK-Version verwenden?**

Alle Versuche zum Herstellen einer Verbindung mit dem DocumentDB-Diensts mit einer deaktivierten SDK-Version werden abgelehnt. 

**5. Neue Features und Funktionen werden für alle nicht zurückgezogen SDKs angewendet werden**

Neue Features und Funktionen werden nur neuen Versionen hinzugefügt. Wenn Sie eine alte, noch aktivierte Version des SDK verwenden, funktionieren Ihre Anforderungen an DocumentDB weiter wie bisher, doch Sie haben keinen Zugriff auf neue Funktionen.  

**6. Was tun kann ich, wenn ich meine Anwendung, bevor Sie einen Stichtag aktualisieren können**

Es wird empfohlen, so früh wie möglich auf das neueste SDK zu aktualisieren. Sobald ein SDK für die Deaktivierung markiert wurde, bleiben Ihnen 12 Monate zur Aktualisierung Ihrer Anwendung. Wenn, aus irgendeinem Grund nicht abschließen der Anwendung des Updates innerhalb dieses Zeitraums wenden Sie sich an den [DocumentDB Team](mailto:askdocdb@microsoft.com) und deren Unterstützung vor dem Umstellungsjahr.


