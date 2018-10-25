---
title: Använd Marketplace toolkit för att skapa och publicera marketplace-objekt | Microsoft Docs
description: Lär dig hur du snabbt skapa marketplace-objekt med publiceringen Toolkit
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: fabc72e6dc31bb7f244cda9634af3b2556ba23a9
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023801"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Lägg till marketplace-objekt med Publiceringsverktyget

Att lägga till ditt innehåll till den [Azure Stack Marketplace](azure-stack-marketplace.md) gör dina lösningar tillgängliga för dig och dina klienter för distribution. Marketplace-toolkit skapar Azure Marketplace (.azpkg) paketfilerna baserat på dina IaaS Azure Resource Manager-mallar eller VM-tillägg. Du kan också använda verktyget Marketplace för att publicera .azpkg filer, antingen som skapats med verktyget eller med hjälp av [manuell](azure-stack-create-and-publish-marketplace-item.md) steg. Det här avsnittet beskriver hämta verktyget, skapa ett marketplace-objekt baserat på en mall för virtuell dator och sedan publicera objekt i Azure Stack Marketplace.     

## <a name="prerequisites"></a>Förutsättningar

 - Du måste köra verktyget på Azure Stack-värden eller har [VPN](.\asdk\asdk-connect.md#connect-to-azure-stack-with-vpn) anslutningen till ASDK-värden från den dator där du kör verktyget.

 - Ladda ned den [Azure Stack-snabbstartmallar](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) och extrahera.

 - Ladda ned den [Azure-galleriet paketering verktyget](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Publicering på Marketplace kräver ikoner och en miniatyr fil. Du kan använda din egen eller spara den [exempel](azure-stack-marketplace-publisher.md#support-files) filer lokalt i det här exemplet.

## <a name="download-the-tool"></a>Ladda ned verktyget

Du kan hämta verktyget Marketplace [från lagringsplatsen för Azure Stack Tools](azure-stack-powershell-download.md).

##  <a name="create-marketplace-items"></a>Skapa marketplace-objekt

I det här avsnittet använder du Marketplace toolkit för att skapa ett paket för marketplace-objekt i .azpkg format.  

### <a name="provide-marketplace-information-with-wizard"></a>Ange marketplace information med guiden

1. Kör verktyget Marketplace från en PowerShell-session:
   ```PowerShell
   .\MarketplaceToolkit.ps1
   ```

2. Klicka på den **lösning** fliken. Den här skärmen godkänner information om dina marketplace-objekt. Ange information om dina objekt som du vill ska visas i marketplace. Du kan också ange en [parameterfilen](azure-stack-marketplace-publisher.md#use-a-parameters-file) att fylla i formuläret.  
    
    ![Skärmbild av Marketplace Toolkit första skärmen](./media/azure-stack-marketplace-publisher/image7.png)
3. Klicka på **Bläddra** och välj en bildfil för varje ikon och skärmbild fält. Du kan använda din egen ikoner eller exempel ikonerna i det [stödfiler](azure-stack-marketplace-publisher.md#support-files) avsnittet.
4. När alla fält har fyllts i, väljer du ”förhandsversion lösning” för en förhandsversion av lösningen i Marketplace. Du kan ändra och redigera text, bilder och skärmbild innan du klickar på **nästa**.  

### <a name="import-template-and-create-package"></a>Importera mallen och skapa paket

I det här avsnittet ska du importera mallen och arbeta med indata för din lösning.

1.  Klicka på **Bläddra** och välj den *azuredeploy.json* från mappen 101-enkel-Windows-VM i de hämta mallarna.

    ![Skärmbild av andra Marketplace Toolkit-skärmen](./media/azure-stack-marketplace-publisher/image8.png)
2.  Guiden fylls med en *grundläggande* steg och indata objekt för varje parameter som angetts i mallen. Du kan lägga till fler steg och flytta indata mellan stegen. Exempelvis kan du ”Front-End-konfiguration” och ”serverdel” konfigurationssteg för din lösning.
3.  Ange sökvägen till AzureGalleryPackager.exe.  
4.  Klicka på **Skapa**. Marketplace-verktyget paketerar din lösning i en .azpkg-fil. När du är klar visar sökvägen till lösningsfilen med din i guiden och ger dig möjlighet att fortsätta publicera ditt paket till Azure Stack.

## <a name="publish-marketplace-items"></a>Publicera marketplace-objekt

I det här avsnittet kan publicera du marketplace-objekt till din Azure Stack Marketplace.

![Skärmbild av Marketplace Toolkit första skärmen](./media/azure-stack-marketplace-publisher/image9.png)

1.  Guiden kräver information för att publicera din lösning:
    
    |Fält|Beskrivning|
    |-----|-----|
    | Admin-tjänstnamn | Tjänstadministratörskontot.  Exempel: ServiceAdmin@mydomain.onmicrosoft.com |
    | Lösenord | Lösenord för administratörskontot för tjänsten. |
    | API-slutpunkt | Azure Stack Azure Resource Manager-slutpunkt. Exempel: management.local.azurestack.external |
2.  Klicka på **publicera** och publicera loggen visas.
3.  Du kan nu distribuera din publicerade objekt via Azure Stack-portalen.

## <a name="use-a-parameters-file"></a>Använd en fil med parametrar

Du kan också använda en fil med parametrar för att slutföra artikelinformation marketplace.  

Marketplace-verktygen innehåller en *solution.parameters.ps1* du kan använda för att skapa en egen fil med parametrar.

## <a name="support-files"></a>Stöd för filer

| Beskrivning | Exempel |
| ----- | ----- |
| 40 x 40 .png-ikon | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90 x 90 .png-ikon | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115 x 115 .png-ikon | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255 x 115 .png-ikon | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324 .png miniatyr | ![](./media/azure-stack-marketplace-publisher/image5.png) |

## <a name="next-steps"></a>Nästa steg

[Hämta Marketplace-objekt](azure-stack-download-azure-marketplace-item.md)  
[Skapa och publicera ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md)