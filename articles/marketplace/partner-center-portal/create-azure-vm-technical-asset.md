---
title: Skapa dina tekniska resurser för din virtuella azure-dator
description: Lär dig hur du skapar och konfigurerar tekniska resurser för ett virtuellt datorerbjudande (VM) för Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb885d0e965579b1ab2d66395f9f96eab0845bae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266505"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Skapa dina tekniska resurser för din virtuella azure-dator

> [!IMPORTANT]
> Vi flyttar hanteringen av dina Azure Virtual Machine-erbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [Skapa tekniska resurser för ett erbjudande om virtuella datorer för Cloud Partner Portal för](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) att hantera dina erbjudanden.

I den här artikeln beskrivs hur du skapar och konfigurerar tekniska resurser för ett vm-erbjudande (Virtual Machine) för Azure Marketplace. En virtuell dator innehåller två komponenter: operativsystemets virtuella hårddisk (VHD) och valfria associerade datadiskar virtuella hårddiskar:

* **Operativsystemets VIRTUELLA HÅRDDISK** – Innehåller operativsystemet och lösningen som distribueras med ditt erbjudande. Processen att förbereda den virtuella hårddisken skiljer sig åt beroende på om det är en Linux-baserad, Windows-baserad eller anpassad virtuell dator.
* **Virtuella hårddiskar för datadiskar** – dedikerad, beständig lagring för en virtuell dator. Använd inte operativsystemets virtuella hårddisk (till exempel C:-enheten) för att lagra beständig information.

En VM-avbildning innehåller en operativsystemdisk och upp till 16 datadiskar. Använd en virtuell hårddisk per datadisk, även om disken är tom.

> [!NOTE]
> Oavsett vilket operativsystem du använder lägger du bara till det minsta antal datadiskar som behövs av lösningen. Kunder kan inte ta bort diskar som ingår i en avbildning vid tidpunkten för distributionen, men de kan alltid lägga till diskar under eller efter distributionen.

> [!IMPORTANT]
> Varje VM-avbildning i en plan måste ha samma antal datadiskar.

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Designa, bygga och testa dessa tillgångar tar tid och kräver teknisk kunskap om både Azure-plattformen och de tekniker som används för att skapa erbjudandet. Förutom din lösningsdomän bör ditt ingenjörsteam ha kunskap om följande Microsoft-tekniker:

* Grundläggande förståelse för [Azure Services](https://azure.microsoft.com/services/)
* Så här [utformar och arkitekt azure-program utformas](https://azure.microsoft.com/solutions/architecture/)
* Kunskap om [virtuella Azure-datorer,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
* Kunskap om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Arbetskunskap om [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Föreslagna verktyg – tillval

Överväg att använda någon av följande skriptmiljöer för att hantera virtuella datorer och virtuella hårddiskar:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

Överväg dessutom att lägga till följande verktyg i utvecklingsmiljön:

* [Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio-koden](https://code.visualstudio.com/)
  * Tillägg: [Verktyg för Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Förlängning: [Försköna](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Förlängning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Granska de tillgängliga verktygen på sidan [Azure Developer Tools](https://azure.microsoft.com/product-categories/developer-tools/) och, om du använder Visual Studio, Visual Studio [Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Skapa en VM-avbildning med en godkänd bas

> [!NOTE]
> Om du vill skapa tekniska resurser för virtuella datorer med hjälp av en avbildning som du har byggt på din egen lokal går du till [Skapa en virtuell dator med din egen avbildning](#create-a-vm-using-your-own-image).

I det här avsnittet beskrivs olika aspekter av hur du använder en godkänd bas, till exempel att använda RDP (Remote Desktop Protocol), välja en storlek för den virtuella datorn, installera de senaste Windows-uppdateringarna och generalisera VHD-avbildningen.

Följande avsnitt fokuserar främst på windows-baserade virtuella hårddiskar. Mer information om hur du skapar Linux-baserade virtuella hårddiskar finns i [Linux på distributioner som stöds av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Följ anvisningarna i det här avsnittet om du vill använda Azure för att skapa en virtuell dator som innehåller ett förkonfigurerat, godkänt operativsystem. Om detta inte är kompatibelt med din lösning är det möjligt att skapa och konfigurera en lokal virtuell dator med hjälp av ett godkänt operativsystem. Du kan sedan konfigurera och förbereda den för överföring enligt beskrivningen i [Förbered en Windows VHD eller VHDX för att ladda upp till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Välj en godkänd bas

Välj antingen Windows operativsystem eller Linux som bas.

#### <a name="windows"></a>Windows

Operativsystemet VHD för din Windows-baserade VM-avbildning måste baseras på en Azure-godkänd basavbildning som innehåller Windows Server eller SQL Server. Börja med att skapa en virtuell dator från en av följande avbildningar från Azure-portalen:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Företag, Standard, Webb)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Företag, Standard, Webb)

> [!NOTE]
> Om du använder den aktuella Azure-portalen eller Azure PowerShell godkänns Windows Server-avbildningar som publicerades den 8 september 2014 och senare.

#### <a name="linux"></a>Linux

Azure erbjuder en rad godkända Linux-distributioner. En aktuell lista finns i [Linux på distributioner som stöds av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Skapa virtuell dator i Azure-portalen

Följ dessa steg för att skapa grundläggande VM-avbildningen i [Azure-portalen:](https://ms.portal.azure.com/)

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/) med Det Microsoft-konto som är kopplat till den Azure-prenumeration som du vill använda för att publicera ditt VM-erbjudande.
2. Skapa en ny resursgrupp och ange **resursgruppsnamn,** **prenumeration**och **resursgruppsplats**. Mer information finns i [Hantera resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Välj **Virtuella datorer** till vänster om du vill visa informationssidan för virtuella datorer.
4. Välj **+ Lägg till** om du vill öppna upplevelsen Skapa en virtuell **dator**.
5. Välj bilden i listrutan eller klicka på **Bläddra bland alla offentliga och privata bilder** för att söka eller bläddra bland alla tillgängliga bilder på virtuella datorer.
6. Välj storleken på den virtuella datorn som ska distribueras med hjälp av följande rekommendationer:
    * Om du planerar att utveckla den virtuella hårddisken lokalt, storleken spelar ingen roll. Överväg att använda en av de mindre virtuella datorerna.
    * Om du planerar att utveckla avbildningen i Azure kan du överväga att använda en av de rekommenderade vm-storlekarna för den valda avbildningen.

7. Expandera avsnittet **Avancerat** i avsnittet **Diskar** och ange alternativet **Använd hanterade diskar** till **Nej**.
8. Ange annan information som krävs för att skapa den virtuella datorn.
9. Välj **Granska + skapa** för att granska dina val. När meddelandet **Validering har skickats** väljer du **Skapa**.

Azure börjar etablera den virtuella datorn som du angav. Du kan spåra dess förlopp genom att välja fliken **Virtuella datorer** till vänster. När den har skapats ändras statusen till **Kör**.

Om du har problem med att skapa din nya Azure-baserade virtuella hårddisk läser du [Vanliga problem när du skapar virtuella hårddiskar](https://aka.ms/VHDcreationIssues).

### <a name="connect-to-your-azure-vm"></a>Anslut till din virtuella Azure-dator

I det här avsnittet beskrivs hur du ansluter till och loggar in på den virtuella datorn som du skapade på Azure. När du har anslutit kan du arbeta med den virtuella datorn som om du var lokalt inloggad på värdservern.

#### <a name="connect-to-a-windows-based-vm"></a>Ansluta till en Windows-baserad virtuell dator

Använd fjärrskrivbordsklienten för att ansluta till den Windows-baserade virtuella datorn som finns på Azure. De flesta versioner av Windows innehåller internt stöd för fjärrskrivbordsprotokollet (RDP). För andra operativsystem hittar du mer information om klienter i [fjärrskrivbordsklienter](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

I den hÃ¤r artikeln beskrivs hur du anvÃ¤nder det inbyggda Windows RDP-stödet för att ansluta till din virtuella dator: [AnvÃ¤nda och logga in pÃ¤ en virtuell Azure-dator med Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Du kan få säkerhetsvarningar under processen. Varningar som "Rdp-filen kommer till exempel från en okänd utgivare" eller "Dina användaruppgifter kan inte verifieras". Det är säkert att ignorera dessa varningar.

#### <a name="connect-to-a-linux-based-vm"></a>Anslut till en Linux-baserad virtuell dator

Om du vill ansluta till en Linux-baserad virtuell dator behöver du en SSH-klient (Secure Shell Protocol). Följande steg använder den kostnadsfria [PuTTY](https://www.ssh.com/ssh/putty/) SHH-terminalen.

1. Gå till [Azure-portalen](https://ms.portal.azure.com/).
2. Sök efter och välj **Virtuella datorer**.
3. Välj den virtuella dator som du vill ansluta till.
4. Starta den virtuella datorn om den inte redan körs.
5. Välj namnet på den virtuella datorn för att öppna sidan **Översikt.**
6. Observera den offentliga IP-adressen och DNS-namnet på den virtuella datorn (om dessa värden inte är inställda måste du [skapa ett nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Öppna PuTTY-programmet.
8. I dialogrutan PuTTY-konfiguration anger du IP-adressen eller DNS-namnet på den virtuella datorn.

    :::image type="content" source="media/avm-putty.png" alt-text="Illustrerar PuTTY-terminalinställningarna. Värdnamns- eller IP-adressen och portrutorna är markerade.":::

9. Välj **Öppna** för att öppna en PuTTY-terminal.
10. Ange kontonamnet och lösenordet för ditt Linux VM-konto när du uppmanas att göra det.

Om du har anslutningsproblem läser du dokumentationen för SSH-klienten. [Kapitel 10: Vanliga felmeddelanden](https://www.ssh.com/ssh/putty/putty-manuals).

Mer information, inklusive hur du lägger till ett skrivbord i en etablerad Virtuell Linux-dator, finns i [Installera och konfigurera Fjärrskrivbord för att ansluta till en Virtuell Linux-dator i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Skapa en virtuell dator med din egen avbildning

I det här avsnittet beskrivs hur du skapar och distribuerar en virtuell datoravbildning (User Provided Virtual Machine). Du kan göra detta genom att tillhandahålla VHD-avbildningar för operativsystem och datadisk från en Azure-distribuerad virtuell hårddisk (VHD).

> [!NOTE]
> Om du vill använda en godkänd basavbildning kan du följa instruktionerna i [Skapa en vm-avbildning med en godkänd bas](#create-a-vm-image-using-an-approved-base).

1. Ladda upp dina avbildningar till Azure Storage-konto.
2. Distribuera vm-avbildningen.
3. Fånga vm-avbildningen.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Ladda upp dina avbildningar till ett Azure-lagringskonto

1. Logga in på [Azure portal](https://portal.azure.com/).
2. Ladda upp ditt allmänna hårddisksystem vhd och datadiskvd till ditt Azure-lagringskonto.

### <a name="deploy-your-image"></a>Distribuera avbildningen

Skapa avbildningen med antingen Azure-portalen eller Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Distribuera med hjälp av Azure-portalen

1. På startsidan väljer du **Skapa en resurs**, söker efter "Malldistribution" och väljer **Skapa**.
2. Välj **Skapa en egen mall i redigeraren**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Illustrerar sidan Anpassad distribution.":::

3. Klistra in den här [JSON-mallen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) i redigeraren och välj **Spara**.
4. Ange parametervärdena för de **anpassade distributionsegenskapssidorna.**

    | Parameter | Beskrivning |
    | ------------ | ------------- |
    | Namn på användarkonto för användare | Innehåll från cell 2 |
    | Behållarens namn för användarlagring | Namn på lagringskonto där den allmänna virtuella hårddisken finns |
    | DNS-namn för offentlig IP | Offentligt IP DNS-namn. Definiera DNS-namnet för den offentliga IP-adressen i Azure-portalen när erbjudandet har distribuerats. |
    | Administratörsanvändarnamn | Administratörskontots användarnamn för ny virtuell dator |
    | Adminlösenord | Administratörskontots lösenord för ny virtuell dator |
    | OS-typ | VM-operativsystem: Windows eller Linux |
    | Prenumerations-ID:t | Identifierare för den valda prenumerationen |
    | Location | Geografisk plats för distributionen |
    | Storlek på virtuell dator | [Azure VM-storlek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), till exempel Standard_A2 |
    | Namn på offentlig IP-adress | Namn på din offentliga IP-adress |
    | Namn på virtuell dator | Namnet på den nya virtuella datorn |
    | Namn på virtuellt nätverk | Namn på det virtuella nätverk som används av den virtuella datorn |
    | Nätverkskortsnamn | Namn på nätverkskortet som kör det virtuella nätverket |
    | VHD URL | Fullständig VHD-URL för OS-disk |
    |  |  |

5. När du har anger dessa värden väljer du **Köp**.

Azure börjar distributionen. Den skapar en ny virtuell dator med den angivna obearmanna vhd-hårddisken i den angivna sökvägen till lagringskontot. Du kan spåra förloppet i Azure-portalen genom att välja **virtuella datorer** till vänster i portalen. När den virtuella datorn skapas ändras statusen från Start till Löpning.

#### <a name="deploy-using-azure-powershell"></a>Distribuera med hjälp av Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Ta bilden av den virtuella datorn

Använd följande instruktioner som motsvarar din metod:

* Azure PowerShell: [Skapa en ohanterad VM-avbildning från en virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI: [Så här skapar du en avbildning av en virtuell dator eller virtuell hårddisk](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Virtuella datorer - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Konfigurera den virtuella datorn

I det här avsnittet beskrivs hur du ändrar storlek, uppdaterar och generaliserar en Azure-virtuell dator. Dessa steg är nödvändiga för att förbereda din virtuella dator som ska distribueras på Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimensionering av virtuella hårddiskar

Om du har valt en av de virtuella datorerna som är förkonfigurerade med ett operativsystem (och eventuellt ytterligare tjänster) har du redan valt en standardstorlek för Azure VM. Att starta din lösning med ett förkonfigurerat operativsystem är den rekommenderade metoden. Om du installerar ett operativsystem manuellt måste du dock ändra storleken på den primära virtuella hårddisken i avbildningen för den virtuella datorn:

* För Windows bör operativsystemet VHD skapas som en 127–128 GB virtuell hårddisk med fast format.
* För Linux bör den här virtuella hårddisken skapas som en virtuell hårddisk med 30–50 GB i fast format.

Om den fysiska storleken är mindre än 127–128 GB ska den virtuella hårddisken vara utbyggbar (gles/dynamisk). De grundläggande Windows- och SQL Server-avbildningar som redan finns uppfyller dessa krav, så ändra inte formatet eller storleken på den virtuella hårddisken.

Datadiskar kan vara så stora som 1 TB. När du bestämmer storlek bör du komma ihåg att kunder inte kan ändra storlek på virtuella hårddiskar i en avbildning vid tidpunkten för distributionen. Virtuella hårddiskar med datadiskar ska skapas som virtuella hårddiskar i fast format. De bör också vara utbyggbara (glesa/dynamiska). Datadiskar kan inledningsvis vara tomma eller innehålla data.

### <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

Basavbildningarna för virtuella datorer i operativsystemet måste innehålla de senaste uppdateringarna fram till det publicerade datumet. Innan du publicerar operativsystemets VIRTUELLA HÅRDDISK som du skapade måste du uppdatera operativsystemet och alla installerade tjänster med alla de senaste säkerhets- och underhållskorrigeringarna.

För Windows Server kör du kommandot **Sök efter uppdateringar.**

För Linux-distributioner hämtas och installeras uppdateringar ofta via ett kommandoradsverktyg eller ett grafiskt verktyg. Ubuntu Linux tillhandahåller till exempel kommandot [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) och [uppdateringshanterarens](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) verktyg för uppdatering av operativsystemet.

### <a name="perform-additional-security-checks"></a>Utföra ytterligare säkerhetskontroller

Upprätthålla en hög säkerhetsnivå för dina lösningsavbildningar på Azure Marketplace. Följande artikel innehåller en checklista över säkerhetskonfigurationer och säkerhetsprocedurer som hjälper dig: [Säkerhetsrekommendationer för Azure Marketplace-avbildningar](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Några av dessa rekommendationer är specifika för Linux-baserade avbildningar, men de flesta gäller för alla VM-avbildningar.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utföra anpassade konfigurations- och schemalagda aktiviteter

Om ytterligare konfiguration behövs använder du en schemalagd aktivitet som körs vid start för att göra eventuella slutliga ändringar i den virtuella datorn när den har distribuerats. Tänk också på följande rekommendationer:

* Om det är en run-once-aktivitet bör aktiviteten ta bort sig själv när den har slutförts.
* Konfigurationer bör inte förlita sig på andra enheter än C eller D, eftersom endast dessa två enheter alltid garanteras att existera (enhet C är operativsystemdisken och enhet D är den tillfälliga lokala disken).

Mer information om Linux-anpassningar finns i [Tillägg och funktioner för virtuella datorer för Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalisera bilden

Alla avbildningar på Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå detta måste operativsystemets virtuella dator vara generaliserad, en åtgärd som tar bort alla instansspecifika identifierare och programvarudrivrutiner från en virtuell dator.

### <a name="windows"></a>Windows

Windows OS-diskar är generaliserade med [sysprep-verktyget](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Om du sedan uppdaterar eller konfigurerar om operativsystemet måste du köra om sysprep.

> [!WARNING]
> Eftersom uppdateringar kan köras automatiskt, när du har kört sysprep, stänger du av den virtuella datorn tills den har distribuerats. Den här avstängningen undviker efterföljande uppdateringar från att göra instansspecifika ändringar i operativsystemet eller installerade tjänster. Mer information om hur du kör sysprep finns i [Steg för att generalisera en virtuell hårddisk](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Följande process generaliserar en Virtuell Linux-dator och distribuerar om den som en separat virtuell dator. Mer information finns i [Så här skapar du en bild av en virtuell dator eller virtuell hårddisk](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Du kan sluta när du når avsnittet "Skapa en virtuell dator från den tagna avbildningen".

1. **Ta bort Azure Linux-agenten**

    1. Anslut till din virtuella Linux-dator med en SSH-klient.
    2. I SSH-fönstret anger du `sudo waagent -deprovision+user`följande kommando: .
    3. Skriv **Y** för att fortsätta (du kan lägga till **-force-parametern** i föregående kommando för att undvika bekräftelsesteget).
    d. När kommandot är klart skriver du **Avsluta** för att stänga SSH-klienten.

2. **Stoppa en virtuell dator**

    1. I Azure-portalen väljer du din resursgrupp (RG) och avallokerar den virtuella datorn.
    2. Din virtuella hårddisk är nu generaliserad och du kan skapa en ny virtuell dator med den här virtuella hårddisken.

## <a name="next-steps"></a>Nästa steg

Om du har haft problem med att skapa din nya Azure-baserade virtuella hårddisk läser du [Vanliga problem när den virtuella hårddisken skapas](https://aka.ms/AzureVM_VHDCreationFAQ).

I annat fall:

* [Certifiera din VM-avbildning](https://aks.ms/CertifyVMimage) förklarar hur du testar och skickar in en VM-avbildning för Azure Marketplace-certifiering, inklusive var du kan hämta *verktyget Certifieringstestverktyg för Azure Certified* och hur du använder det för att certifiera din VM-avbildning.
