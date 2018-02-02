---
title: "Hämta marketplace-objekt från Azure | Microsoft Docs"
description: "I min Azure Stack-distribution kan jag hämta marketplace-objekt från Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2018
ms.author: brenduns
ms.openlocfilehash: 58f8287e5675e1134cb2fcceef9a9128ef97207c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Hämta marketplace-objekt från Azure till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*


Som du bestämmer vilket innehåll som ska ingå i din Azure-stacken marketplace bör du innehållet från Azure marketplace. Du kan hämta från en granskad lista över Azure marketplace-objekt som har förhandstestade körs på Azure-stacken. Nya objekt är ofta läggs till i listan, så se till att kontrollera för nytt innehåll.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Hämta marketplace-objekt i ett scenario med anslutna (med Internetanslutning)

1. Om du vill hämta marketplace-objekt måste du först [registrera Azure stacken med Azure](azure-stack-register.md).
2. Logga in på Azure Stack-administratörsportalen (https://portal.local.azurestack.external).
3. Vissa marketplace-objekt kan vara stora. Kontrollera att du har tillräckligt med utrymme på datorn genom att klicka på **Resursproviders** > **lagring**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Klicka på **fler tjänster** > **Marketplace Management**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Klicka på **Lägg till från Azure** att se en lista med objekt som är tillgängliga för hämtning. Du kan klicka på varje objekt i listan om du vill visa dess beskrivning och filstorlek.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Välj ett objekt i listan och klickar sedan på **hämta**. Detta startar hämta VM-avbildning för det valda objektet. Hämtningstider variera.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. När nedladdningen är klar kan du distribuera din nya marketplace-objektet som ett Azure-stacken operator eller användare. Klicka på **+ ny**söka bland kategorier för nya marketplace-objektet och välj ett objekt.
7. Klicka på **skapa** öppna skapa upplevelsen för det nyligen hämtade objektet. Följ instruktionerna för att distribuera objektet.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Hämta marketplace-objekt i en frånkopplad eller delvis scenariot (med begränsad Internetanslutning)

När du distribuerar Azure Stack i frånkopplat läge (utan någon Internetanslutning), kan du hämta marketplace-objekt med hjälp av Azure Stack-portalen. Du kan dock använda marketplace syndikering för att hämta marketplace-objekt till en dator som är ansluten till internet och överför dem till din Azure Stack-miljö.

### <a name="prerequisites"></a>Förutsättningar
Innan du kan använda verktyget marketplace syndikering, se till att du har [registrerad Azure stacken med din Azure-prenumeration](azure-stack-register.md).  

Använd följande steg för att ladda ned nödvändiga marketplace-objekt från den dator som är ansluten till internet:

1. Öppna ett PowerShell-konsolen som administratör och [installera PowerShell-moduler med Azure-stacken specifika](azure-stack-powershell-install.md). Kontrollera att du installerar **PowerShell version 1.2.11 eller högre**.  

2. Lägg till Azure-konto som du använde för att registrera Azure stacken. Det gör du genom att köra den **Add-AzureRmAccount** cmdlet utan några parametrar. Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto och du kan behöva använda 2-faktor-autentisering baserat på konfigurationen för ditt konto.  

3. Om du har flera prenumerationer kör du följande kommando för att markera det som du har använt för registrering:  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Hämta den senaste versionen av marketplace syndikering verktyget genom att använda följande skript:  

   ```PowerShell
   # Download the tools archive.
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/vnext.zip `
     -OutFile vnext.zip

   # Expand the downloaded files.
   expand-archive vnext.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd \AzureStack-Tools-vnext

   ```

5. Importera modulen syndikering och starta verktyget genom att köra följande kommandon:  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination “<Destination folder path>” `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. När verktyget körs kan uppmanas du att ange dina autentiseringsuppgifter för Azure-konto. Logga in på Azure-konto som du använde för att registrera Azure stacken. När inloggningen är lyckas, bör du se följande skärmbild med listan över tillgängliga marketplace-objekt.  

   ![Azure Marketplace objekt popup](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Välj den avbildning som du vill hämta (du kan välja flera avbildningar genom att hålla Ctrl-tangenten) och anteckna image-version kan du använda den här versionen så här importerar du bilden i nästa avsnitt > klickar du på **Ok** > godkänner du de juridiska villkoren genom att klicka på **Ja**. Du kan också filtrera listan över bilder med hjälp av den **lägga till villkor** alternativet. Hämtningen tar ett tag beroende på storleken på bilden. En gång bilder hämtas, den är tillgänglig i den målsökväg som du angav tidigare. Hämtningen innehåller VHD-filen och galleriet objekt i formatet Azpkg.  

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>Importera avbildningen och publicera den på Azure-stacken marketplace

1. När du har hämtat det bild & galleri paketet spara dem och innehållet i en flyttbar diskenhet AzureStack-verktyg-vnext-mappen och kopiera den till Azure Stack-miljö (du kan kopiera den till lokalt till valfri plats som: ”C:\MarketplaceImages”.)   

2. Innan du importerar avbildningen måste du ansluta till Azure Stack-operatorn miljö med hjälp av stegen som beskrivs i [konfigurera Azure Stack operatorn PowerShell-miljö](azure-stack-powershell-configure-admin.md).  

3. Importera avbildningen till Azure-stacken genom att använda Lägg till AzsVMImage cmdlet. När du använder denna cmdlet, se till att ersätta utgivare, erbjudande och andra parametervärden med värden för den bild som du importerar. Du kan hämta ”publisher”, ”erbjudande” och ”sku” värden av bilden från imageReference objekt av den Azpkg-fil som du hämtade tidigare och ”version”-värdet från steg 6 i föregående avsnitt.

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Ersätt parametervärden och kör följande kommando:

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2017.09.25" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Microsoft.WindowsServer2016DatacenterServerCore-ARM-Eval.2017.09.25.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

4. Använd portalen för att ladda upp din Marketplace-objektet (. Azpkg) till Azure-stacken Blob storage. Du kan överföra till lokal lagring i Azure Stack eller överför till Azure Storage. (Det är en tillfällig plats för paketet.) Se till att blob är offentligt tillgänglig och notera URI: N.  

5. Publicera marketplace-objektet till Azure-stacken genom att använda den **Lägg till AzsGalleryItem**. Exempel:

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. När galleriobjektet har publicerats kan du visa den från den **ny** > **Marketplace** fönstret.  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>Nästa steg

[Skapa och publicera en Marketplace-objektet](azure-stack-create-and-publish-marketplace-item.md)
