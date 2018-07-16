---
title: Hämta marketplace-objekt från Azure | Microsoft Docs
description: Operatorn molnet kan hämta marketplace-objekt från Azure till min Azure Stack-distribution.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/13/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 73f8616449141ca91f96e9fcebede74597bc4fe3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044925"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Hämta marketplace-objekt från Azure till Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Ladda ned objekt från Azure Marketplace som ansvarig för molnet och göra dem tillgängliga i Azure Stack. Det är de objekt som du kan välja från en granskad lista över Azure Marketplace-objekt som testats och har stöd för att fungera med Azure Stack. Ytterligare objekt är ofta läggs till i listan kan fortsätta så Håll utkik efter nytt innehåll. 

Det finns två scenarier för att ansluta till Azure Marketplace: 

- **Ett scenario med anslutna** -som kräver Azure Stack-miljön vara ansluten till internet. Du kan använda Azure Stack-portalen för att leta upp och hämta objekt. 
- **Ett scenario med frånkopplade eller anslutna delvis** -som kräver att du ansluter till internet med hjälp av verktyget för marketplace-syndikering att ladda ned marketplace-objekt. Sedan kan överföra du dina nedladdningar till den frånkopplade Azure Stack-installationen. Det här scenariot används PowerShell.

Se [Azure Marketplace-objekt för Azure Stack](azure-stack-marketplace-azure-items.md) en lista över marketplace-objekt som du kan ladda ned.


## <a name="connected-scenario"></a>Scenariot
Om Azure Stack ansluter till internet kan använda du admin portal för att ladda ned marketplace-objekt.

### <a name="prerequisites"></a>Förutsättningar
Azure Stack-distributioner måste ha Internetanslutning och vara [registrerad med Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Använda portalen för att hämta marketplace-objekt  
1. Logga in på Azure Stack-administratörsportalen.

2.  Granska det tillgängliga lagringsutrymmet innan du laddar ned marketplace-objekt. Senare, när du väljer objekt för nedladdning, kan du jämföra hämtningsstorleken med tillgängliga lagringskapaciteten. Om kapaciteten är begränsad, Överväg alternativ för [hantera tillgängligt utrymme](azure-stack-manage-storage-shares.md#manage-available-space). 

    Att granska tillgängligt utrymme i **regionshantering** väljer du den region som du vill utforska och gå sedan till **Resursprovidrar** > **Storage**.

    ![Granska lagringsutrymme](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Öppna Azure Stack Marketplace och Anslut till Azure. Om du vill göra det, Välj **Marketplace management**, och välj sedan **Lägg till från Azure**.

    ![Lägg till från Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    Portalen visar en lista över tillgängliga för nedladdning från Azure Marketplace. Du kan klicka på varje objekt för att visa dess beskrivning och ytterligare information om det, inklusive dess hämtningsstorlek. 

    ![Marketplace-lista](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Det objekt du vill och välj sedan **hämta**. Filhämtningar variera.

    ![Hämta meddelande](media/azure-stack-download-azure-marketplace-item/image04.png)

    När nedladdningen är klar kan du distribuera det nya marketplace-objektet som ett Azure Stack-operatör eller användare.

5. Om du vill distribuera hämtade objektet, Välj **+ ny**, och sedan söka bland olika kategorier av nya marknadsplats-objektet. Sedan väljer du objektet för att starta distributionsprocessen. Processen varierar för olika marketplace-objekt. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Frånkopplad eller en delvis ansluten scenario

Om Azure Stack är i frånkopplat läge och utan Internetanslutning kan du använda PowerShell och *marketplace syndikering verktyget* att ladda ned marketplace-objekt till en dator med Internetanslutning. Du kan sedan överföra objekt till Azure Stack-miljön. I en frånkopplad miljö kan du ladda ned marketplace-objekt med hjälp av Azure Stack-portalen. 

Marketplace-syndikering verktyget kan också användas i ett scenario med anslutna. 

Det finns två delar i det här scenariot:
- **Del 1:** ladda ned från Azure Marketplace. På datorn med Internetåtkomst konfigurera PowerShell, ladda ned verktyget syndikering och sedan hämta objekt formuläret Azure Marketplace.  
- **Del 2:** överföring och publicering på Azure Stack Marketplace. Du flyttar de filer som du hämtade till Azure Stack-miljön, importera dem till Azure Stack och sedan publicera dem på Azure Stack Marketplace.  


### <a name="prerequisites"></a>Förutsättningar
- Azure Stack-distributioner måste vara [registrerad med Azure](azure-stack-register.md).  

- Den dator som är ansluten till internet måste ha **Azure Stack PowerShell-modulen version 1.2.11** eller högre. Om den inte redan finns [installera PowerShell-moduler med Azure Stack specifika](azure-stack-powershell-install.md).  

- Att aktivera import av ett hämtade marketplace-objekt i [PowerShell-miljö för Azure Stack-operatör](azure-stack-powershell-configure-admin.md) måste konfigureras.  

- Du måste ha en [lagringskonto](azure-stack-manage-storage-accounts.md) i Azure Stack som har en offentligt tillgänglig behållare (vilket är en lagringsblob). Du använder behållare som temporär lagring för marketplace objekt galleriet filer. Om du inte är bekant med lagringskonton och behållare finns i [arbeta med blobar – Azure-portalen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) i Azure-dokumentationen.

- Verktyget för marketplace-syndikering hämtas vid den första proceduren. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Använd verktyget för marketplace-syndikering för att hämta marketplace-objekt

1. Öppna en PowerShell-konsol som administratör på en dator med en Internetanslutning.

2. Lägg till Azure-konto som du använde för att registrera Azure Stack. Att lägga till kontot i PowerShell kör `Add-AzureRmAccount` utan några parametrar. Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto och du kan behöva använda 2-faktor autentisering baserat på konfigurationen för ditt konto.

3. Om du har flera prenumerationer kör du följande kommando för att välja den du har använt för registrering:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Ladda ned den senaste versionen av verktyget för marketplace-syndikering genom att använda följande skript:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importera modulen syndikering och starta verktyget genom att köra följande skript. Ersätt den *sökväg till målmappen* med en plats att lagra de filer som hämtas från Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. När verktyget körs, uppmanas du att ange dina autentiseringsuppgifter för Azure-konto. Logga in på Azure-konto som du använde för att registrera Azure Stack. När inloggningen lyckas visas en skärm som liknar följande bild, med listan över tillgängliga marketplace-objekt.  

   ![Azure Marketplace objekt popup](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Välj ett objekt som du vill ladda ned och anteckna den *version*. (Du kan hålla den *Ctrl* för att markera flera avbildningar.) Du kan referera till den *version* när du importerar objekt i nästa procedur. 
   
   Du kan också filtrera listan över avbildningar med hjälp av den **lägga till villkor** alternativet.

8. Välj **OK**, och sedan granska och Godkänn de juridiska villkoren. 

9. Den tid som nedladdningen tar beror på storleken på objektet. När nedladdningen är klar är artikeln tillgänglig i den mapp som du angav i skriptet. Nedladdningen innehåller en VHD-fil (för virtuella datorer) eller en. ZIP-filen (för tillägg till virtuella datorer). Den innehåller också ett gallery-paket i den *.azpkg* format. (A *.azpkg* paketet är en *.zip* fil.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Importera nedladdningen och publicera Azure Stack Marketplace
1. Filer för avbildningar av virtuella datorer eller mallar för lösningar som du har [tidigare hämtade](#use-the-marketplace-syndication-tool-to-download-marketplace-items) måste vara tillgängliga lokalt till Azure Stack-miljön.  

2. Importera VHD-avbildning till Azure Stack med hjälp av den **Lägg till AzsPlatformimage** cmdlet. När du använder den här cmdleten ersätter den *publisher*, *erbjuder*, och andra parametervärden med värdena för den avbildning som du importerar. 

   Du kan hämta den *publisher*, *erbjuder*, och *sku* värdena för avbildningen från textfilen som hämtar filen AZPKG. Filen lagras på målplatsen.
 
   I följande exempelskript används värdena för den Windows Server 2016 Datacenter - Server Core-VM. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **Om lösningsmallar:** vissa mallar kan innehålla en liten 3 MB. VHD-fil med namnet **fixed3.vhd**. Du behöver inte importera den till Azure Stack. Fixed3.VHD.  Den här filen som ingår i vissa lösningsmallar att uppfylla publishing krav för Azure Marketplace.

   Granska beskrivningen mallar och hämta och importera sedan ytterligare krav som virtuella hårddiskar som krävs för att arbeta med lösningsmallen.

3. Du kan använda administrationsportalen för att överföra marketplace objekt paketet (.azpkg-fil) till Azure Stack Blob storage. Överföring av paketet gör dem tillgängliga för Azure Stack så att du senare kan publicera objektet Azure Stack Marketplace.

   Ladda upp måste du ha ett lagringskonto med en offentligt tillgänglig behållare (se kraven för det här scenariot)   
   1. I Azure Stack-administratörsportalen, går du till **fler tjänster** > **lagringskonton**.  
   
   2. Välj ett lagringskonto från din prenumeration och sedan under **BLOBTJÄNSTEN**väljer **behållare**.  
      ![BLOB service](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Markera den behållare som du vill använda och välj sedan **överför** att öppna den **ladda upp blob** fönstret.  
      ![Behållare](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. Bläddra till de filer som du vill läsa in till lagring och välj sedan i fönstret ladda upp blob **överför**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Filer som du överför visas i fönstret behållare. Välj en fil och kopiera Webbadressen från den **Blobegenskaper** fönstret. Du ska använda den här URL: en i nästa steg när du importerar marketplace-objekt till Azure Stack.  I följande bild, behållaren är *test blobblagring* och filen *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  Filen URL: en är *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Blobegenskaper](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Använd PowerShell för att publicera Azure Stack marketplace-objekt med hjälp av den **Lägg till AzsGalleryItem** cmdlet. Exempel:  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. När du har publicerat ett galleriobjekt kan du visa den från **fler tjänster** > **Marketplace**.  Om din nedladdning är en lösningsmall, kontrollera att du lägger till alla beroende VHD-avbildning för den lösningsmallen.  
  ![Visa marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Du kan nu lägga till tillägg för virtuell dator med versionen av Azure Stack PowerShell 1.3.0.

Exempel:

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Nästa steg
[Skapa och publicera ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md)