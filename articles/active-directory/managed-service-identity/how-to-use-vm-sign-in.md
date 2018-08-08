---
title: Hur du använder en Azure VM Managed Service Identity för inloggning
description: Stegvisa åtkomst anvisningar och exempel för att använda en virtuell MSI-dator för Azure-tjänstens huvudnamn för skriptet klienten logga in och resurs.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 205938bbf615face0768028717a333c13c1fafa1
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590321"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Hur du använder en Azure VM hanterad tjänstidentitet (MSI) för inloggning 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller PowerShell och CLI-exempelskript för att logga in med hjälp av en MSI-tjänstens huvudnamn och vägledning om viktiga ämnen som felhantering.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell eller Azure CLI-exempel i den här artikeln, måste du installera den senaste versionen av [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) eller [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Alla exempelskriptet i den här artikeln förutsätter att kommandoraden klienten körs på en MSI-aktiverad virtuell dator. Använd funktionen ”ansluta” virtuell dator i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns i [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md), eller någon av varianten artiklar (med PowerShell, CLI, en mall eller ett Azure SDK). 
> - För att förhindra fel vid åtkomst till den Virtuella datorns-MSI måste ges minst ”läsare” åtkomst till lämplig definitionsområdet (den virtuella datorn eller högre) så att Azure Resource Manager-åtgärder på den virtuella datorn. Se [ge en hanterad tjänstidentitet (MSI)-åtkomst till en resurs med hjälp av Azure portal](howto-assign-access-portal.md) mer information.

## <a name="overview"></a>Översikt

En MSI som innehåller en [tjänstens huvudnamnsobjekt](../develop/developer-glossary.md#service-principal-object) , vilket är [skapas vid när du aktiverar MSI](overview.md#how-does-it-work) på den virtuella datorn. Tjänstens huvudnamn kan få åtkomst till Azure-resurser, och används som en identitet av skript/kommandoradsbaserad-Command-Line klienter för inloggning och åtkomst till resurser. För att komma åt skyddade resurser under sin egen identitet traditionellt har måste en skript-klient du:  

   - registrera och godkänt villkoren med Azure AD som ett konfidentiellt/web-klientprogram
   - logga in under dess huvudnamn för tjänsten med hjälp av appens autentiseringsuppgifter (som är sannolikt inbäddad i skriptet)

Med MSI, skript-klienten inte längre behöver göra något, eftersom det kan logga in under MSI tjänstens huvudnamn. 

## <a name="azure-cli"></a>Azure CLI

Följande skript visar hur du:

1. Logga in på Azure AD under den Virtuella datorns MSI-tjänstens huvudnamn  
2. Anropa Azure Resource Manager och få den Virtuella datorns service principal-ID. CLI tar hand om hanteringen token förvärv/använda automatiskt åt dig. Se till att ersätta ditt VM-namn för `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Följande skript visar hur du:

1. Logga in på Azure AD under den Virtuella datorns MSI-tjänstens huvudnamn  
2. Anropa en Azure Resource Manager-cmdlet för att få information om den virtuella datorn. PowerShell tar hand om hanteringen token används åt dig automatiskt.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Resurs-ID: N för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](services-support-msi.md#azure-services-that-support-azure-ad-authentication) en lista över resurser som stöder Azure AD och har testats med MSI och deras respektive resurs-ID.

## <a name="error-handling-guidance"></a>Vägledning för hantering av fel 

Svar som följande kan tyda på att den Virtuella datorns MSI inte har konfigurerats korrekt:

- PowerShell: *Invoke-WebRequest: Det gick inte att ansluta till fjärrservern*
- CLI: *MSI: Det gick inte att hämta en token från ”http://localhost:50342/oauth2/token” med ett fel av ' HTTPConnectionPool (värd = localhost, port = 50342)* 

Om du får något av dessa fel, gå tillbaka till den virtuella Azure-datorn i den [Azure-portalen](https://portal.azure.com) och:

- Gå till den **Configuration** sidan och se till att ”hanterad tjänstidentitet” anges till ”Yes”.
- Gå till den **tillägg** sidan och se till att MSI-tillägget har distribuerats.

Om något är fel, kan du behöva distribuera om MSI på resursen igen eller felsöka distributionsfel. Se [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration.

## <a name="related-content"></a>Relaterat innehåll

- För att aktivera MSI på en Azure VM, se [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med hjälp av PowerShell](qs-configure-powershell-windows-vm.md), eller [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure CLI](qs-configure-cli-windows-vm.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.








