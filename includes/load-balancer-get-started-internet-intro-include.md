Mit einem Lastenausgleichsmodul erreichen Sie eine hohe Verfügbarkeit für Ihre Workloads in Azure. Beim Azure-Load Balancer handelt sich um einen Load Balancer der Schicht 4 (TCP, UDP), das eingehenden Datenverkehr zwischen funktionierenden Dienstinstanzen in Clouddiensten oder auf virtuelle Computer verteilt, die in einer Load Balancer-Gruppe definiert wurden.
 
Ein Lastenausgleichsmodul lässt sich konfigurieren, um

- einen Lastenausgleich des auf den virtuellen Computern (VMs) eingehenden Internetdatenverkehrs herzustellen. Wir bezeichnen ein Lastenausgleich in diesem Szenario als ein [des Lastenausgleichs mit Internetzugriff](load-balancer-internet-overview.md).
- Lastenausgleich für Datenverkehr zwischen VMs in einem virtuellen Netzwerk (VNet), zwischen virtuellen Computern in Clouddiensten oder zwischen lokalen Computern und VMs in einem standortübergreifenden virtuellen Netzwerk. Wir bezeichnen ein Lastenausgleich in diesem Szenario als ein [internen Lastenausgleich (ILB)](load-balancer-internal-overview.md).
-   Weiterleiten von externem Datenverkehr an eine bestimmte VM-Instanz.


