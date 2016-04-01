<a name="tellmecs"></a>
## Informationen zu Cloud Services

Cloud Services ist ein Beispiel für Platform-as-a-Service (PaaS). Wie [Anwendungsdienste](app-service-web-overview.md), diese Technologie unterstützt Clientanwendungen, die skalierbare, zuverlässige und niedrigen Betriebskosten. Genau wie ein [Anwendungsdienste](app-service-web-overview.md) also auf virtuellen Computern gehostet werden auch die Cloud-Dienste, allerdings haben Sie mehr Kontrolle über die virtuellen Computer. Sie können Ihre eigene Software auf Clouddienst-VMs installieren und per Remotezugriff auf diese zugreifen.

![cs_diagramcs_diagram](./media/cloud-services-choose-me-content/diagram.png) 

Mehr Kontrolle bedeutet auch weniger anwenderfreundlichkeit; Wenn Sie die zusätzlichen Optionen benötigen, ist in der Regel schneller und einfacher, eine Webanwendung zu erstellen und im Web-Apps in App Service ausgeführt mit verglichen Cloud-Dienste. 

Die Technologie bietet zwei leicht abweichende Optionen für VM: Instanzen *Webrollen* läuft eine Variante von Windows Server mit IIS, während auf Instanzen von *Workerrollen* der gleichen Windows Server-Variante ohne IIS läuft. Cloud Services-Anwendungen verwenden dieselbe Kombination dieser zwei Optionen. 

Eine beliebige Kombination dieser zwei leicht unterschiedlichen VM Hostingoptionen sind in einem Clouddienst verfügbar:

* **Webrolle**  
  Wird Windows Server mit Ihrer Web-app automatisch auf IIS bereitgestellt.
* **Workerrolle**  
  WindowsServer ohne IIS ausgeführt wird.

Einfache Anwendungen verwenden z. B. nur eine Webrolle, während komplexere Anwendungen eine Webrolle für die Verarbeitung eingehender Benutzeranforderungen verwenden und die aus diesen Anforderungen entstandene Arbeit an Workerrollen abgeben. (Diese Kommunikation können [Service Bus](../articles/service-bus/fundamentals-service-bus-hybrid-solutions.md) oder [Azure-Warteschlangen](../articles/storage/storage-introduction.md).)

Die obige Abbildung zeigt, dass alle VMs in einer einzigen Anwendung im gleichen Clouddienst laufen. Aus diesem Grund greifen die Benutzer über eine einzige öffentliche IP-Adresse auf die Anwendung zu, und die Anforderungen werden automatisch auf die einzelnen VMs  verteilt. Die Plattform [skalieren und Bereitstellen von](../articles/cloud-services/cloud-services-how-to-scale.md) die virtuellen Computer in einer Cloud-Dienste-Anwendung in einer Weise, die eine einzelne Fehlerquellen vermieden. 

Obwohl die Anwendungen in Virtual Machines laufen, ist es wichtig, zu betonen, dass Cloud Services PaaS und nicht IaaS bieten. Sie können sich dies auf folgende Weise vorstellen: Mit IaaS, z. B. mit Azure Virtual Machines, erstellen und konfigurieren Sie zunächst die Umgebung für Ihre Anwendung und stellen die Anwendung anschließend in Ihrer Umgebung bereit. Sie sind für die Verwaltung dieser Umgebung verantwortlich und müssen z. B. neue gepatchte Versionen des Betriebssystems in den einzelnen VM installieren. Mit PaaS dagegen existiert diese Umgebung bereits. Sie müssen nur noch Ihre Anwendung bereitstellen. Sie brauchen sich nicht um die Verwaltung der Plattform kümmern, inklusive neuer Versionen des Betriebssystems.

## Skalierung und Verwaltung
Mit Cloud Services erstellen Sie keine Virtual Machines. Stattdessen stellen Sie eine Konfigurationsdatei, die Azure anweist, wie viele Instanzen Sie, z. B. **drei Webrolleninstanzen** und **zwei Workerrolleninstanzen**, und die Plattform erstellt diese für Sie.  Wählen Sie Sie immer noch [Größe](../articles/cloud-services/cloud-services-sizes-specs.md) Diese virtuellen Computer zu sichern, aber Sie nicht explizit sie selbst erstellen. Wenn Ihre Anwendung eine größere Last verarbeiten muss, können Sie von Azure weitere virtuelle Computer anfordern. Wenn die Last abnimmt, können Sie diese Instanzen abschalten und müssen diese nicht länger bezahlen.

Cloud Services-Anwendungen werden den Benutzern normalerweise in zwei Schritten bereitgestellt. Ein Entwickler zuerst [lädt die Anwendung](../articles/cloud-services/cloud-services-how-to-create-deploy.md) in Stagingbereich der Plattform. Wenn die Entwickler die Anwendung live schalten möchten, können sie diese über das Azure-Verwaltungsportal in den Produktionsmodus umschalten. Diese [Wechsel zwischen Staging und Produktion](../articles/cloud-services/cloud-services-nodejs-stage-application.md) erfolgt ohne Ausfallzeit, eine laufende Anwendung ohne Beeinträchtigung der Benutzer auf eine neue Version aktualisiert werden kann. 

## Überwachung
Cloud Services bietet außerdem Überwachungsfunktionen. Wie auch bei Azure Virtual Machines werden Ausfälle von physischen Servern erkannt und die entsprechenden virtuellen Computer auf einem anderen Server neu gestartet. Cloud Services erkennt jedoch neben Hardwarefehlern auch Ausfälle von virtuellen Computern und Anwendungen. Im Gegensatz zu Virtual Machines läuft in jeder Web- und Workerrolle ein Agent. Daher können im Fehlerfall neue virtuelle Computer und Anwendungsinstanzen gestartet werden.

Die PaaS-Funktionsweise von Cloud Services hat auch noch andere Auswirkungen. Zum Beispiel sollten Anwendungen für diese Technologie so entwickelt werden, dass sie auch dann korrekt funktionieren, wenn eine beliebige Web- oder Workerrolle ausfällt. Aus diesem Grund sollten Cloud Services-Anwendungen keinen Status im Dateisystem der eigenen virtuellen Computer speichern. Im Gegensatz zu virtuellen Computern mit Azure Virtual Machines sind Schreibvorgänge in virtuellen Cloud Services-Computern nicht persistent. Diese virtuellen Computer haben keine eigenen Datenträger. Cloud Services-Anwendungen sollten daher ihren Status in SQL-Datenbanken, Blobs, Tabellen oder andere externe Speichermedien schreiben. Die auf diese Weise erstellten Anwendungen sind skalierbarer und fehlerresistenter; zwei wichtige Ziele von Cloud Services.


