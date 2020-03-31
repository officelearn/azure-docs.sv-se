---
title: Skicka autentiseringsuppgifter till Azure med önskad tillståndskonfiguration
description: Lär dig hur du på ett säkert sätt skickar autentiseringsuppgifter till virtuella Azure-datorer med Hjälp av DSC (Desired State Configuration) för PowerShell Desired State Configuration (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f7edbd0fd8791829a2d9ffaa4e7c0ee0e561cc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748966"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Skicka autentiseringsuppgifter till Azure DSCExtension-hanteraren

Den här artikeln beskriver DSC-tillägget (Desired State Configuration) för Azure. En översikt över DSC-tilläggshanteraren finns i [Introduktion till azure desired state configuration extension-hanteraren](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Skicka in autentiseringsuppgifter

Som en del av konfigurationsprocessen kan du behöva konfigurera användarkonton, komma åt tjänster eller installera ett program i en användarkontext. För att göra dessa saker måste du ange autentiseringsuppgifter.

Du kan använda DSC för att ställa in parameteriserade konfigurationer. I en parameteriserad konfiguration skickas autentiseringsuppgifter till konfigurationen och lagras säkert i .mof-filer. Azure-tilläggshanteraren förenklar hantering av autentiseringsuppgifter genom att tillhandahålla automatisk hantering av certifikat.

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

Det är viktigt att inkludera **nod localhost** som en del av konfigurationen. Tilläggshanteraren söker specifikt efter **noden localhost-satsen.** Om den här policyn saknas fungerar inte följande steg. Det är också viktigt att inkludera typecast **[PsCredential]**. Den här specifika typen utlöser tillägget för att kryptera autentiseringsuppgifterna.

Så här publicerar du skriptet i Azure Blob storage:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Så här anger du Azure DSC-tillägget och tillhandahåller autentiseringsuppgifterna:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Hur en autentiseringstillstånd skyddas

Om du kör den här koden uppmanas du att ange en autentiseringsfråga. När autentiseringsuppgifterna har angetts lagras den en kort stund i minnet. När autentiseringsuppgifterna publiceras med hjälp av **cmdleten Set-AzVMDscExtension** överförs autentiseringsuppgifterna via HTTPS till den virtuella datorn. I den virtuella datorn lagrar Azure autentiseringsuppgifterna krypterade på disk med hjälp av det lokala VM-certifikatet. Autentiseringsuppgifterna dekrypteras kort i minnet och krypteras sedan om för att skicka den till DSC.

Den här processen skiljer sig från [att använda säkra konfigurationer utan förlängningshanteraren](/powershell/scripting/dsc/pull-server/securemof). Azure-miljön ger dig ett sätt att överföra konfigurationsdata på ett säkert sätt via certifikat. När du använder DSC-tilläggshanteraren behöver du inte ange **$CertificatePath** eller en **$CertificateID**/ **$Thumbprint** posten i **ConfigurationData**.

## <a name="next-steps"></a>Nästa steg

- Få en [introduktion till Azure DSC-tilläggshanteraren](dsc-overview.md).
- Undersök [Azure Resource Manager-mallen för DSC-tillägget](dsc-template.md).
- Mer information om PowerShell DSC finns i [PowerShells dokumentationscenter](/powershell/scripting/dsc/overview/overview).
- Om du vill ha fler funktioner som du kan hantera med PowerShell DSC och mer DSC-resurser bläddrar du i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).