---
title: Hämta marketplace-objekt från Azure | Microsoft Docs
description: Operatorn molnet kan hämta Azure marketplace-objekt i min Azure Stack-distribution.
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
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5d403f7c1d0fff466f6c0fb9942ec777ab820eab
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604540"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Hämta marketplace-objekt från Azure till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Hämta objekt från Azure Marketplace och göra dem tillgängliga i Azure-stacken som en moln-operator. Objekt som du kan välja är från en granskad lista över Azure Marketplace-objekt som redan testats och stöds för att fungera med Azure-stacken. Ytterligare artiklar läggs ofta till i listan, så att fortsätta att kontrollera för nytt innehåll. 

Det finns två scenarier för att ansluta till Azure Marketplace: 

- **Ett scenario med anslutna** -som kräver Azure Stack miljön måste vara ansluten till internet. Du kan använda Azure Stack-portal för att leta upp och hämta objekt. 
- **Ett scenario med frånkopplade eller anslutna delvis** -som kräver att du ansluter till internet med hjälp av verktyget marketplace syndikering hämta marketplace-objekt. Sedan överför du din hämtningsbara filer till den frånkopplade Azure Stack-installationen. Det här scenariot använder PowerShell.

Se [Azure Marketplace-objekt för Azure-Stack](azure-stack-marketplace-azure-items.md) en lista över marketplace-objekt som du kan ladda ned.


## <a name="connected-scenario"></a>Scenariot
Om Azure-stacken ansluter till internet kan använda du administrationsportalen i hämta marketplace-objekt.

### <a name="prerequisites"></a>Förutsättningar
Distributionen av Azure-stacken måste ha Internetanslutning och vara [registrerad med Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Använda portalen för att hämta marketplace-objekt  
1. Logga in på Azure Stack-administratörsportalen.

2.  Granska tillgängligt lagringsutrymme innan du hämtar marketplace-objekt. Senare, när du väljer objekt, kan du jämföra hämtningsstorleken till den tillgängliga lagringskapaciteten. Om du har begränsad kapacitet, Överväg alternativ för [hantera tillgängligt utrymme](azure-stack-manage-storage-shares.md#manage-available-space). 

    Granska tillgängligt utrymme i **Region management** väljer du den region som du vill utforska och gå sedan till **Resursproviders** > **lagring**.

    ![Granska lagringsutrymme](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Öppna Azure Stack Marketplace och Anslut till Azure. Om du vill göra det, Välj **Marketplace management**, och välj sedan **Lägg till från Azure**.

    ![Lägg till från Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    Portalen visar listan över objekt som är tillgängliga för nedladdning från Azure Marketplace. Du kan klicka på varje objekt för att visa dess beskrivning och ytterligare information, inklusive dess hämtningsstorlek. 

    ![Marketplace-lista](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Markera objektet som du vill använda och välj sedan **hämta**. Hämtningstider variera.

    ![Hämta meddelandet](media/azure-stack-download-azure-marketplace-item/image04.png)

    När nedladdningen är klar kan du distribuera nya marketplace-objektet som ett Azure-stacken operator eller användare.

5. Om du vill distribuera det hämta objektet, Välj **+ ny**, och sedan söka bland kategorier för nya marketplace-objektet. Sedan väljer du objektet du vill starta distributionsprocessen. Processen varierar för olika marketplace-objekt. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Frånkopplad eller delvis scenariot

Om Azure-stacken är i frånkopplat läge, utan Internetanslutning kan du använda PowerShell och *marketplace syndikering verktyget* att ladda ned marketplace-objekt till en dator med internet-anslutning. Du kan sedan överföra objekten till Azure Stack-miljön. Du kan inte hämta marketplace-objekt med hjälp av Azure Stack-portal i en frånkopplad miljö. 

Verktyget marketplace syndikering kan också användas i ett scenario med anslutna. 

Det finns två delar i det här scenariot:
- **Del 1:** hämta från Azure Marketplace. På datorn med åtkomst till internet konfigurerar du PowerShell, hämta verktyget syndikering och hämta objekt formuläret Azure Marketplace.  
- **Del 2:** överföra och publicera Azure Stack Marketplace. Du flyttar de filer som du hämtade i Azure-stacken miljön, importera dem till Azure-stacken och publicera dem på Azure Marketplace för stacken.  


### <a name="prerequisites"></a>Förutsättningar
- Distributionen av Azure-stacken måste vara [registrerad med Azure](azure-stack-register.md).  

- Den dator som är ansluten till internet måste ha **PowerShell-modulen för Azure-stacken version 1.2.11** eller högre. Om den inte redan finns [installera PowerShell-moduler med Azure-stacken specifika](azure-stack-powershell-install.md).  

- Att aktivera ett hämtade marketplace-objekt att importera den [PowerShell-miljö för operatorn Azure Stack](azure-stack-powershell-configure-admin.md) måste konfigureras.  

- Du måste ha en [lagringskonto](azure-stack-manage-storage-accounts.md) i Azure-grupp som har en offentligt tillgänglig behållare (vilket är ett blob storage). Du kan använda behållaren som temporär lagring för marketplace-objekt gallery-filer. Om du inte är bekant med lagringskonton och behållare finns [arbeta med BLOB - Azure-portalen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) i Azure-dokumentationen.

- Verktyget marketplace syndikering hämtas vid den första proceduren. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Använd verktyget marketplace syndikering att hämta marketplace-objekt

1. Öppna ett PowerShell-konsolen som administratör på en dator med en Internet-anslutning.

2. Lägg till Azure-konto som du använde för att registrera Azure stacken. Att lägga till kontot i PowerShell kör `Add-AzureRmAccount` utan några parametrar. Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto och du kan behöva använda 2-faktor-autentisering baserat på konfigurationen för ditt konto.

3. Om du har flera prenumerationer kör du följande kommando för att markera det som du har använt för registrering:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Hämta den senaste versionen av verktyget marketplace syndikering med hjälp av följande skript:  

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

5. Importera modulen syndikering och starta verktyget genom att köra följande skript. Ersätt den *målmappsökvägen* med en plats att lagra filer som du hämtar från Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. När verktyget körs kan uppmanas du att ange dina autentiseringsuppgifter för Azure-konto. Logga in på Azure-konto som du använde för att registrera Azure stacken. När inloggningen lyckas, bör du se en skärm som liknar följande bild, med listan över tillgängliga marketplace-objekt.  

   ![Azure Marketplace objekt popup](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Välj ett objekt som du vill hämta och anteckna den *version*. (Du kan välja flera avbildningar genom att hålla den *Ctrl* nyckel.) Du sedan hänvisar till den *version* när du importerar objekt i nästa procedur. 
   
   Du kan också filtrera listan över bilder med hjälp av den **lägga till villkor** alternativet.

8. Välj **OK**, och sedan granska och acceptera de juridiska villkoren. 

9. Den tid hämtningen tar beror på storleken på objektet. När nedladdningen är klar är objektet tillgängligt i mappen som du angav i skriptet. Hämtningen innehåller en VHD-fil (för virtuella datorer) eller en. ZIP-filen (för tillägg för virtuell dator). Den innehåller också ett galleripaket i den *.azpkg* format. (A *.azpkg* paketet är en *.zip* filen.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Importera hämtningen och publicera Azure Stack Marketplace
1. Filer för virtuella bilder eller lösningsmallar som du har [tidigare hämtade](#use-the-marketplace-syndication-tool-to-download-marketplace-items) måste tillhandahållas lokalt till din Azure Stack-miljö.  

2. Importera. VHD-filer till Azure-stacken. Om du vill importera en avbildning av virtuell dator (VM), måste du ha följande information om den virtuella datorn:
   - Den *version*, enligt beskrivningen i steg 7 i föregående procedur.
   - Värden för de virtuella datorerna *publisher*, *erbjuder*, och *sku*. För att få dessa värden, byta namn på en kopia av den **.azpkg** fil att ändra filnamnstillägget till **.zip**. Du kan sedan använda en textredigerare för att öppna **DeploymentTemplates\CreateUiDefinition.json**. Leta upp i JSON-fil i *imageReference* avsnitt som innehåller dessa värden för marketplace-objektet. I följande exempel visas hur den här informationen visas:

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   Importera avbildningen till Azure-stacken genom att använda den **Lägg till AzsPlatformimage** cmdlet. När du använder denna cmdlet, se till att ersätta den *publisher*, *erbjuder*, och andra parametervärden med värden för den bild som du importerar. Du kan hämta den *publisher*, *erbjuder*, och *sku* värden av bilden från den textfil som hämtas tillsammans med filen AZPKG och lagras på målplatsen . 

   I följande exempelskriptet används värdena för Datacenter Windows Server 2016 - Server Core virtuella. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **Om lösningsmallar:** vissa mallar kan innehålla en liten 3 MB. VHD-fil med namnet **fixed3.vhd**. Du behöver inte importera den till Azure-stacken. Fixed3.VHD.  Den här filen ingår i vissa lösningsmallar till publishing krav för Azure Marketplace.

   Granska beskrivningen mallar och hämta och importera sedan ytterligare krav som virtuella hårddiskar som krävs för att arbeta med hjälp av mallen för lösningen.

3. Använda administrationsportal för att överföra marketplace objektet paketet (.azpkg-filen) till Azure-stacken Blob storage. Överföring av paketet gör den tillgänglig för Azure-stacken så att du senare kan publicera artikeln Stack Azure Marketplace.

   Överför kräver att du har ett lagringskonto med en offentligt tillgänglig behållare (se krav för det här scenariot)   
   1. Gå till i administrationsportalen för Azure-stacken **fler tjänster** > **lagringskonton**.  
   
   2. Välj ett lagringskonto från din prenumeration och sedan under **BLOB-tjänsten**väljer **behållare**.  
      ![BLOB-tjänsten](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Markera den behållare som du vill använda och välj sedan **överför** att öppna den **överför blob** fönstret.  
      ![Behållaren](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. Bläddra till de filer som du vill läsa in i lagring och välj sedan i fönstret överför blob **överför**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Filer som du överför visas i fönstret behållare. Välj en fil och sedan kopiera Webbadressen från den **Blob-egenskaper** fönstret. Du kommer att använda Webbadressen i nästa steg när du importerar marketplace-objektet till Azure-stacken.  I följande bild, behållaren är *test blobblagring* och filen *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  Filen URL: en är *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![BLOB-egenskaper](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Använda PowerShell för att publicera marketplace-objektet till Azure-stacken genom att använda den **Lägg till AzsGalleryItem** cmdlet. Exempel:  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. När du har publicerat ett galleriobjekt kan du visa den från **fler tjänster** > **Marketplace**.  Om hämtningen är en lösningsmall, kontrollera att du lägger till alla beroende VHD-avbildningen för att lösningsmall.  
  ![Visa marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Du kan nu lägga till tillägg för virtuell dator med versionen av Azure-stacken PowerShell 1.3.0.

Exempel:

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Nästa steg
[Skapa och publicera en Marketplace-objektet](azure-stack-create-and-publish-marketplace-item.md)