| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Virtuella datorer](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per Molntjänsten<sup>1</sup> |50 |50 |
| Ange slutpunkter per Molntjänsten<sup>2</sup> |150 |150 |

<sup>1</sup>lagras automatiskt virtuella datorer som skapats i Service Management (i stället för Resource Manager) i en molntjänst. Du kan lägga till fler virtuella maskiner Molntjänsten för belastningsutjämning och tillgänglighet. 

<sup>2</sup>indata slutpunkter tillåta kommunikation till en virtuell dator från utanför molntjänst för den virtuella datorn. Virtuella datorer i samma molntjänst eller virtuella nätverk automatiskt kan kommunicera med varandra. Se [konfigurera slutpunkter till en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 

