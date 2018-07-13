| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Virtuella datorer](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per molntjänst<sup>1</sup> |50 |50 |
| Ange slutpunkter per molntjänst<sup>2</sup> |150 |150 |

<sup>1</sup>virtuella datorer som skapats i Service Management (i stället för Resource Manager) lagras automatiskt i en molntjänst. Du kan lägga till fler virtuella datorer till Molntjänsten för Utjämning av nätverksbelastning och tillgänglighet. 

<sup>2</sup>indata slutpunkter gör det möjligt för kommunikation till en virtuell dator från utanför den virtuella datorns tjänst i molnet. Virtuella datorer i samma molntjänst eller virtuella nätverk automatiskt kan kommunicera med varandra. Se [så här konfigurerar du slutpunkter till en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 

