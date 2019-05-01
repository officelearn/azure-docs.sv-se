---
title: Hantera åtkomstkontrollistor i Azure-slutpunkt | PowerShell | Klassiska | Microsoft Docs
description: 'Lär dig hur du hanterar ACL: er med PowerShell'
services: virtual-network
documentationcenter: na
author: genlin
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: c43dacaf7bb5ab17fe740dd429e4a40dbc11bb6e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726928"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Hantera åtkomstkontrollistor för slutpunkt med PowerShell i den klassiska distributionsmodellen
Du kan skapa och hantera nätverket åtkomstkontrollistor (ACL) för slutpunkter med hjälp av Azure PowerShell eller i hanteringsportalen. Det här avsnittet innehåller procedurer för ACL vanliga uppgifter som du kan utföra med hjälp av PowerShell. Lista över Azure PowerShell cmdlet: ar i [Azure Management-Cmdlets](https://go.microsoft.com/fwlink/?LinkId=317721). Mer information om ACL: er finns i [vad är ett nätverk åtkomstkontrollistan (ACL)?](virtual-networks-acl.md). Om du vill hantera din ACL: er med hjälp av hanteringsportalen, se [så konfigurera slutpunkter till en virtuell dator](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Hantera nätverk ACL: er med hjälp av Azure PowerShell
Du kan använda Azure PowerShell-cmdletar för att skapa, ta bort och konfigurera (set) nätverk åtkomstkontrollistor (ACL). Vi har inkluderat några exempel på några av de sätt som du kan konfigurera en ACL med hjälp av PowerShell.

Om du vill hämta en fullständig lista över ACL PowerShell-cmdlets kan du använda något av följande:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Skapa en nätverks-ACL med regler som tillåter åtkomst från ett fjärranslutet undernät
Exemplet nedan visar ett sätt att skapa en ny ACL som innehåller regler. Den här ACL tillämpas sedan till en slutpunkt för virtuell dator. ACL-reglerna i exemplet nedan kommer att tillåta åtkomst från ett fjärranslutet undernät. Skapa en ny nätverks-ACL med tillåter regler för ett fjärranslutet undernät genom att öppna en Azure PowerShell ISE. Kopiera och klistra in skriptet nedan, konfigurera skriptet med dina egna värden och sedan köra skriptet.

1. Skapa det nya nätverk ACL-objektet.
   
        $acl1 = New-AzureAclConfig
2. Ange en regel som tillåter åtkomst från ett fjärranslutet undernät. I exemplet nedan ställer du in regel *100* (som har högre prioritet än regeln 200 och högre) så att fjärrundernätet *10.0.0.0/8* åtkomst till virtuella datorslutpunkt. Ersätt värdena med dina egna konfigurationskrav. Namnet ”SharePoint ACL-config” ska ersättas med det egna namnet som du vill anropa den här regeln.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Upprepa cmdleten, där du ersätter värdena med dina egna konfigurationskrav för ytterligare regler. Kom ihåg att ändra regeln nummer för att återspegla den ordning som du vill att regler som ska användas. Lägre regelnummer har företräde framför den högre nummer.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Därefter kan du antingen skapa en ny slutpunkt (Lägg till) eller ange ACL för en befintlig slutpunkt (Set). I det här exemplet ska vi lägga till en ny virtuell dator-slutpunkt som kallas ”web” och uppdatera virtuella datorslutpunkt med ACL-inställningarna.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Sedan kombinera cmdletar och kör skriptet. I det här exemplet kombinerade cmdlets skulle se ut så här:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Ta bort en nätverk ACL-regel som tillåter åtkomst från ett fjärranslutet undernät
Exemplet nedan visar ett sätt att ta bort en regel för ACL.  Ta bort en nätverk ACL-regel med tillåter regler för ett fjärranslutet undernät, öppna ett Azure PowerShell ISE. Kopiera och klistra in skriptet nedan, konfigurera skriptet med dina egna värden och sedan köra skriptet.

1. Första steget är att hämta Network ACL-objektet för virtuella datorslutpunkt. Sedan tar du bort regeln för Portåtkomstkontrollistor. I det här fallet vi tas den bort av regel-ID Detta tar bara bort regel-ID 0 från ACL. ACL-objektet tas inte bort från virtuella datorslutpunkt.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Sedan måste du använda nätverk ACL-objekt på virtuella datorslutpunkt och uppdatera den virtuella datorn.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Ta bort en ACL för nätverk från en slutpunkt för virtuell dator
I vissa fall, kanske du vill ta bort ett nätverk ACL-objekt från en virtuell dator-slutpunkt. Gör detta genom att öppna en Azure PowerShell ISE. Kopiera och klistra in skriptet nedan, konfigurera skriptet med dina egna värden och sedan köra skriptet.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
[Vad är ett nätverk åtkomstkontrollistan (ACL)?](virtual-networks-acl.md)

