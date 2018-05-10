


Den här artikeln beskriver hur du:

* Mata in data i en Azure virtuell dator (VM) när den har etablerats.
* Hämta den för både Windows och Linux.
* Använd särskilda verktyg som finns på vissa datorer att identifiera och hantera anpassade data automatiskt.

> [!NOTE]
> Den här artikeln beskriver hur anpassade data kan matas in med hjälp av en virtuell dator som skapats med den klassiska distributionsmodellen. Information om hur du använder Azure Resource Management API finns [exempel mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Placera anpassade data i ditt virtuella Azure-datorn
Den här funktionen stöds för närvarande bara i den [Azure-kommandoradsgränssnittet](https://github.com/Azure/azure-xplat-cli). Här kan vi skapa en `custom-data.txt` -fil som innehåller våra data sedan mata in det i till den virtuella datorn under etableringen. Även om du kan använda något av alternativen för den `azure vm create` kommandot i följande exempel visar en enkel metod:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Med hjälp av anpassade data på den virtuella datorn
* Om din Azure VM är en Windows-baserad virtuell dator och sedan anpassade data sparas till `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Även om det var base64-kodad Överför från den lokala datorn till den nya virtuella datorn kan automatiskt avkodas och öppnas eller används omedelbart.
  
  > [!NOTE]
  > Om filen finns över den. Säkerheten för katalogen är inställd på **System: fullständig kontroll** och **administratörer: fullständig kontroll**.
  > 
  > 
* Om din Azure VM är en Linux-baserade Virtuella, finns anpassade data-filen på någon av följande platser beroende på din distro. Data kan vara base64-kodade, så du kan behöva avkryptering av data först:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Molnet initiering på Azure
Om din Azure VM är från en Ubuntu eller virtuell CoreOS-avbildning, kan du använda CustomData för att skicka en moln-config till molnet initiering. Eller om anpassade datafilen är ett skript, molnet init kan köra.

### <a name="ubuntu-cloud-images"></a>Ubuntu molnet bilder
I de flesta Azure Linux-avbildningar, skulle du redigera ”/ etc/waagent.conf” att konfigurera tillfällig disken och byta ut filen. Se [Azure Linux-agenten användarhandboken](../articles/virtual-machines/extensions/agent-linux.md) för mer information.

Dock på Ubuntu molnet bilder, måste du använda molnet init för att konfigurera resurs-disken (det vill säga ”tillfälliga” disk) och växla partition. Se följande sida i wiki Ubuntu mer information: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Nästa steg: med molnet initiering
Mer information finns i [moln init-dokumentationen för Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Lägg till rollen Service Management REST API-referens](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure-kommandoradsgränssnittet](https://github.com/Azure/azure-xplat-cli)

