Als Nächstes, wenn alle Server auf dem Cluster Windows Server 2008 R2 oder Windows Server 20012 ausgeführt werden, Sie müssen sicherstellen, dass das Hotfix [KB2854082](http://support.microsoft.com/kb/2854082) auf jedem der lokale Server oder Azure-VMs, die Teil des Clusters installiert ist. Auf allen Servern oder virtuellen Computern, die sich im Cluster befinden, aber nicht in der Verfügbarkeitsgruppe, sollte dieser Hotfix ebenfalls installiert sein.

In der Remotedesktopsitzung für jeden Clusterknoten herunterladen [KB2854082](http://support.microsoft.com/kb/2854082) in ein lokales Verzeichnis. Installieren Sie dann den Hotfix nacheinander auf allen Clusterknoten. Wenn der Clusterdienst derzeit auf dem Clusterknoten ausgeführt wird, wird der Server am Ende der Installation des Hotfixes neu gestartet.

>[AZURE.WARNING] Den Clusterdienst beenden oder Neustarten des Servers wirkt sich auf den quorumzustand des Clusters und der verfügbarkeitsgruppe, und kann dazu führen, dass den Cluster offline geschaltet. Um die hohe Verfügbarkeit des Clusters während der Installation zu gewährleisten, überprüfen Sie Folgendes:
>
> - Der Cluster weist einen optimalen Quorumzustand auf. 
> - Vor der Installation des Hotfixes auf einem Knoten sind alle Clusterknoten online.
> - Die Installation des Hotfixes auf einem Knoten wird vollständig abgeschlossen, einschließlich des Neustarts des Servers, bevor die Installation des Hotfixes auf einem anderen Knoten im Cluster ausgeführt wird.


