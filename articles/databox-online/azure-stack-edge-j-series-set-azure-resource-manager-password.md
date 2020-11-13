---
title: Ange Azure Resource Manager lösen ord på din Azure Stack kant-GPU-enhet
description: Beskriver hur du anger Azure Resource Manager lösen ordet för din Azure Stack Edge Pro-GPU med Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 915146cd17b90272daea4ce57f5243baf1d49cb3
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578798"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-pro-gpu-device"></a>Ange Azure Resource Manager lösen ord på Azure Stack Edge Pro GPU-enhet

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

I den här artikeln beskrivs hur du ställer in Azure Resource Manager lösen ordet. Du måste ange det här lösen ordet när du ansluter till lokala enhets-API: er via Azure Resource Manager.

Hur du ställer in lösenordet kan variera beroende på om du använder Azure-portalen eller PowerShell-cmdletar. Var och en av dessa procedurer beskrivs i följande avsnitt.


## <a name="reset-password-via-the-azure-portal"></a>Återställ lösen ord via Azure Portal

1. Gå till den Azure Stack Edge-resurs som du skapade för att hantera enheten i Azure Portal. Gå till **Edge compute > kom igång**.

2. Välj **Återställ Edge ARM-lösenord** från kommandoraden i den högra rutan. 

    ![Återställ EdgeARM User Password 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. I bladet **Återställ EdgeArm användar lösen** ord anger du ett lösen ord för att ansluta till dina lokala API: er för lokala enheter via Azure Resource Manager. Bekräfta lösen ordet och välj **Återställ**.

    ![Återställ EdgeARM User Password 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Återställ lösen ord via PowerShell

1. I Azure Portal går du till den Azure Stack Edge-resurs som du skapade för att hantera enheten. Anteckna följande parametrar på sidan **Översikt** .

    - Resurs namn för Azure Stack Edge
    - Prenumerations-ID:t

2. Gå till **inställningar > egenskaper**. Anteckna följande parametrar på sidan **Egenskaper** .

    - Resursgrupp
    - CIK krypterings nyckel: Välj Visa och kopiera sedan **krypterings nyckeln**.

    ![Hämta krypterings nyckel för CIK](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Identifiera ett lösen ord som du ska använda för att ansluta till Azure Resource Manager.

4. Starta Cloud Shell. Välj på ikonen i det övre högra hörnet:

    ![Starta Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    När Cloud Shell har startats kan du behöva växla till PowerShell.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Ange kontext. Ange:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Här är exempel på utdata:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Om du har gamla PS-moduler måste du installera dem.

    `Remove-Module  Az.DataBoxEdge -force`

    Här är ett exempel på utdata. I det här exemplet fanns det inga gamla moduler att installera.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. Nästa uppsättning kommandon kommer att hämta och köra ett skript för att installera PowerShell-moduler.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. I nästa uppsättning kommandon måste du ange resurs namn, resurs grupps namn, krypterings nyckel och lösen ord som du identifierade i föregående steg.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    Parametrarna för lösen ords-och krypterings nycklar måste skickas som säkra strängar. Använd följande cmdlets för att konvertera lösen ordet och krypterings nyckeln för att skydda strängarna.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Använd de skyddade strängarna ovan genererade som parametrar i Set-AzDataBoxEdgeUser cmdlet för att återställa lösen ordet. Använd samma resurs grupp som du använde när du skapade Azure Stack Edge Pro/Data Box Gateway-resursen.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Här är exempel på utdata.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
Använd det nya lösen ordet för att ansluta till Azure Resource Manager.

## <a name="next-steps"></a>Nästa steg

[Anslut till Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)
