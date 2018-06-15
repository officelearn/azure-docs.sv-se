En virtuell dator i Azure stöder tillkoppling av ett antal datadiskar. Den här artikeln beskriver skalbarhets- och prestandamål för en virtuell dators datadiskar. Använd dessa mål för att bestämma antalet och typ av disk som du behöver för att uppfylla dina krav på prestanda och kapacitet. 

> [!IMPORTANT]
> Begränsa antalet hög utnyttjade diskar som är anslutna till den virtuella datorn för att undvika möjlig begränsning för optimala prestanda. Om alla anslutna diskar inte används hög samtidigt, kan den virtuella datorn stöder ett större antal diskar.

* **För Azure-hanterade diskar:** 

> | Resurs | Standardgräns | Övre gräns |
> | --- | --- | --- |
> | Standard Managed Disks | 10 000 | 50,000 |
> | Standard SSD Managed Disks | 10 000 | 50,000 |
> | Premium Managed Disks | 10 000 | 50,000 |
> | Standard_LRS ögonblicksbilder | 10 000 | 50,000 |
> | Standard_ZRS ögonblicksbilder | 10 000 | 50,000 |
> | Premium_LRS ögonblicksbilder | 10 000 | 50,000 |
> | Hanterad avbildning | 10 000 | 50,000 |

* **För standardlagringskonton:** Ett standardlagringskonto har en högsta totala begärandefrekvens på 20 000 IOPS. Det totala antalet IOPS för alla virtuella datordiskar på ett standardlagringskonto bör inte överskrida den här gränsen.
  
    Du kan på ett ungefär beräkna antalet högutnyttjade diskar som stöds av ett enda standardlagringskonto baserat på gränsen för begärandehastigheten. Till exempel för en grundläggande nivån VM, det maximala antalet hög utnyttjade diskar är om 66 (20 000/300 IOPS per disk) och för en Standard-nivån VM, är det ungefär 40 (20 000/500 IOPS per disk). 

* **För premiumlagringskonton:** Ett premiumlagringskonto har en högsta totala dataflödeshastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

