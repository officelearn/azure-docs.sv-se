---
title: "Använda Marketplace toolkit för att skapa och publicera marketplace-objekt | Microsoft Docs"
description: "Lär dig att snabbt skapa marketplace-objekt med publicering Toolkit"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61ee3296429f9641643f1c9268ae89e3691fcfa1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Lägg till marketplace-objekt med hjälp av Publiceringsverktyget
Lägger till ditt innehåll till den [Azure Stack Marketplace](azure-stack-marketplace.md) tillgängliggör dina lösningar för dig och dina klienter för distribution.  Marketplace-Toolkit skapar Azure Marketplace-paket (.azpkg) filer baserat på din IaaS Azure Resource Manager-mallar eller VM-tillägg.  Du kan också använda Marketplace Toolkit för att publicera .azpkg filer skapas med verktyget eller med hjälp av [manuell](azure-stack-create-and-publish-marketplace-item.md) steg.  Det här avsnittet hjälper dig att hämta verktyget, skapa en marketplace-objekt baserat på en mall och sedan publicera elementet i stapeln Azure Marketplace.     


## <a name="prerequisites"></a>Förutsättningar
 - Du måste köra verktyget på Azure Stack-värden eller har [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) anslutning från den dator där du kör verktyget.

 - Hämta den [Azure Stack-snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) och extrahera.

 - Hämta den [Azure-galleriet paketering verktyget](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Publicering till marketplace kräver ikoner och en miniatyr fil.  Du kan använda din egen eller spara den [exempel](azure-stack-marketplace-publisher.md#support-files) filer lokalt i det här exemplet.

## <a name="download-the-tool"></a>Ladda ned verktyget
Marketplace-Toolkit kan vara [hämtas från lagringsplatsen för Azure-stacken verktyg](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Skapa marketplace-objekt
I det här avsnittet använder du Marketplace Toolkit för att skapa ett paket för marketplace-objekt i .azpkg format.  

### <a name="provide-marketplace-information-with-wizard"></a>Ange marketplace information med guiden
1. Kör Marketplace-verktyget från en PowerShell-session:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Klicka på den **lösning** fliken.  Den här skärmen accepterar information om dina marketplace-objektet. Ange information om objektet som du vill ska visas i marketplace.  Du kan också ange en [parameterfilen](azure-stack-marketplace-publisher.md#use-a-parameters-file) att fylla i formuläret.  
    
    ![Skärmbild av Marketplace Toolkit första skärmen](./media/azure-stack-marketplace-publisher/image7.png)
3. Klicka på **Bläddra** och välj en avbildningsfil för varje ikon och skärmbild fält.  Du kan använda din egen ikoner eller exempel ikoner i den [stödfiler](azure-stack-marketplace-publisher.md#support-files) avsnitt.
4. När alla fält fylls välja ”Preview postlösning” för en förhandsgranskning av lösningen i Marketplace.  Du kan ändra och redigera text, bilder och skärmbild innan du klickar på **nästa**.  

### <a name="import-template-and-create-package"></a>Importera mallen och skapa paket
I det här avsnittet Importera mallen och arbeta med indata för lösningen.

1.  Klicka på **Bläddra** och välj den *azuredeploy.json* från mappen 101-enkel-Windows-VM i de hämtade mallarna.

    ![Skärmbild av Marketplace Toolkit andra skärmen](./media/azure-stack-marketplace-publisher/image8.png)
2.  Guiden fylls med en *grundläggande* steg och indata objekt för varje parameter som angetts i mallen.  Du kan lägga till fler steg och flytta indata mellan stegen.  Exempelvis kanske ”frontend-konfiguration” och ”serverdel” konfigurationssteg för din lösning.
3.  Ange sökvägen till AzureGalleryPackager.exe.  
4.  Klicka på **skapa** och Marketplace Toolkit paket lösningen till en .azpkg-fil.  När borttagningen är klar visar sökvägen till lösningsfilen i guiden och ger dig möjlighet att fortsätta att publicera paketet till Azure-stacken.


## <a name="publish-marketplace-items"></a>Publicera marketplace-objekt
I det här avsnittet Publicera marketplace-objektet till din Azure-stacken Marketplace.

![Skärmbild av Marketplace Toolkit första skärmen](./media/azure-stack-marketplace-publisher/image9.png)

1.  Guiden kräver information för att publicera dina lösningar:
    
    |Fält|Beskrivning|
    |-----|-----|
    | Namn på serveradministratör | Administratörskonto för tjänsten.  Exempel:  ServiceAdmin@mydomain.onmicrosoft.com |
    | Lösenord | Lösenord för administratörskontot för tjänsten. |
    | API-slutpunkt | Azure Stack Azure Resource Manager-slutpunkt.  Example: management.local.azurestack.external |
2.  Klicka på **publicera** och publishing loggen visas.
3.  Du kan nu distribuera din publicerade artikeln via Azure Stack-portal.


## <a name="use-a-parameters-file"></a>Använd en fil med parametrar
Du kan också använda en fil med parametrar för att slutföra artikelinformation marketplace.  

Marketplace-Toolkit innehåller en *solution.parameters.ps1* du kan använda för att skapa en egen fil med parametrar.


## <a name="support-files"></a>Stödfiler
| Beskrivning | Exempel |
| ----- | ----- |
| 40 x 40 .png-ikon | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90 x 90 .png-ikon | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115 x 115 .png-ikon | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255 x 115 .png-ikon | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324 .png miniatyr | ![](./media/azure-stack-marketplace-publisher/image5.png) |


