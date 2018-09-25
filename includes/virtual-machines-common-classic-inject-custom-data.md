


Den här artikeln beskrivs hur du:

* Mata in data i en Azure-dator (VM) när den har etablerats.
* Hämta den för både Windows och Linux.
* Använda särskilda verktyg som är tillgängliga i vissa system för att identifiera och hantera anpassade data automatiskt.

> [!NOTE]
> Den här artikeln beskrivs hur anpassade data kan införas med hjälp av en virtuell dator som skapats med den klassiska distributionsmodellen. Om du vill se hur du använder Azure Resource Management-API, se [exempel mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Infoga anpassade data i din Azure-dator

[!INCLUDE [classic-cli](contains-classic-cli-content.md)]

Den här funktionen stöds för närvarande bara i den [Azure CLI](https://github.com/Azure/azure-xplat-cli). Här kan vi skapa en `custom-data.txt` -fil som innehåller våra data sedan mata in det i till den virtuella datorn under etableringen. Även om du kan använda något av alternativen för den `azure vm create` kommandot i följande exempel visar en grundläggande metod:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Använda anpassade data i den virtuella datorn
* Om den virtuella Azure-datorn är en Windows-baserad virtuell dator och anpassade data-fil sparas på `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Även om det var base64-kodad Överför från den lokala datorn till den nya virtuella datorn, kan den är automatiskt avkodade och öppnas eller används omedelbart.
  
  > [!NOTE]
  > Om filen finns skrivs den över. Säkerhet på katalogen är inställd på **System: fullständig kontroll** och **administratörer: fullständig kontroll**.
  > 
  > 
* Om din virtuella Azure-datorn är en Linux-baserad virtuell dator, finns anpassade data-filen i någon av följande platser, beroende på din distribution. Data kan vara base64-kodad, så du kan behöva avkoda data först:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Cloud-init på Azure
Om din Azure VM från en Ubuntu eller CoreOS-avbildning, kan du använda CustomData för att skicka en molnkonfiguration till cloud-init. Eller om din anpassade data-fil är ett skript, sedan cloud-init kan köra den.

### <a name="ubuntu-cloud-images"></a>Ubuntu Molnbilder
I de flesta Azure Linux-avbildningar du redigerar ”/ etc/waagent.conf” att konfigurera en temporär disk och byta ut filen. Se [användarhandboken för Azure Linux Agent](../articles/virtual-machines/extensions/agent-linux.md) för mer information.

Dock på Ubuntu Cloud-avbildningar, måste du använda cloud-init konfigurera resursdisk (det vill säga ”tillfälliga” disk) och växla partition. Följande sida visas på Ubuntu wiki för mer information: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Nästa steg: använda cloud-init
Mer information finns i den [cloud-init-dokumentationen för Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Lägg till rollen Service Management REST API-referens](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure-kommandoradsgränssnittet](https://github.com/Azure/azure-xplat-cli)

