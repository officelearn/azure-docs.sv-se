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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ff7f8bc27d358c667b10c0bd3383e78b20494303
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64680122"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Använd PowerShell för att skapa en Azure AD-app ska använda med Azure Media Services-API

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Lär dig hur du använder ett PowerShell-skript för att skapa ett Azure Active Directory (Azure AD)-program och tjänstens huvudnamn för Azure Media Services-resurser.  

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett Azure-konto. Om du inte har ett konto kan du börja med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services-konto i Azure-portalen](media-services-portal-create-account.md).

- Azure PowerShell. Mer information finns i [hur du använder Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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
