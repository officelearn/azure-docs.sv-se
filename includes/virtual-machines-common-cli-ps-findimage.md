

## <a name="azure-cli-20"></a>Azure CLI 2.0

När du har [installerat Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) kan du använda kommandot `az vm image list` för att visa en cachelagrad lista över populära VM-avbildningar. Följande exempel på kommandot `az vm image list -o table` visar:

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Söka efter alla befintliga avbildningar

Om du vill visa en aktuell lista över alla avbildningar använder du kommandot `az vm image list` med alternativet `--all`. Till skillnad från kommandona i Azure CLI 1.0 så returnerar kommandot `az vm image list --all` som standard alla avbildningar i **usavästra** (om du inte anger ett visst argument av typen `--location`), vilket betyder att kommandot `--all` kan ta lång tid att slutföra. Om du vill göra en interaktiv sökning använder du `az vm image list --all > allImages.json`, vilket returnerar en lista med alla avbildningar som för tillfället finns tillgängliga på Azure och lagrar den som en fil för användning lokalt. 

Du kan ange ett av flera alternativ för att begränsa sökningen till en viss plats, erbjudande, utgivare eller SKU. Om du inte anger en plats returneras värdena för **usavästra**.

### <a name="find-specific-images"></a>Söka efter specifika avbildningar

Använd `az vm image list` med ett [JMESPATH-frågefilter](https://docs.microsoft.com/cli/azure/query-az-cli2) för att hitta viss information. Nedanstående visar till exempel de **SKU:er** som är tillgängliga för **Debian** (kom ihåg att utan `--all`-växeln genomsöks endast den lokala cachen med vanliga avbildningar):

```azurecli
az vm image list --query '[?contains(offer,`Debian`)]' -o table --all
```

Resultatet ser ut ungefär så här: 
```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
  Sku  Publisher    Offer    Urn                       Version    UrnAlias
-----  -----------  -------  ------------------------  ---------  ----------
    8  credativ     Debian   credativ:Debian:8:latest  latest     Debian

<list shortened for the example>
```

Om du vet var distributionen finns kan du använda allmänna sökresultat tillsammans med kommandona `az vm image list-skus`, `az vm image list-offers` och `az vm image list-publishers` för att hitta exakt det du letar efter och ställen där det kan användas. Utgå från exemplet ovan och anta att du vet att `credativ` har ett erbjudande på Debian. Då kan du använda `--location` och andra alternativ för att hitta exakt det du söker. Använd följande exempel om du vill söka efter en Debian 8-avbildning i **europavästra**:

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

Utdata ser ut så här:

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0 

> [!NOTE]
> I den här artikeln får du veta hur du navigerar bland och väljer virtuella avbildningar med hjälp av en Azure CLI 1.0- eller Azure PowerShell-installation som stöder Azure Resource Manager-distributionsmodellen. Detta kräver dock att du byter till Resource Manager-läget. Om du använder Azure CLI byter du läge genom att skriva `azure config mode arm`. 
> 

Det snabbaste sättet att hitta en avbildning är att anropa kommandot `azure vm image list` och skicka platsen, utgivarens namn (ej skiftlägeskänsligt) och ett erbjudande – om du nu känner till erbjudandet. I följande lista utgör endast ett kort exempel – många listor kan vara rätt långa. Kanske vet du redan att ”Canonical” är utgivaren till erbjudandet ”UbuntuServer”.

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

Kolumnen **Urn** skickas till `azure vm quick-create`.

Men oftast vet du nog inte vad som finns tillgängligt. I det här fallet kan du navigera bland avbildningarna med hjälp av kommandot `azure vm image list-publishers` genom att ange en datacenterplats i prompten. Följande listar till exempel alla utgivare av avbildningar på platsen USA, västra (du skickar platsargumentet genom att använda gemener och ta bort blankstegen från standardplatserna).

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Listorna kan bli rätt långa, så exempellistan ovan är bara ett kort utdrag. Antag att du sett att Canonical är en utgivare av avbildningar på platsen USA, västra. Då kan du leta fram deras erbjudanden genom att anropa `azure vm image list-offers` och skicka platsen och utgivaren i prompten, som i följande exempel:

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

Nu vet vi att Canonical är utgivare till erbjudandet **UbuntuServer** i regionen USA, västra. Men vad finns det för SKU:er? För att visa sådana värden anropar du `azure vm image list-skus` och besvarar frågan med den plats, utgivare och det erbjudande som du har hittat.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Med den här informationen kan du nu hitta exakt den avbildning som du vill ha genom att anropa det ursprungliga anropet högst upp.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

Du kan nu välja exakt den avbildning som du vill använda. Du kan snabbt och smidigt skapa en virtuell dator med hjälp av URN-informationen som du letat reda på, eller använda en mall med URN-informationen. Mer information finns i [Använda Azure CLI för Mac, Linux och Windows med Azure Resource Manager](../articles/xplat-cli-azure-resource-manager.md).

## <a name="powershell"></a>PowerShell
> [!NOTE]
> Installera och konfigurera [den senaste versionen av Azure PowerShell](/powershell/azureps-cmdlets-docs). Om du använder Azure PowerShell-moduler tidigare än version 1.0 kan du fortfarande använda följande kommandon, men först måste du `Switch-AzureMode AzureResourceManager`. 
> 
> 

När du skapar en ny virtuell dator med Azure Resource Manager måste du i vissa fall ange en avbildning med följande kombination av egenskaper:

* Utgivare
* Erbjudande
* SKU

De här värdena behövs för till exempel `Set-AzureRMVMSourceImage` PowerShell-cmdleten eller en resursgruppmall där du måste ange vilken typ av virtuell dator som ska skapas.

Navigera bland avbildningarna för att fastställa värden:

1. Visa en lista över avbildningsutgivare.
2. Visa en lista över erbjudanden från en viss utgivare.
3. Visa en lista över SKU:er för ett visst erbjudande.

Börja med att skapa en lista över utgivare med hjälp av följande kommandon:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Fyll i namnet på den valda utgivaren och kör följande kommandon:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Fyll i namnet på det valda erbjudandet och kör följande kommandon:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

I visningen av kommandot `Get-AzureRMVMImageSku` har du all information du behöver för att ange avbildning till en ny virtuell dator.

Följande visar ett fullständigt exempel:

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

För utgivaren ”MicrosoftWindowsServer”:

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

För erbjudandet ”WindowsServer”:

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

Kopiera det valda SKU-namnet från listan så har du all information om `Set-AzureRMVMSourceImage` PowerShell-cmdleten eller om en resursgruppmall.

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/