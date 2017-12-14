När du inte längre behöver en datadisk som är ansluten till en virtuell dator (VM) kan du enkelt koppla bort den. När du kopplar bort en disk från den virtuella datorn tas den inte bort från lagringsutrymmet. Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator eller till en annan.  

> [!NOTE]
> En virtuell dator i Azure använder olika typer av diskar – en operativsystemdisk, en lokal temporär disk och valfria datadiskar. Mer information finns i avsnittet om [diskar och virtuella hårddiskar för virtuella datorer](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Du kan inte koppla bort en operativsystemdisk om du inte också tar bort den virtuella datorn.

## <a name="find-the-disk"></a>Hitta disken
Innan du kan koppla bort en disk från en virtuell dator måste du ta reda på LUN-numret, som är en identifierare för disken som ska kopplas från. Det gör du genom att följa dessa steg:

1. Öppna Azure CLI och [anslut till din Azure-prenumeration](/cli/azure/authenticate-azure-cli). Kontrollera att du är i läget för Azure-tjänsthantering (`azure config mode asm`).
2. Ta reda på vilka diskar som är anslutna till den virtuella datorn. I följande exempel visas diskar för den virtuella datorn med namnet `myVM`:

    ```azurecli
    azure vm disk list myVM
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Notera LUN-numret eller det **logiska enhetsnumret** för den disk som du vill koppla från.

## <a name="remove-operating-system-references-to-the-disk"></a>Ta bort operativsystemreferenser till disken
Innan du kopplar bort disken från Linux-gästen kontrollerar du att inga partitioner på disken används. Kontrollera att operativsystemet inte försöker montera om dem efter en omstart. Dessa steg ångrar konfigurationen som du antagligen skapade när du [anslöt](../articles/virtual-machines/linux/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) disken.

1. Använd `lsscsi`-kommandot för att identifiera diskens identifierare. `lsscsi` kan installeras med `yum install lsscsi` (Red Hat-baserade distributioner) eller med `apt-get install lsscsi` (Debian-baserade distributioner). Du kan hitta diskidentifieraren som du letar efter med hjälp av LUN-numret. Det sista numret i tuppeln på varje rad är LUN-numret. I följande exempel från `lsscsi` mappar LUN 0 till */dev/sdc*

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Använd `fdisk -l <disk>` för att identifiera de partitioner som associeras med disken som ska kopplas från. Följande exempel visar utdata för `/dev/sdc`:

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Demontera alla partitioner som visas för disken. I följande exempel demonteras `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

4. Använd kommandot `blkid` för att identifiera UUID:n för alla partitioner. Utdata ser ut ungefär så här:

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. Ta bort poster i **/etc/fstab**-filen som är associerade med enhetssökvägarna eller med UUID:n för alla partitioner för disken som ska kopplas från.  Poster i det här exemplet kan vara:

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    eller
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>Koppla från disken
När du har hittat LUN-numret för disken och tagit bort operativsystemreferenserna är du redo att koppla från disken:

1. Koppla från den valda disken från den virtuella datorn genom att köra kommandot `azure vm disk detach
   <virtual-machine-name> <LUN>`. I följande exempel kopplas LUN `0` bort från den virtuella datorn med namnet `myVM`:
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. Du kan kontrollera att disken har kopplats från genom att köra `azure vm disk list` igen. I följande exempel kontrollerar vi den virtuella datorn med namnet `myVM`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    De utdata som returneras liknar dem i följande exempel, som visar att datadisken inte längre är ansluten:

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

Den frånkopplade disken finns kvar i lagringsutrymmet men är inte längre kopplad till en virtuell dator.

