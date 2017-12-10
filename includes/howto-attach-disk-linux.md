
Mer information om diskar finns i [Om diskar och virtuella hårddiskar för Virtual Machines](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Ansluta en tom disk
1. Öppna Azure CLI 1.0 och [anslut till din Azure-prenumeration](/cli/azure/authenticate-azure-cli). Kontrollera att du är i läget för Azure-tjänsthantering (`azure config mode asm`).
2. Ange `azure vm disk attach-new` för att skapa och ansluta en ny disk enligt följande exempel. Ersätt *myVM* med namnet på din virtuella Linux-dator och ange diskens storlek i GB, vilken är *100GB* i det här exemplet:

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. När datadisken har skapats och anslutits visas den i utdata för `azure vm disk list <virtual-machine-name>` som du kan se i det här exemplet:
   
    ```azurecli
    azure vm disk list TestVM
    ```

    Utdata ser ut ungefär så här:

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
För att kunna ansluta en befintlig disk måste du ha en VHD-fil tillgänglig i ett lagringskonto.

1. Öppna Azure CLI 1.0 och [anslut till din Azure-prenumeration](/cli/azure/authenticate-azure-cli). Kontrollera att du är i läget för Azure-tjänsthantering (`azure config mode asm`).
2. Kontrollera om den virtuella hårddisken som du vill ansluta redan har överförts till din Azure-prenumeration:
   
    ```azurecli
    azure vm disk list
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Om du inte hittar den disk som du vill använda, kan du överföra en lokal virtuell hårddisk till din prenumeration med hjälp av `azure vm disk create` eller `azure vm disk upload`. Ett exempel på `disk create`:
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   Du kan också överföra en virtuell hårddisk till ett visst lagringskonton med `azure vm disk upload`. Mer information om kommandon för att hantera datadiskar för virtuella Azure-datorer hittar du [här](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

4. Anslut önskad virtuell hårddisk till din virtuella dator:
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Se till att ersätta *myVM* med namnet på din virtuella dator och *myVHD* med önskad virtuell hårddisk.

5. Du kan kontrollera att disken är ansluten till den virtuella datorn med `azure vm disk list <virtual-machine-name>`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> När du har lagt till en datadisk måste du logga in på den virtuella datorn och initiera disken så att den virtuella datorn kan använda disken för lagring (mer information om hur du initierar disken finns i följande anvisningar).
> 
> 

