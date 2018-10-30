---
title: Använd PowerShell för att skapa en Azure AD-app för att komma åt Azure Media Services-API | Microsoft Docs
description: Lär dig hur du använder PowerShell för att skapa en app i Azure Active Directory (Azure AD) och konfigurera den för att komma åt Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: bd7938fb600a1a0cd65b89fde6ca25135306b76d
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232650"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Använd PowerShell för att skapa en Azure AD-app ska använda med Azure Media Services-API

Lär dig hur du använder ett PowerShell-skript för att skapa ett Azure Active Directory (Azure AD)-program och tjänstens huvudnamn för Azure Media Services-resurser.  

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett konto kan du börja med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services-konto i Azure-portalen](media-services-portal-create-account.md).
- Azure PowerShell-version 0.8.8 eller en senare version. Mer information finns i [hur du använder Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
- Azure Resource Manager-cmdletar.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Skapa en Azure AD-app med hjälp av PowerShell  

```powershell
Connect-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Mer information finns i följande artiklar:

- [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Hantera rollbaserad åtkomstkontroll med hjälp av Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Hur du manuellt konfigurerar daemon-appar med hjälp av certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Nästa steg

Kom igång med [ladda upp filer till ditt konto](media-services-portal-upload-files.md).
