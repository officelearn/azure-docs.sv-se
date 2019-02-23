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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 13e4b99da6de7d9c1fb08edc80605d38ed07c6f5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727204"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Använd PowerShell för att skapa en Azure AD-app ska använda med Azure Media Services-API

Lär dig hur du använder ett PowerShell-skript för att skapa ett Azure Active Directory (Azure AD)-program och tjänstens huvudnamn för Azure Media Services-resurser.  

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Ett Azure-konto. Om du inte har ett konto kan du börja med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services-konto i Azure-portalen](media-services-portal-create-account.md).

- Azure PowerShell. Mer information finns i [hur du använder Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Skapa en Azure AD-app med hjälp av PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Mer information finns i följande artiklar:

- [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Hantera rollbaserad åtkomstkontroll med hjälp av Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Hur du manuellt konfigurerar daemon-appar med hjälp av certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Nästa steg

Kom igång med [ladda upp filer till ditt konto](media-services-portal-upload-files.md).
