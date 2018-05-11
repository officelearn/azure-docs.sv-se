Stöd för två felsöka funktioner är nu tillgängligt i Azure: konsolens utdata och skärmbild stöd för virtuella datorer i Azure Resource Manager-modellen. 

När en egen avbildning till Azure eller även starta en av avbildningarna plattform, kan det finnas många orsaker till varför en virtuell dator hämtar till tillståndet ej startbar. Dessa funktioner kan du enkelt diagnostisera och återställa dina virtuella datorer från startfel.

Du kan enkelt se utdata från konsolen loggen från portalen för Linux virtuella datorer:

![Azure Portal](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Men för både Windows och Linux virtuella datorer kan Azure du också se en skärmbild av den virtuella datorn från hypervisor-programmet:

![Fel](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Båda dessa funktioner stöds för virtuella Azure-datorer i alla regioner. Tänk på att det kan ta upp till 10 minuter innan skärmbilder och utdata visas på lagringskontot.

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
1. När du skapar en ny virtuell dator från Azure Portal väljer den **Azure Resource Manager** distribution modellen listrutan:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. I **inställningar**, aktivera den **starta diagnostik**, och välj sedan ett lagringskonto som du vill att dessa diagnostiska filerna.
 
    ![Skapa en virtuell dator](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Start-diagnostik har inte stöd för premium-lagringskontot. Om du använder premium storage-konto för startdiagnostikinställningar kan du får felmeddelandet StorageAccountTypeNotSupported när du startar den virtuella datorn.
    >
    > 

3. Om du distribuerar en Azure Resource Manager-mall, navigera till din virtuella datorresurser och Lägg till avsnittet diagnostics profil. Kom ihåg att använda API-versionsrubriken 2015-06-15.

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

Kolla våra lagringsplatsen här om du vill distribuera en virtuell dator i exemplet med startdiagnostikinställningar aktiverad.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Aktivera startdiagnostikinställningar på befintlig virtuell dator 

Följ dessa steg om du vill aktivera startdiagnostikinställningar på en befintlig virtuell dator:

1. Logga in på den [Azure-portalen](https://portal.azure.com), och välj sedan den virtuella datorn.
2. I **stöd + felsökning**väljer **starta diagnostik** > **inställningar**, ändra status till **på**, och sedan Välj ett lagringskonto. 
4. Kontrollera att alternativet Start diagnostik är markerad och spara ändringen.

    ![Uppdatera befintlig virtuell dator](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Starta om den virtuella datorn så att ändringarna börjar gälla.


