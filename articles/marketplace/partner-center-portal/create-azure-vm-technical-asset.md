---
title: Skapa tekniska till gångar för din virtuella Azure-dator
description: Lär dig hur du skapar och konfigurerar tekniska till gångar för ett erbjudande för virtuella datorer (VM) för Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 4d2d33f9d83132147b5b257ffcd6d659f272b8ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730724"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Skapa tekniska till gångar för din virtuella Azure-dator

> [!IMPORTANT]
> Vi flyttar hanteringen av din virtuella Azure-dator med erbjudanden från Cloud Partner Portal till Partner Center. Följ anvisningarna i [skapa tekniska till gångar för ett virtuellt dator erbjudande för Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) att hantera dina erbjudanden innan du migrerar dina erbjudanden.

Den här artikeln beskriver hur du skapar och konfigurerar tekniska till gångar för ett erbjudande för virtuella datorer (VM) för Azure Marketplace. En virtuell dator innehåller två komponenter: virtuella hård diskar (VHD) för operativ system och valfria associerade data diskar för virtuella hård diskar:

* **Operativ systemets VHD** – innehåller det operativ system och den lösning som distribueras med ditt erbjudande. Processen för att förbereda den virtuella hård disken varierar beroende på om det är en Linux-baserad, Windows-baserad eller anpassad-baserad virtuell dator.
* **Data diskar VHD** -dedikerad, beständig lagring för en virtuell dator. Använd inte den virtuella hård disken (t. ex. C: Drive) för att lagra beständig information.

En avbildning av en virtuell dator innehåller en operativ system disk och upp till 16 data diskar. Använd en virtuell hård disk per data disk, även om disken är tom.

> [!NOTE]
> Oavsett vilket operativ system du använder lägger du endast till det minsta antalet data diskar som behövs för lösningen. Kunder kan inte ta bort diskar som ingår i en avbildning vid tidpunkten för distributionen, men de kan alltid lägga till diskar under eller efter distributionen.

> [!IMPORTANT]
> Varje VM-avbildning i en plan måste ha samma antal data diskar.

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet. Förutom din lösnings domän bör ditt tekniska team ha kunskap om följande Microsoft-tekniker:

* Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/)
* Så här [utformar och skapar du Azure-program](https://azure.microsoft.com/solutions/architecture/)
* Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking)
* Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Arbeta med kunskaper om [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Rekommenderade verktyg – valfritt

Överväg att använda någon av följande skript miljöer för att hantera virtuella datorer och virtuella hård diskar:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

Överväg dessutom att lägga till följande verktyg i utvecklings miljön:

* [Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio-koden](https://code.visualstudio.com/)
  * Tillägg: [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Tillägg: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Tillägg: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Granska de tillgängliga verktygen på [Azure utvecklarverktyg](https://azure.microsoft.com/product-categories/developer-tools/) -sidan och, om du använder Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Skapa en VM-avbildning med en godkänd bas

> [!NOTE]
> Om du vill skapa tekniska till gångar för virtuella datorer med hjälp av en avbildning som du skapat på dina egna anläggningar går du till [skapa en virtuell dator med hjälp av en egen avbildning](#create-a-vm-using-your-own-image).

I det här avsnittet beskrivs olika aspekter av att använda en godkänd bas, till exempel att använda Remote Desktop Protocol (RDP), välja en storlek för den virtuella datorn, installera de senaste Windows-uppdateringarna och generalisera VHD-avbildningen.

Följande avsnitt fokuserar främst på Windows-baserade virtuella hård diskar. Mer information om hur du skapar Linux-baserade virtuella hård diskar finns i [Linux on distributioner som har godkänts av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Följ anvisningarna i det här avsnittet för att använda Azure för att skapa en virtuell dator som innehåller ett förkonfigurerat, godkänt operativ system. Om detta inte är kompatibelt med din lösning, är det möjligt att skapa och konfigurera en lokal virtuell dator med ett godkänt operativ system. Du kan sedan konfigurera och förbereda den för uppladdning enligt beskrivningen i [förbereda en virtuell Windows-VHD eller VHDX för att ladda upp till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Välj en godkänd bas

Välj antingen Windows operativ system eller Linux som bas.

#### <a name="windows"></a>Windows

Operativ systemets virtuella hård disk för en Windows-baserad avbildning av virtuella datorer måste baseras på en Azure-godkänd bas avbildning som innehåller Windows Server eller SQL Server. Börja med att skapa en virtuell dator från någon av följande avbildningar från Azure Portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, Web)

> [!NOTE]
> Om du använder den nuvarande Azure Portal eller Azure PowerShell, kommer Windows Server-avbildningar som publicerats den 8 september 2014 och senare att godkännas.

#### <a name="linux"></a>Linux

Azure erbjuder ett antal godkända Linux-distributioner. En aktuell lista finns i [Linux on distributioner som har godkänts av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Skapa en virtuell dator i Azure Portal

Följ de här stegen för att skapa en bas avbildning av virtuella datorer i [Azure Portal](https://ms.portal.azure.com/):

1. Logga in på [Azure Portal](https://ms.portal.azure.com/) med det Microsoft-konto som är associerat med den Azure-prenumeration som du vill använda för att publicera ditt virtuella dator erbjudande.
2. Skapa en ny resurs grupp och ange **resurs gruppens namn**, **prenumeration**och **plats för resurs gruppen**. Mer information finns i [Hantera resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Välj **Virtual Machines** till vänster för att visa sidan med information om virtuella datorer.
4. Välj **+ Lägg** till för att öppna **upplevelsen skapa en virtuell dator**.
5. Välj avbildningen i den nedrullningsbara listan eller klicka på **Bläddra alla offentliga och privata avbildningar** för att söka eller bläddra bland alla tillgängliga avbildningar av virtuella datorer.
6. Välj storleken på den virtuella dator som ska distribueras med följande rekommendationer:
    * Storleken spelar ingen roll om du planerar att utveckla den virtuella hård disken lokalt. Överväg att använda en av de mindre virtuella datorerna.
    * Om du planerar att utveckla avbildningen i Azure bör du överväga att använda en av de rekommenderade VM-storlekarna för den valda avbildningen.

7. I avsnittet **diskar** expanderar du avsnittet **Avancerat** och anger alternativet **Använd Managed disks** till **Nej**.
8. Ange övrig information som krävs för att skapa den virtuella datorn.
9. Välj **Granska + skapa** för att granska dina val. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

Azure börjar etablering av den virtuella dator som du har angett. Du kan följa förloppet genom att välja fliken **Virtual Machines** till vänster. När den har skapats ändras statusen till **körs**.

Om du får problem med att skapa en ny Azure-baserad virtuell hård disk kan du läsa [vanliga problem när du skapar en virtuell hård disk (FAQ)](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation).

### <a name="connect-to-your-azure-vm"></a>Anslut till din virtuella Azure-dator

I det här avsnittet beskrivs hur du ansluter till och loggar in på den virtuella datorn som du skapade i Azure. När du har anslutit kan du arbeta med den virtuella datorn som om du var inloggad lokalt på värd servern.

#### <a name="connect-to-a-windows-based-vm"></a>Ansluta till en Windows-baserad virtuell dator

Använd fjärr skrivbords klienten för att ansluta till den Windows-baserade virtuella datorn som finns på Azure. De flesta versioner av Windows innehåller inbyggt stöd för Remote Desktop Protocol (RDP). För andra operativ system kan du hitta mer information om klienter i [fjärr skrivbords klienter](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Den här artikeln beskriver hur du använder det inbyggda Windows RDP-stödet för att ansluta till din virtuella dator: [hur du ansluter och loggar in på en virtuell Azure-dator som kör Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Du kan få säkerhets varningar under processen. Exempel: varningar som "RDP-filen kommer från en okänd utgivare" eller "dina användarautentiseringsuppgifter kan inte verifieras". Det är säkert att ignorera dessa varningar.

#### <a name="connect-to-a-linux-based-vm"></a>Ansluta till en Linux-baserad virtuell dator

Om du vill ansluta till en Linux-baserad virtuell dator behöver du en SSH-klient (Secure Shell Protocol). I följande steg används den kostnads fria SHH-terminalen för [SparaTillFil](https://www.ssh.com/ssh/putty/) .

1. Gå till [Azure Portal](https://ms.portal.azure.com/).
2. Sök efter och välj **virtuella datorer**.
3. Välj den virtuella dator som du vill ansluta till.
4. Starta den virtuella datorn om den inte redan körs.
5. Välj namnet på den virtuella datorn för att öppna dess **översikts** sida.
6. Anteckna den offentliga IP-adressen och DNS-namnet på den virtuella datorn (om dessa värden inte har angetts måste du [skapa ett nätverks gränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Öppna programmet SparaTillFil.
8. Ange IP-adressen eller DNS-namnet på den virtuella datorn i dialog rutan SparaTillFil-konfiguration.

    :::image type="content" source="media/avm-putty.png" alt-text="Visar Terminal-inställningarna för SparaTillFil. rutorna värd namn eller IP-adress och port markeras.":::

9. Välj **Öppna** för att öppna en SparaTillFil-Terminal.
10. När du uppmanas till det anger du konto namnet och lösen ordet för ditt Linux VM-konto.

Om du har anslutnings problem kan du läsa dokumentationen för SSH-klienten. Till exempel [kapitel 10: vanliga fel meddelanden](https://www.ssh.com/ssh/putty/putty-manuals).

Mer information, inklusive hur du lägger till ett skriv bord till en etablerad virtuell Linux-dator finns i [Installera och konfigurera fjärr skrivbord för att ansluta till en virtuell Linux-dator i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Skapa en virtuell dator med en egen avbildning

I det här avsnittet beskrivs hur du skapar och distribuerar en användardefinierad avbildning av en virtuell dator (VM). Du kan göra detta genom att tillhandahålla VHD-avbildningar för operativ system och data diskar från en Azure-distribuerad virtuell hård disk (VHD).

> [!NOTE]
> Om du vill kan du använda en godkänd bas avbildning genom att följa anvisningarna i [skapa en VM-avbildning med en godkänd bas](#create-a-vm-image-using-an-approved-base).

1. Ladda upp dina avbildningar till Azure Storage-kontot.
2. Distribuera avbildningen av den virtuella datorn.
3. Avbilda avbildningen av den virtuella datorn.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Ladda upp dina avbildningar till ett Azure Storage-konto

1. Logga in på [Azure portal](https://portal.azure.com/).
2. Överför dina generaliserade operativ system diskar och datadisk-VHD: ar till ditt Azure Storage-konto.

### <a name="deploy-your-image"></a>Distribuera din avbildning

Skapa din avbildning med antingen Azure Portal eller Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Distribuera med hjälp av Azure-portalen

1. På sidan start väljer du **skapa en resurs**, söker efter "mall distribution" och väljer **skapa**.
2. Välj **Bygg en egen mall i redigeraren**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Visar den anpassade distributions sidan.":::

3. Klistra in denna [JSON-mall](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) i redigeraren och välj **Spara**.
4. Ange parameter värden för de visade egenskaps sidorna för **Anpassad distribution** .

    | Parameter | Beskrivning |
    | ------------ | ------------- |
    | Konto namn för användar lagring | Innehåll från cell 2 |
    | Namn på användar lagrings behållare | Lagrings konto namn där den generaliserade virtuella hård disken finns |
    | DNS-namn för offentlig IP | DNS-namn för offentlig IP. Definiera DNS-namnet för den offentliga IP-adressen i Azure Portal när erbjudandet har distribuerats. |
    | Administratörens användar namn | Administratörs kontots användar namn för ny virtuell dator |
    | Adminlösenord | Administratörs kontots lösen ord för ny virtuell dator |
    | OS-typ | Operativ system för virtuell dator: Windows eller Linux |
    | Prenumerations-ID:t | Identifierare för den valda prenumerationen |
    | Plats | Distributionens geografiska plats |
    | Storlek på virtuell dator | [Storlek på virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), till exempel Standard_A2 |
    | Namn på offentlig IP-adress | Namn på din offentliga IP-adress |
    | Namn på virtuell dator | Namn på den nya virtuella datorn |
    | Virtual Network namn | Namnet på det virtuella nätverk som används av den virtuella datorn |
    | NIC-namn | Namnet på det nätverks gränssnitts kort som kör det virtuella nätverket |
    | VHD-URL | Fullständigt VHD-URL för OS-disk |
    |  |  |

5. När du har angett dessa värden väljer du **köp**.

Azure kommer att börja distribuera. Den skapar en ny virtuell dator med den angivna ohanterade virtuella hård disken i den angivna sökvägen för lagrings kontot. Du kan följa förloppet i Azure Portal genom att välja **Virtual Machines** till vänster i portalen. När den virtuella datorn skapas kommer statusen att ändras från att börja köras.

#### <a name="deploy-using-azure-powershell"></a>Distribuera med hjälp av Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Avbilda avbildningen av den virtuella datorn

Använd följande instruktioner som motsvarar din metod:

* Azure PowerShell: [så här skapar du en ohanterad virtuell dator avbildning från en virtuell Azure-dator](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI: [så här skapar du en avbildning av en virtuell dator eller virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Virtual Machines-Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Konfigurera den virtuella datorn

I det här avsnittet beskrivs hur du ändrar storlek, uppdaterar och generaliserar en virtuell Azure-dator. Dessa steg är nödvändiga för att förbereda din virtuella dator så att den distribueras på Azure Marketplace.

### <a name="sizing-the-vhds"></a>Ändra storlek på de virtuella hård diskarna

Om du har valt en av de virtuella datorer som är förkonfigurerade med ett operativ system (och eventuellt ytterligare tjänster) har du redan valt en standard storlek för virtuella Azure-datorer. Den rekommenderade metoden är att starta din lösning med ett förkonfigurerat operativ system. Om du installerar ett operativ system manuellt måste du dock ändra den primära virtuella hård disken i den virtuella dator avbildningen:

* För Windows bör den virtuella hård disken för operativ systemet skapas som en 127 – 128 GB fast format-VHD.
* För Linux bör denna virtuella hård disk skapas som en 30 – 50 GB fast format-VHD.

Om den fysiska storleken är mindre än 127 – 128 GB ska den virtuella hård disken vara utbyggbar (sparse/dynamisk). De grundläggande Windows-och SQL Server avbildningarna uppfyller redan dessa krav, så ändra inte formatet eller storleken på den virtuella hård disken.

Data diskar kan vara så stora som 1 TB. Kom ihåg att kunder inte kan ändra storlek på VHD: er i en avbildning vid tidpunkten för distributionen när de väljer storlek. Datadisk-VHD: er ska skapas som virtuella hård diskar med fast format. De bör också vara expanderbara (glesa/dynamiska). Data diskar kan inlednings vis vara tomma eller innehålla data.

### <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

De grundläggande avbildningarna av virtuella operativ system måste innehålla de senaste uppdateringarna upp till publicerings datumet. Innan du publicerar den virtuella hård disk som du skapade måste du uppdatera operativ systemet och alla installerade tjänster med de senaste säkerhets-och underhålls korrigeringarna.

För Windows Server kör du kommandot **Sök efter uppdateringar** .

För Linux-distributioner hämtas och installeras uppdateringar ofta med hjälp av ett kommando rads verktyg eller grafiskt verktyg. Till exempel tillhandahåller Ubuntu Linux [apt-get-](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) kommandot och [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) -verktyget för att uppdatera operativ systemet.

### <a name="perform-additional-security-checks"></a>Utför ytterligare säkerhets kontroller

Upprätthålla en hög säkerhets nivå för dina lösnings avbildningar på Azure Marketplace. Följande artikel innehåller en check lista över säkerhetskonfigurationer och procedurer som hjälper dig att: [säkerhets rekommendationer för Azure Marketplace-avbildningar](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Några av de här rekommendationerna är speciella för Linux-baserade avbildningar, men de flesta gäller för alla VM-avbildningar.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utför anpassad konfiguration och schemalagda aktiviteter

Om ytterligare konfiguration behövs använder du en schemalagd aktivitet som körs vid start för att göra eventuella slutliga ändringar i den virtuella datorn när den har distribuerats. Tänk också på följande rekommendationer:

* Om det är en körnings aktivitet ska aktiviteten Ta bort sig själv när den har slutförts.
* Konfigurationer bör inte förlita sig på andra enheter än C eller D, eftersom endast dessa två enheter alltid garanterar att de finns (enhet C är operativ system disken och enhet D är den temporära lokala disken).

Mer information om Linux-anpassningar finns i [tillägg och funktioner för virtuella datorer för Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalisera avbildningen

Alla avbildningar på Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå detta måste den virtuella hård disken generaliseras, en åtgärd som tar bort alla instans-/regionsspecifika identifierare och program driv rutiner från en virtuell dator.

### <a name="windows"></a>Windows

Windows OS-diskar generaliseras med [Sysprep-verktyget](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Om du senare uppdaterar eller omkonfigurerar operativ systemet måste du köra Sysprep igen.

> [!WARNING]
> Eftersom uppdateringar kan köras automatiskt när du har kört Sysprep stänger du av den virtuella datorn tills den har distribuerats. Den här avstängningen kommer att undvika efterföljande uppdateringar från att göra instansbaserade ändringar i operativ systemet eller de installerade tjänsterna. Mer information om hur du kör Sysprep finns i [steg för att generalisera en virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Följande process generaliserar en virtuell Linux-dator och distribuerar den igen som en separat virtuell dator. Mer information finns i [så här skapar du en avbildning av en virtuell dator eller virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Du kan stoppa när du når avsnittet "skapa en virtuell dator från avbildningen".

1. **Ta bort Azure Linux-agenten**

    1. Anslut till din virtuella Linux-dator med hjälp av en SSH-klient.
    2. I SSH-fönstret anger du följande kommando: `sudo waagent -deprovision+user`.
    3. Skriv in **Y** om du vill fortsätta (du kan lägga till parametern **-Force** i föregående kommando för att undvika bekräftelse steget).
    d. När kommandot har slutförts skriver du **exit** för att stänga SSH-klienten.

2. **Stoppa en virtuell dator**

    1. I Azure Portal väljer du resurs gruppen (RG) och avallokerar den virtuella datorn.
    2. Din virtuella hård disk är nu generaliserad och du kan skapa en ny virtuell dator med den här virtuella hård disken.

## <a name="next-steps"></a>Nästa steg

Om du har problem med att skapa din nya Azure-baserade virtuella hård disk kan du läsa [vanliga problem när du skapar en virtuell hård disk](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues).

I annat fall:

* [Certifiera din VM-avbildning](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) förklarar hur du testar och skickar en VM-avbildning för Azure Marketplace-certifiering, inklusive var du hämtar *certifierings test verktyget för Azure Certified* -verktyget och hur du använder det för att certifiera din VM-avbildning.
