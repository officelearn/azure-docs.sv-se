---
title: Skicka autentiseringsuppgifter till Azure med Desired State Configuration
description: Lär dig hur du på ett säkert sätt skicka autentiseringsuppgifter till Azure-datorer med hjälp av PowerShell Desired State Configuration (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 52e115aa7f54eccc2be4e500c544aa38ca3bc32d
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631284"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Skicka autentiseringsuppgifter till Azure DSCExtension-hanteraren

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Den här artikeln beskriver tillägget Desired State Configuration (DSC) för Azure. En översikt över hanteraren för DSC-tillägg finns i [introduktion till Azure Desired State Configuration-tilläggshanterare](dsc-overview.md).

## <a name="pass-in-credentials"></a>Skicka in autentiseringsuppgifter

Som en del av konfigurationsprocessen, kanske du behöver du ställer in användarkonton, få åtkomst till tjänster, eller installera ett program i en användarkontext. Om du vill göra detta måste du ange autentiseringsuppgifter.

Du kan använda DSC för att ställa in parametriserade konfigurationer. I en konfiguration för parametriserade är autentiseringsuppgifter skickas i konfigurationen och lagras på ett säkert sätt i MOF-filer. Azure-tilläggshanterare förenklar hanteringen av autentiseringsuppgifter genom att tillhandahålla automatisk hantering av certifikat.

Följande DSC-konfigurationsskript skapar ett lokalt användarkonto med det angivna lösenordet:

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

Det är viktigt att inkludera **noden localhost** som en del av konfigurationen. Tillägg för hanteraren söker specifikt efter den **noden localhost** instruktionen. Följande steg fungerar inte om den här instruktionen saknas. Det är också viktigt att inkludera den typecast **[PsCredential]**. Den här specifika typen utlöser tillägget för att kryptera autentiseringsuppgifterna.

Att publicera det här skriptet till Azure Blob storage:

`Publish-AzureRmVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Ange Azure DSC-tillägg och ange autentiseringsuppgiften:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzureRmVM -Name 'example-1'

$vm = Set-AzureRmVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureRmVM
```

## <a name="how-a-credential-is-secured"></a>Hur en autentiseringsuppgift är skyddad

Kör den här koden efterfrågar autentiseringsuppgifter. När autentiseringsuppgifterna har angetts, lagras den kort i minnet. När autentiseringsuppgifterna har publicerats med hjälp av den **Set-AzureRmVMDscExtension** cmdlet, autentiseringsuppgifter skickas över HTTPS till den virtuella datorn. I den virtuella datorn lagrar autentiseringsuppgifterna krypteras på disk med hjälp av lokal VM-certifikatet i Azure. Autentiseringsuppgifterna dekrypteras kort i minnet och sedan krypteras det igen för att skicka dem till DSC.

Den här processen skiljer sig från [använder säker konfigurationer utan tillägg hanteraren](/powershell/dsc/securemof). Azure-miljön är ett sätt att överföra konfigurationsdata på ett säkert sätt via certifikat. När du använder DSC-tilläggshanterare, behöver du inte ange **$CertificatePath** eller en **$CertificateID**/ **$Thumbprint** post i **ConfigurationData**.

## <a name="next-steps"></a>Nästa steg

- Hämta en [introduktion till Azure DSC-tilläggshanterare](dsc-overview.md).
- Granska den [Azure Resource Manager-mall för DSC-tillägget](dsc-template.md).
- Mer information om PowerShell DSC, går du till den [PowerShell dokumentationscentret](/powershell/dsc/overview).
- Fler funktioner som du kan hantera med hjälp av PowerShell DSC, samt mer DSC-resurser, bläddra i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).