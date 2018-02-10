---
title: Autentiseringsuppgifter skickas till Azure med DSC | Microsoft Docs
description: "Översikt över autentiseringsuppgifter skickas på ett säkert sätt till Azure virtuella datorer med hjälp av PowerShell Desired State Configuration"
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
ms.openlocfilehash: 140ca3cc9b72afac720e5bcf1d620ac9b1b72132
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="passing-credentials-to-the-azure-dsceextension-handler"></a>Skicka autentiseringsuppgifter till Azure DSCEextension-hanteraren
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Den här artikeln beskriver Desired State Configuration-tillägget för Azure.
En översikt över hanteraren för DSC-tillägg finns på [introduktion till Azure Desired State Configuration-tillägget hanteraren](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="passing-in-credentials"></a>Skicka i autentiseringsuppgifter

Som en del av konfigurationsprocessen om du behöver konfigurera användarkonton, åtkomst till tjänster, eller installera ett program i en användarkontext. Om du vill göra detta måste du ange autentiseringsuppgifter.

DSC tillåter parametriserade konfigurationer där autentiseringsuppgifterna skickades till konfigurationen och lagras på ett säkert sätt i MOF-filer.
Azure-tillägget hanteraren förenklar hanteringen av autentiseringsuppgifter genom att tillhandahålla automatisk hantering av certifikat.

Tänk på följande DSC-konfigurationsskript som skapar ett lokalt konto med det angivna lösenordet:

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

Det är viktigt att ha *nod localhost* som en del av konfigurationen.
Om den här instruktionen saknas, fungerar inte följande steg som hanteraren tillägg specifikt söker efter nod localhost-instruktion.
Det är också viktigt att inkludera den typecast *[PsCredential]*eftersom den här specifika typen utlöser tillägget för att kryptera autentiseringsuppgifterna.

Publicera det här skriptet till blob storage:

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

## <a name="how-credentials-are-secured"></a>Hur autentiseringsuppgifter skyddas

Kör den här koden efterfrågar autentiseringsuppgifter.
När det tillhandahålls lagras i minnet kort.
När den publiceras med `Set-AzureVmDscExtension` cmdlet, den överförs via HTTPS till den virtuella datorn, där Azure lagrar den krypterade på disk, med det lokala VM-certifikatet.
Det är sedan kort dekrypteras i minnet och krypteras igen om du vill skicka till DSC.

Det här beteendet skiljer sig [använder säker konfigurationer utan tillägget hanteraren](https://msdn.microsoft.com/powershell/dsc/securemof). Azure-miljön gör att överföra konfigurationsdata på ett säkert sätt via certifikat. När du använder Hanteraren för DSC-tillägg, det finns ingen anledning att tillhandahålla $CertificatePath eller en $CertificateID / $Thumbprint post i ConfigurationData.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure DSC-tillägg-hanteraren finns [introduktion till Azure Desired State Configuration-tillägget hanteraren](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Granska de [Azure Resource Manager-mall för DSC-tillägg](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om PowerShell DSC [finns på PowerShell documentation center](https://msdn.microsoft.com/powershell/dsc/overview).

Du hittar ytterligare funktioner som du kan hantera med PowerShell DSC [Bläddra PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) mer DSC-resurser.
