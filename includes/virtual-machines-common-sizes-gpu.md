
GPU optimerade VM storlekar är för särskilda virtuella datorer som mål för tunga grafisk återgivning och redigering av video. Tillgängligt med en eller flera GPU-kort. Den här artikeln innehåller information om antalet vCPUs, datadiskar och nätverkskort samt lagring genomflöde och nätverket bandbredden för varje storlek i den här grupperingen. 


NC- och NV-storlekarna kallas även GPU-aktiverade instanser. De är särskilda virtuella datorstorlekar som innehåller NVIDIA GPU-kort som optimerats för olika scenarier och användningsfall. NV-storlekarna är optimerade och utformade för fjärrvisualiserings-, strömnings-, spel-, kodnings- och VDI-scenarier som använder ramverk som OpenGL och DirectX. NC-storlekarna är mer optimerade för beräkningsintensiva och nätverksintensiva tillämpningar och algoritmer, inklusive CUDA- och OpenCL-baserade program och simuleringar. 


NV-instanserna drivs av NVIDIA:s Tesla M60 GPU-kort och NVIDIA GRID för skrivbordsaccelererade program och virtuella skrivbord där kunder kan visualisera sina data eller simuleringar. Användare kan visualisera sina grafik beräkningsintensiva arbetsflöden för att hämta kapacitet överlägsen grafik och dessutom köra enkel precision arbetsbelastningar som till exempel kodning och återgivning NV instanser. Tesla M60 levererar 4 096 CUDA-kärnor i en design med dubbla GPU:er med upp till 36 dataströmmar med 1080p H.264. 

NC-instanserna drivs av NVIDIA:s Tesla K80-kort. Nu kan användarna bearbeta data mycket snabbare genom att utnyttja CUDA för energiutforskningstillämpningar, kraschsimulering, strålspårning (raytracing), deep learning och mycket annat. Tesla K80 levererar 4 992 CUDA-kärnor med en design med dubbla GPU:er, upp till 2,91 teraflops med dubbel precision och upp till 8,93 teraflops prestanda med enkel precision.

## <a name="nv-instances"></a>NV-instanser

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = ett halvt M60-kort.

## <a name="nc-instances"></a>NC-instanser

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = ett halvt K80-kort.

*RDMA-stöd


