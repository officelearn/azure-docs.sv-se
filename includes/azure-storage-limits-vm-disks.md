En virtuell dator i Azure stöder tillkoppling av ett antal datadiskar. Den här artikeln beskriver skalbarhets- och prestandamål för en virtuell dators datadiskar. Använd dessa mål för att bestämma antalet och typ av disk som du behöver för att uppfylla dina krav på prestanda och kapacitet. 

> [!IMPORTANT]
> Begränsa antalet hög utnyttjade diskar som är anslutna till den virtuella datorn för att undvika möjlig begränsning för optimala prestanda. Om alla anslutna diskar inte används hög samtidigt, kan den virtuella datorn stöder ett större antal diskar.

* **För Azure Managed diskar:** disk gränsen för hanterade diskar är per region och typ av disk. Den maximala gränsen och Standardgränsen, är 10 000 hanterade diskar per region och typ av disk för en prenumeration. Du kan till exempel skapa upp till 10 000 standard hanteras diskarna och även 10 000 premium hanterade diskar i en region per prenumeration.

    Hanterad ögonblicksbilder och bilder räknas av mot gränsen för hanterade diskar.

* **För standardlagringskonton:** Ett standardlagringskonto har en högsta totala begärandefrekvens på 20 000 IOPS. Det totala antalet IOPS för alla virtuella datordiskar på ett standardlagringskonto bör inte överskrida den här gränsen.
  
    Du kan på ett ungefär beräkna antalet högutnyttjade diskar som stöds av ett enda standardlagringskonto baserat på gränsen för begärandehastigheten. Till exempel för en grundläggande nivån VM, det maximala antalet hög utnyttjade diskar är om 66 (20 000/300 IOPS per disk) och för en Standard-nivån VM, är det ungefär 40 (20 000/500 IOPS per disk). 

* **För premiumlagringskonton:** Ett premiumlagringskonto har en högsta totala dataflödeshastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

