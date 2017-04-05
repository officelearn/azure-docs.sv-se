NC- och NV-storlekarna kallas även GPU-aktiverade instanser. De är specialiserade virtuella datorer som använder NVIDIA:s GPU-kort och som är optimerade för olika scenarier och användningsfall. NV-storlekarna är optimerade och utformade för fjärrvisualiserings-, strömnings-, spel-, kodnings- och VDI-scenarier med ramverk som OpenGL och DirectX. NC-storlekarna är mer optimerade för beräkningsintensiva och nätverksintensiva tillämpningar och algoritmer, inklusive CUDA- och OpenCL-baserade program och simuleringar. 


NV-instanserna drivs av NVIDIA:s Tesla M60 GPU-kort och NVIDIA GRID för skrivbordsaccelererade program och virtuella skrivbord där kunder kan visualisera sina data eller simuleringar. Användarna kan visualisera grafikintensiva arbetsflöden på NV-instanserna och få överlägsen grafikkapacitet samt köra enskilda precisionsarbetsbelastningar som kodning och rendering. Tesla M60 levererar 4 096 CUDA-kärnor i en design med dubbla GPU:er med upp till 36 dataströmmar med 1080p H.264. 

NC-instanserna drivs av NVIDIA:s Tesla K80-kort. Nu kan användarna bearbeta data mycket snabbare genom att utnyttja CUDA för energiutforskningstillämpningar, kraschsimulering, strålspårning (raytracing), deep learning och mycket annat. Tesla K80 levererar 4 992 CUDA-kärnor med en design med dubbla GPU:er, upp till 2,91 teraflops med dubbel precision och upp till 8,93 teraflops prestanda med enkel precision.

## <a name="nv-instances"></a>NV-instanser

| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = ett halvt M60-kort.

**Operativsystem som stöds**

* Windows Server 2016, Windows Server 2012 R2 – läs avsnittet om [drivrutinsinstallationer för N-serien för Windows](../articles/virtual-machines/windows/n-series-driver-setup.md)

## <a name="nc-instances"></a>NC-instanser

| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = ett halvt K80-kort.

*RDMA-stöd

**Operativsystem som stöds**

* Windows Server 2016, Windows Server 2012 R2 – läs avsnittet om [drivrutinsinstallationer för N-serien för Windows](../articles/virtual-machines/windows/n-series-driver-setup.md)
* Ubuntu 16.04 LTS – läs avsnittet om [drivrutinsinstallationer för N-serien för Linux](../articles/virtual-machines/linux/n-series-driver-setup.md)

<br>

