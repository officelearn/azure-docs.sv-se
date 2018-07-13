En virtuell dator i Azure stöder tillkoppling av ett antal datadiskar. Den här artikeln beskriver mål för skalbarhet och prestanda för en virtuell dators datadiskar. Använd dessa mål för att bestämma antalet och typen av disk som du behöver för att uppfylla dina krav på prestanda och kapacitet. 

> [!IMPORTANT]
> Begränsa antalet högutnyttjade diskar som är anslutna till den virtuella datorn för att undvika eventuell begränsning för optimala prestanda. Om alla anslutna diskar inte används mycket på samma gång, kan den virtuella datorn stöder ett större antal diskar.

* **Hanterade diskar för Azure:** 

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
  
    Du kan på ett ungefär beräkna antalet högutnyttjade diskar som stöds av ett enda standardlagringskonto baserat på gränsen för begärandehastigheten. Till exempel för en grundläggande nivå virtuell dator, det maximala antalet högutnyttjade diskar som är cirka 66 (20 000/300 IOPS per disk) och för en virtuell dator på standardnivå, är den cirka 40 (20 000/500 IOPS per disk). 

* **För premiumlagringskonton:** Ett premiumlagringskonto har en högsta totala dataflödeshastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

