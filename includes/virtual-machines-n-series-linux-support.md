## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds


### <a name="nc-ncv2-and-nd-instances---nvidia-cuda-drivers"></a>Instanser av NC-NCv2 och ND - NVIDIA CUDA drivrutiner
| Distribution | Drivrutin |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/> CentOS 7.3 eller 7.4 | NVIDIA CUDA 9.1, drivrutinen gren R390 |

> [!IMPORTANT]
> Se till att du installerar eller uppgraderar till de senaste drivrutinerna för CUDA för din distribution. Drivrutiner som är äldre än version R390 kan ha problem med uppdaterade Linux kärnor.
>

### <a name="nv-instances---nvidia-grid-drivers"></a>NV-instanser – NVIDIA GRID-drivrutiner


| Distribution | Drivrutin |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS-baserad 7.3 | NVIDIA RUTNÄTET 5.2, drivrutinen gren R384|

> [!NOTE]
> Microsoft distribuerar NVIDIA RUTNÄTET drivrutinen installationsprogram för NV virtuella datorer. Installera endast drivrutinerna RUTNÄTET på NV virtuella Azure-datorer. De här drivrutinerna inkluderar licensiering för RUTNÄTET virtuella GPU-programvara i Azure.
>

> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
