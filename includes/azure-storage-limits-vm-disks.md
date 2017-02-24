En virtuell dator i Azure stöder tillkoppling av ett antal datadiskar. För optimala prestanda bör du begränsa antalet högutnyttjade diskar som är anslutna till den virtuella datorn för att undvika eventuell begränsning. Om alla diskar inte är används mycket på samma gång kan lagringskontot användas med ett större antal diskar.

* **För Azure Managed Disks:** Gränsvärdet för Managed Disks är regionalt för prenumerationen. Den flexibla standardgränsen är 2 000. Kontakta Azure-supporten om du vill öka gränsen.

    Hanterade ögonblicksbilder och bilder räknas mot gränsen för hanterade diskar.

* **För standardlagringskonton:** Ett standardlagringskonto har en högsta totala begärandefrekvens på 20 000 IOPS. Det totala antalet IOPS för alla virtuella datordiskar på ett standardlagringskonto bör inte överskrida den här gränsen.
  
    Du kan på ett ungefär beräkna antalet högutnyttjade diskar som stöds av ett enda standardlagringskonto baserat på gränsen för begärandehastigheten. För en virtuell dator på Basic-nivån är det högsta antalet högutnyttjade diskar exempelvis cirka 66 (20 000/300 IOPS per disk) och för en virtuell dator på standardnivå är den cirka 40 (20 000/500 IOPS per disk), illustrerat i tabellen nedan. 
* **För premiumlagringskonton:** Ett premiumlagringskonto har en högsta totala dataflödeshastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.



<!--HONumber=Feb17_HO2-->


