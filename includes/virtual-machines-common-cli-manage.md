Azure CLI kan du skapa och hantera dina Azure-resurser på macOS, Linux och Windows. Den här artikeln beskriver några av de vanligaste kommandon för att skapa och hantera virtuella datorer (VM).

Den här artikeln kräver Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [installera Azure CLI](/cli/azure/install-azure-cli). Du kan också använda [Cloud Shell](/azure/cloud-shell/quickstart) från din webbläsare.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Grundläggande Azure Resource Manager-kommandon i Azure CLI
Mer detaljerad hjälp med särskilda kommandoradsväxlingar och alternativ du kan använda online kommandohjälp och alternativ genom att skriva `az <command> <subcommand> --help`.

### <a name="create-vms"></a>Skapa VM:ar
| Aktivitet | Azure CLI-kommandon |
| --- | --- |
| Skapa en resursgrupp | `az group create --name myResourceGroup --location eastus` |
| Skapa en virtuell Linux-dator | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Skapa en virtuell Windows-dator | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Hantera VM-status
| Aktivitet | Azure CLI-kommandon |
| --- | --- |
| Starta en virtuell dator | `az vm start --resource-group myResourceGroup --name myVM` |
| Stoppa en virtuell dator | `az vm stop --resource-group myResourceGroup --name myVM` |
| Frigöra en virtuell dator | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Starta om en virtuell dator | `az vm restart --resource-group myResourceGroup --name myVM` |
| Distribuera om en VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Ta bort en virtuell dator | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Hämta VM-info
| Aktivitet | Azure CLI-kommandon |
| --- | --- |
| Lista över virtuella datorer | `az vm list` |
| Hämta information om en virtuell dator | `az vm show --resource-group myResourceGroup --name myVM` |
| Få användning av virtuella datorresurser | `az vm list-usage --location eastus` |
| Hämta alla tillgängliga storlekar för virtuella datorer | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Diskar och avbildningar
| Aktivitet | Azure CLI-kommandon |
| --- | --- |
| Lägg till en datadisk i en virtuell dator | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new ` |
| Ta bort en datadisk från en virtuell dator | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Ändra storlek på en disk | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Ögonblicksbild av en disk | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Skapa avbildning av en virtuell dator | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Skapa virtuell dator från avbildningen | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Nästa steg
Ytterligare exempel på CLI-kommandon finns i [skapa och hantera virtuella Linux-datorer med Azure CLI](../articles/virtual-machines/linux/tutorial-manage-vm.md) självstudien.

