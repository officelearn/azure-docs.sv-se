Nu finns stöd för två felsökningsfunktioner i Azure: konsolutdata och skärmbild för Azure Virtual Machines Resource Manager-distributionsmodellen. 

När du använder en egen avbildning i Azure eller till och med startar en av plattformsavbildningarna, kan det finnas många orsaker till att en virtuell dator övergår i tillståndet Ej startbar. Med de här funktionerna kan du enkelt diagnostisera och återställa virtuella datorer vid startfel.

För virtuella Linux-datorer kan du lätt visa utdata från konsolloggen på portalen:

![Azure Portal](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
För både virtuella Windows- och Linux-datorer kan du dock även se en skärmbild av den virtuella datorn från hypervisor-program:

![Fel](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

De här båda funktionerna finns för Azure Virtual Machines i alla regioner. Tänk på att det kan ta upp till 10 minuter innan skärmbilder och utdata visas på lagringskontot.

## <a name="common-boot-errors"></a>Vanliga startfel

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Inget operativsystem hittades](https://support.microsoft.com/help/4010142)
- [Startfel eller INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Aktivera diagnostik på en ny virtuell dator
1. När du skapar en ny virtuell dator från förhandsversionsportalen, väljer du **Azure Resource Manager** från listrutan med distributionsmodeller:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. Konfigurera övervakningsalternativet och välj det lagringskonto där du vill placera de här diagnostikfilerna.
 
    ![Skapa en virtuell dator](./media/virtual-machines-common-boot-diagnostics/screenshot4.jpg)

3. Om du distribuerar från en Azure Resource Manager-mall går du till den virtuella datorresursen och lägger till diagnostikprofilavsnittet. Kom ihåg att använda API-versionsrubriken 2015-06-15.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Via diagnostikprofilen kan du välja det lagringskonto där loggarna ska placeras.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Om du vill distribuera en virtuell exempeldator med aktiverad startdiagnostik, kan du kolla in vår repo här.

## <a name="update-an-existing-virtual-machine"></a>Uppdatera en befintlig virtuell dator ##

Du kan också uppdatera en befintlig virtuell dator via portalen om du vill aktivera startdiagnostik via portalen. Välj alternativet Startdiagnostik och Spara. Starta om den virtuella datorn så att ändringarna börjar gälla.

![Uppdatera befintlig virtuell dator](./media/virtual-machines-common-boot-diagnostics/screenshot5.png)

