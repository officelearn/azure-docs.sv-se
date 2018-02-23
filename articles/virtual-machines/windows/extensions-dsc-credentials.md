---
title: "Skicka autentiseringsuppgifter till Azure med hjälp av Desired State Configuration | Microsoft Docs"
description: "Lär dig mer om att på ett säkert sätt skicka autentiseringsuppgifter till virtuella Azure-datorer med hjälp av PowerShell önskad tillstånd Configuration (DSC)."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/17/2018
ms.author: zachal,migreene
ms.openlocfilehash: a0a565c0bb7e17315c7b0475f3213b620a3e2d6c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Skicka autentiseringsuppgifter till Azure DSCExtension-hanterare
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Den här artikeln beskriver önskad tillstånd Configuration DSC ()-tillägg för Azure. En översikt över hanteraren för DSC-tillägg, se [introduktion till Azure Desired State Configuration-tillägget hanteraren](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="pass-in-credentials"></a>Ange autentiseringsuppgifter

Som en del av konfigurationsprocessen, kanske du måste ställa in användarkonton, komma åt tjänster eller installera ett program i en användarkontext. Om du vill göra detta måste du ange autentiseringsuppgifter.

Du kan använda DSC för att ställa in parametrar konfigurationer. I en konfiguration för parametriserade autentiseringsuppgifterna skickades till konfigurationen och lagras på ett säkert sätt i MOF-filer. Azure-tillägget hanteraren förenklar hanteringen av autentiseringsuppgifter genom att tillhandahålla automatisk hantering av certifikat.

Följande DSC-konfigurationsskript skapar ett lokalt konto med det angivna lösenordet:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Det är viktigt att ha **nod localhost** som en del av konfigurationen. Tillägget hanteraren specifikt söker efter den **nod localhost** instruktionen. Om den här instruktionen saknas, fungerar inte följande steg. Det är också viktigt att inkludera den typecast **[PsCredential]**. Den här specifika typen utlöser tillägget för att kryptera autentiseringsuppgifterna.

För att publicera det här skriptet till Azure Blob storage:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Ange Azure DSC-tillägg och ange autentiseringsuppgifterna:

```powershell
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```

## <a name="how-a-credential-is-secured"></a>Hur en autentiseringsuppgift skyddas

Kör den här koden efterfrågar autentiseringsuppgifter. När autentiseringsuppgifterna har angetts, lagras den kortfattat i minnet. När autentiseringsuppgifterna har publicerats med hjälp av den **Set AzureVmDscExtension** cmdlet, autentiseringsuppgifter skickas via HTTPS till den virtuella datorn. I den virtuella datorn lagrar Azure autentiseringsuppgifterna krypteras på disken med hjälp av lokala VM-certifikatet. Autentiseringsuppgifterna dekrypteras kortfattat i minnet och sedan krypteras det igen om du vill skicka till DSC.

Den här processen skiljer sig från [använder säker konfigurationer utan tillägget hanteraren](https://msdn.microsoft.com/powershell/dsc/securemof). Azure-miljön kan du överföra konfigurationsdata på ett säkert sätt via certifikat. När du använder Hanteraren för DSC-tillägg, behöver du inte ange **$CertificatePath** eller en **$CertificateID**/ **$Thumbprint** post i **ConfigurationData**.

## <a name="next-steps"></a>Nästa steg

* Hämta en [introduktion till Azure DSC-tillägg hanterare](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Granska de [Azure Resource Manager-mall för DSC-tillägg](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Mer information om PowerShell DSC går du till den [PowerShell Dokumentationscenter](https://msdn.microsoft.com/powershell/dsc/overview).
* Fler funktioner som du kan hantera med hjälp av PowerShell DSC, samt mer DSC-resurser, bläddra i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
