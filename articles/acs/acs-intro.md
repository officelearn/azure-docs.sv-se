<properties
   pageTitle="Einführung in Azure Container Service | Microsoft Azure"
   description="Azure Container Service (ACS) bietet eine Möglichkeit zur Vereinfachung der Erstellung, Konfiguration und Verwaltung eines Clusters virtueller Computer, die für die Ausführung von Anwendungen in Containern vorkonfiguriert sind."
   services="virtual-machines"
   documentationCenter=""
   authors="rgardler"
   manager="nepeters"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, Mesos, Azure"/>
   
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="home-page"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="rogardle"/>

# Einführung in Azure Container Service

Azure Container Zugriffssteuerungsdiensts (ACS) bietet eine Möglichkeit zur Vereinfachung der erstellungs,
Konfiguration und Verwaltung eines Clusters von virtuellen Maschinen
zum Ausführen von containerimage Anwendungen vorkonfiguriert. Optimierte verwenden
Konfiguration von gängigen Open-Source-Planung und Orchestrierung
ACS ermöglicht es Ihnen, zeichnen bei einer großen oder nutzen Sie Ihre vorhandenen Kenntnisse und
wachsende Text Community Kenntnisse zum Bereitstellen und Verwalten von Container-basierte
Anwendungen auf Microsoft Azure.

<br />
![ACS bietet eine Möglichkeit zum Verwalten von in Containern ausgeführten Anwendungen auf mehreren Hosts in Azure.](./media/acs-intro/acs-cluster.png)
<br /><br />

ACS nutzt Docker, um sicherzustellen, dass Ihre Anwendungscontainer
vollständig portabel. Sie unterstützt auch die Auswahl des Marathon, Chronos und
Apache Mesos oder Docker Swarm, um sicherzustellen, dass diese Programme werden kann
Skalierung auf Tausende, sogar Zehntausende von Containern.

Der Azure-Container-Dienst können Sie nutzen die
Enterprise Grade der Azure-Funktionen bei gleichzeitiger Wahrung der Anwendung
Portieren zu können, einschließlich der auf der Orchestrierung.

Während der Dienst in der Vorschau ist, bitten wir, der diese testen möchten
der Dienst [selbst nominiert](http://aka.ms/acspreview). Nachdem der Vorschauzugriff bereitgestellt wurde, wird eine E-Mail mit weiteren Details gesendet, einschließlich Bereitstellungsvorlagen und Anweisungen für die ersten Schritte. Um den Dienst zu verwenden, Sie benötigen ein Azure-Abonnement, wenn Sie eine noch, warum nicht melden Sie sich für ein
[frei
Testversion](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AA4C1C935)?

Verwenden von Azure Container Service
-----------------------------

Unser Ziel mit dem Azure-Container-Dienst ist einen Container bereitstellen
Hosting-Umgebung mithilfe der open Source-Tools und Technologien sind
Beliebte zwischen unseren Kunden heute. Zu diesem Zweck wird den Standard verfügbar machen
API-Endpunkte für Docker und die ausgewählte Orchestrator. Mithilfe dieser
Endpunkte können Sie keine Software nutzen, die Kommunikation mit kann
Diese Endpunkte. Beispielsweise bei der Docker Swarm-Endpunkt
Sie können Docker Compose, verwenden Sie für Apache Mesos können Sie
Wählen Sie die DCOS CLI.

Erstellen eines Docker-Clusters mithilfe von Azure Container Service
-------------------------------------------------------

Sobald Sie haben [angeforderte](http://aka.ms/acspreview) und Zugriff auf erteilt wurde
der Vorschau können Sie eine Reihe von Azure-Ressourcen-Manager verwenden
Vorlagen, die Ihnen ermöglichen, Ihre erste Cluster über den Azure bereitzustellen.
löschen. Diese Vorlagen verwenden, Sie können schnell einen Dienst erstellen und
beginnen Sie mit diesem sofort bereitstellen. Beginnen Sie
Legen Sie die Größe des Clusters und möchten Sie nur müssen
Verwenden Sie Docker Swarm oder Apache Mesos, um Ihre Anwendung zu verwalten.

Sie können auch [über die Befehlszeile](/documentation/articles/resource-group-template-deploy/)
Azure-Container-Dienste mithilfe von demselben Vorlagen zu erstellen. Nach dem
mit diesen Vorlagen durchgeführt haben, können Sie die Struktur vertraut sind
Schreiben Sie eigene und vollständig automatisieren der Erstellung eines Azure-Containers
Service-Cluster.

Eine vollständige Dokumentation und Support erhalten Teilnehmer der Vorschau
und hier veröffentlicht wird, sobald der Dienst für die Öffentlichkeit geöffnet wird.

Bereitstellen einer Anwendung
------------------------

Während der Vorschau, bieten wir eine Auswahl von Docker Swarm oder Apache
Mesos (mit DCOS Marathon und DCOS Chronos-Frameworks)
für die Orchestrierung. 

### Verwenden von Apache Mesos

Apache Mesos wird bei der Apache Software untergebracht open Source-Projekt
Foundation. Es enthält einige der [größten
IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/) als
Benutzer "und" Contributors ".

![Für Swarm konfigurierter ACS mit angezeigten Agents und Mastern.](media/acs-intro/acs-mesos.png)

Mesos weist einen beeindruckenden Funktionsumfang auf.

-   Skalierbarkeit auf bis zu 10.000 Knoten

-   Fehlertolerant replizierte Master und Slaves mithilfe von ZooKeeper

-   Unterstützung für Docker-Container

-   Systemeigene Isolierung zwischen Aufgaben mit Linux-Containern

-   Zeitplanung von mehreren Ressourcen (Arbeitsspeicher, CPU, Datenträger und Ports)

-   Java-, Python- und C++-APIs für die Entwicklung neuer paralleler Anwendungen

-   Webbenutzeroberfläche zum Anzeigen von Clusterzuständen

Mesos bietet Unterstützung für eine große Anzahl von
[Frameworks](http://mesos.apache.org/documentation/latest/frameworks/)
kann verwendet werden, für die Planung von Arbeitslasten auf den Azure-Container
Dienst: Standardmäßig enthält ACS die Marathon- und Chronos-Frameworks.

#### Verwenden von Marathon und Chronos

Marathon ist ein für den gesamten Cluster Init und Steuerelement-System für Dienste in
Cgroups oder, im Falle von ACS, Docker-Container. Es ist ein idealer
Partner Chronos, ein Fault tolerant Auftragsplaner für Mesos die
verarbeitet Abhängigkeiten und die Uhrzeit auf Grundlage der Zeitpläne.

Marathon und Chronos bieten eine Webbenutzeroberfläche mit dem bereitgestellt Ihrer
Clientanwendungen. Sie werden dies an eine URL zugreifen, die etwa so wie aussieht
`http://DNS\_PREFIX.REGION.cloudapp.azure.com`
wobei werden DNS\_PREFIX und REGION zum Zeitpunkt der Bereitstellung definiert. Der
Natürlich können Sie Ihren eigenen DNS-Namen angeben.

Sie können auch die REST-APIs für die Kommunikation mit Marathon und
Chronos. Gibt es eine Reihe von Clientbibliotheken, die für jedes Tool verfügbar sind,
behandelt eine Vielzahl von Sprachen und, natürlich, Sie können die HTTP verwenden.
Protokoll in einer beliebigen Sprache. Darüber hinaus bieten viele beliebte DevOps-tools
Unterstützung für diesen Planer. Dies bietet maximale Flexibilität für Ihre
Operations-Teams bei der Arbeit mit einem ACS-Cluster.

Eine vollständige Dokumentation und Support erhalten Teilnehmer der Vorschau
und hier veröffentlicht wird, sobald der Dienst für die Öffentlichkeit geöffnet wird.

### Verwenden von Docker Swarm

Docker Swarm bietet eine systemeigene Clusterfunktion für Docker. Da Docker Swarm
standard-API von Docker, ein Tool, das bereits mit kommuniziert dient
Docker-Daemon können transparent Skalierung auf mehreren Hosts Swarm
Azure-Container-Dienst. 

![Für die Verwendung von Apache Mesos konfigurierter ACS mit angezeigter Jumpbox, Agents und Mastern.](media/acs-intro/acs-swarm.png)

Unterstützte Tools zum Verwalten von Containern in einem Swarm-Cluster enthalten, jedoch nicht beschränkt
um die folgenden:

-   Dokku

-   Docker CLI und Docker Compose

-   Krane

-   Jenkins

Eine vollständige Dokumentation und Support erhalten Teilnehmer der Vorschau
und hier veröffentlicht wird, sobald der Dienst für die Öffentlichkeit geöffnet wird.

Erhalten von Zugriff
--------------

Während der Dienst in der Vorschau ist bitten wir, dass die Tests die
Dienst [selbst nominiert](http://aka.ms/acspreview). Sie benötigen zunächst eine Azure
Abonnement, wenn Sie eine noch nicht melden Sie sich für eine [frei
Testversion](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AA4C1C935)?

Videos
------
AzureCon-Ankündigung:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Erste Schritte mit ACS:  

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]


