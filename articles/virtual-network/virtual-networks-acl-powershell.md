---
title: Hantera åtkomst kontrol listor för Azure-slutpunkt | PowerShell | Klassisk | Microsoft Docs
description: 'Lär dig hur du hanterar ACL: er med PowerShell'
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 4fe8349863c16a15886f9f1d33056f0bbfec31f2
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056757"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Hantera åtkomst kontrol listor för slut punkter med PowerShell i den klassiska distributions modellen
Du kan skapa och hantera nätverks Access Control listor (ACL: er) för slut punkter med hjälp av Azure PowerShell eller i Hanteringsportal. I det här avsnittet hittar du procedurer för vanliga ACL-uppgifter som du kan slutföra med PowerShell. En lista över Azure PowerShell-cmdlets finns i [Azure Management-cmdletar](https://go.microsoft.com/fwlink/?LinkId=317721). Mer information om åtkomst kontrol listor finns i [Vad är en nätverks Access Control lista (ACL)?](virtual-networks-acl.md). Om du vill hantera dina ACL: er med hjälp av Hanteringsportal, se [så här konfigurerar du slut punkter till en virtuell dator](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Hantera nätverks-ACL: er genom att använda Azure PowerShell
Du kan använda Azure PowerShell-cmdletar för att skapa, ta bort och konfigurera (ange) nätverks Access Control listor (ACL: er). Vi har inkluderat några exempel på hur du kan konfigurera en ACL med PowerShell.

Om du vill hämta en fullständig lista över ACL PowerShell-cmdletar kan du använda något av följande:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Skapa en nätverks-ACL med regler som tillåter åtkomst från ett fjärran slutet undernät
Exemplet nedan illustrerar hur du skapar en ny ACL som innehåller regler. Denna ACL tillämpas sedan på en virtuell dator slut punkt. ACL-reglerna i exemplet nedan tillåter åtkomst från ett fjärran slutet undernät. Om du vill skapa en ny nätverks-ACL med regler för ett fjärrundernät öppnar du en Azure PowerShell ISE. Kopiera och klistra in skriptet nedan och konfigurera skriptet med dina egna värden och kör sedan skriptet.

1. Skapa det nya objektet för nätverks-ACL.
   
        $acl1 = New-AzureAclConfig
2. Ange en regel som tillåter åtkomst från ett fjärran slutet undernät. I exemplet nedan ställer du in regel *100* (som har prioritet över regel 200 och högre) för att tillåta fjärr-undernät *10.0.0.0/8* -åtkomst till den virtuella datorns slut punkt. Ersätt värdena med dina egna konfigurations krav. Namnet "SharePoint ACL config" måste ersättas med det egna namnet som du vill anropa regeln för.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Om du vill ha ytterligare regler upprepar du cmdleten och ersätter värdena med dina egna konfigurations krav. Se till att ändra regelns nummer ordning så att den visar i vilken ordning du vill att reglerna ska tillämpas. Det lägre regel numret har företräde framför det högre talet.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Sedan kan du antingen skapa en ny slut punkt (lägga till) eller ange ACL för en befintlig slut punkt (uppsättning). I det här exemplet lägger vi till en ny slut punkt för virtuella datorer med namnet "Web" och uppdaterar slut punkten för den virtuella datorn med ACL-inställningarna.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Sedan kombinerar du cmdletarna och kör skriptet. I det här exemplet skulle de kombinerade cmdletarna se ut så här:
   
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

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Ta bort en regel för nätverks-ACL som tillåter åtkomst från ett fjärran slutet undernät
I exemplet nedan visas ett sätt att ta bort en regel för nätverks-ACL.  Öppna en Azure PowerShell ISE om du vill ta bort en regel för en nätverks-ACL med regler för ett fjärran slutet undernät. Kopiera och klistra in skriptet nedan och konfigurera skriptet med dina egna värden och kör sedan skriptet.

1. Det första steget är att hämta objektet för nätverks-ACL för den virtuella datorns slut punkt. Sedan tar du bort ACL-regeln. I det här fallet tar vi bort det med regel-ID. Detta tar bara bort regel-ID 0 från åtkomst kontrol listan. ACL-objektet tas inte bort från slut punkten för den virtuella datorn.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Därefter måste du tillämpa objektet Network ACL på den virtuella datorns slut punkt och uppdatera den virtuella datorn.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Ta bort en nätverks-ACL från en virtuell dator slut punkt
I vissa fall kanske du vill ta bort ett nätverks-ACL-objekt från en slut punkt för virtuella datorer. Det gör du genom att öppna en Azure PowerShell ISE. Kopiera och klistra in skriptet nedan och konfigurera skriptet med dina egna värden och kör sedan skriptet.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
[Vad är en lista över nätverks Access Control (ACL)?](virtual-networks-acl.md)

