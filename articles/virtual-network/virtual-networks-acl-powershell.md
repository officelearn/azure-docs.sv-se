---
title: Hantera Azure endpoint åtkomstkontrollistor | PowerShell | Klassiska | Microsoft Docs
description: 'Lär dig att hantera ACL: er med PowerShell'
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: c3476908447380ccd7e8b9c0f1c2a55ae763cc1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23928855"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Hantera åtkomstkontrollistor för slutpunkten med PowerShell i den klassiska distributionsmodellen
Du kan skapa och hantera nätverket åtkomstkontrollistor (ACL) för slutpunkter med hjälp av Azure PowerShell eller i hanteringsportalen. I det här avsnittet hittar du procedurer för ACL vanliga uppgifter som du kan utföra med hjälp av PowerShell. Lista över Azure PowerShell cmdlets finns [Azure Management-Cmdlets](http://go.microsoft.com/fwlink/?LinkId=317721). Mer information om åtkomstkontrollistor finns [vad är ett nätverk åtkomstkontrollista (ACL)?](virtual-networks-acl.md). Om du vill hantera din ACL: er med hjälp av hanteringsportalen, se [så ange slutpunkter till en virtuell dator](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Hantera nätverket ACL: er med hjälp av Azure PowerShell
Du kan använda Azure PowerShell-cmdlets för att skapa, ta bort och konfigurera (uppsättning) nätverket åtkomstkontrollistor (ACL). Innehåller några exempel på några av de sätt som du kan konfigurera en ACL med hjälp av PowerShell.

Du kan använda något av följande om du vill hämta en fullständig lista över ACL PowerShell-cmdlet:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Skapa en ACL i nätverket med regler som tillåter åtkomst från ett fjärranslutet undernät
I exemplet nedan kan du skapa en ny ACL som innehåller regler. Denna ACL tillämpas sedan på en virtuell dator-slutpunkt. ACL-regler i exemplet nedan kommer att tillåta åtkomst från ett fjärranslutet undernät. Öppna ett Azure PowerShell ISE för att skapa en ny ACL i nätverket med tillståndet regler för ett fjärranslutet undernät. Kopiera och klistra in skriptet nedan, konfigurera skriptet med egna värden och sedan köra skriptet.

1. Skapa det nya nätverk ACL-objektet.
   
        $acl1 = New-AzureAclConfig
2. Ange en regel som tillåter åtkomst från ett fjärranslutet undernät. I exemplet nedan som du anger regel *100* (som har högre prioritet än 200 och högre) så att det fjärranslutna undernätet *10.0.0.0/8* åtkomst till den virtuella datorslutpunkten. Ersätt värdena med dina egna konfigurationskrav. Namnet ”SharePoint ACL-config” ska ersättas med det egna namnet som du vill anropa den här regeln.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Upprepa cmdleten, där du ersätter värdena med dina egna konfigurationskrav för ytterligare regler. Se till att ändra den här regeln number ordern så att den ordning som du vill att regler tillämpas. Antalet nedre regeln företräde framför högre nummer.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Därefter kan du antingen skapa en ny slutpunkt (Lägg till) eller ange ACL för en befintlig slutpunkt (anges). I det här exemplet ska vi lägga till en ny virtuell dator-slutpunkt som kallas ”web” och uppdatera virtuell datorslutpunkt med ACL-inställningar.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Sedan kombinera cmdletar och kör skriptet. I det här exemplet kombinerade cmdlets skulle se ut så här:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Ta bort en nätverket ACL-regel som tillåter åtkomst från ett fjärranslutet undernät
Exemplet nedan visar ett sätt att ta bort en regel för Portåtkomstkontroll.  Ta bort en nätverket regeln med tillståndet regler för ett fjärranslutet undernät, öppna ett Azure PowerShell ISE. Kopiera och klistra in skriptet nedan, konfigurera skriptet med egna värden och sedan köra skriptet.

1. Första steget är att hämta nätverket ACL-objektet för den virtuella datorslutpunkten. Sedan tar du bort regeln för Portåtkomstkontroll. I det här fallet bort vi den efter regeln-ID. Detta tar endast bort regel-ID 0 från ACL. ACL-objekt tas inte bort från den virtuella datorslutpunkten.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Därefter måste du använda nätverket ACL-objekt till den virtuella datorslutpunkten och uppdatera den virtuella datorn.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Ta bort en ACL i nätverket från en virtuell datorslutpunkt
I vissa fall kanske du vill ta bort ett nätverk ACL-objekt från den virtuella datorslutpunkten. För att göra det, öppnar du en Azure PowerShell ISE. Kopiera och klistra in skriptet nedan, konfigurera skriptet med egna värden och sedan köra skriptet.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
[Vad är ett nätverk åtkomstkontrollista (ACL)?](virtual-networks-acl.md)

