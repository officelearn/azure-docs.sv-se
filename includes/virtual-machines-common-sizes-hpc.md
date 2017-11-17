
Storlekarna i A8–A11- och H-serien kallas även för *beräkningsintensiva instanser*. Maskinvaran som kör dessa storlekar är utformad och optimerad för beräkningsintensiva och nätverksintensiva program, inklusive HPC-klustertillämpningar (databehandling med höga prestanda), modellering och simuleringar. A8–A11-serien använder Intel Xeon E5-2670 @ 2,6 GHZ och H-serien använder Intel Xeon E5-2667 v3 @ 3,2 GHz.  Den här artikeln innehåller information om antalet vCPUs, diskar och nätverkskort samt lagring genomflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

Virtuella datorer i Azure H-serien är nästa generations virtuella datorer för databehandling med höga prestanda och är avsedda för höga beräkningsbehov, som molekylär modellering och beräkningsströmningsdynamik. Dessa virtuella datorer med 8 och 16 virtuella processorer bygger på Intel Haswell E5-2667 V3-processorteknik med DDR4-minne och SSD-baserad temporär lagring. 

Förutom den imponerande processorkraften erbjuder H-serien olika alternativ för RDMA-nätverk med låg fördröjning med FDR InfiniBand och flera minneskonfigurationer som ger stöd för minnesintensiva beräkningskrav.



## <a name="h-series"></a>H-serien

ACU: 290–300

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 × 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 × 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 × 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 × 500 |4  |
| Standard_H16r* |16 |112 |2000 |64 |64 × 500 |4  |
| Standard_H16mr* |16 |224 |2000 |64 |64 × 500 |4 |

*För MPI-program används FDR InfiniBand-nätverk, som har extremt korta svarstider och hög bandbredd, för att upprätta dedikerade RDMA-servernätverk.

<br>



## <a name="a-series---compute-intensive-instances"></a>A-serien – beräkningsintensiva instanser

ACU: 225

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |32 |32 × 500 |2 |
| Standard_A9* |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 × 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

*För MPI-program används FDR InfiniBand-nätverk, som har extremt korta svarstider och hög bandbredd, för att upprätta dedikerade RDMA-servernätverk.

<br>



