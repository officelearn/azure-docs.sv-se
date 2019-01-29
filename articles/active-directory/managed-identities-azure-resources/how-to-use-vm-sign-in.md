---
title: Hur du använder hanterade identiteter för Azure-resurser på en Azure virtuell dator för inloggning
description: Steg för steg-anvisningar och exempel för att använda en Azure-dator hanterade identiteter för Azure-resurser tjänstens huvudnamn för skriptet klienten inloggning och åtkomst till.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: priyamo
ms.openlocfilehash: d5f07fefc18e87f1ffb760defe0d5c256c6b338e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55186798"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Hur du använder hanterade identiteter för Azure-resurser på en Azure virtuell dator för inloggning 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller PowerShell och CLI-exempelskript för att logga in med hjälp av hanterade identiteter för tjänstobjektet i Azure-resurser och vägledning om viktiga ämnen som felhantering.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell eller Azure CLI-exempel i den här artikeln, måste du installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Alla exempelskriptet i den här artikeln förutsätter att kommandoradsklienten körs på en virtuell dator med hanterade identiteter för Azure-resurser aktiverat. Använd funktionen ”ansluta” virtuell dator i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i [konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure portal](qs-configure-portal-windows-vm.md), eller någon av varianten artiklar (med PowerShell, CLI, en mall eller en Azure SDK). 
> - För att förhindra fel vid åtkomst till den Virtuella datorns hanterad identitet måste ges minst ”läsare” åtkomst till lämplig definitionsområdet (den virtuella datorn eller högre) så att Azure Resource Manager-åtgärder på den virtuella datorn. Se [tilldela hanterade identiteter för Azure-resurser åtkomst till en resurs med hjälp av Azure portal](howto-assign-access-portal.md) mer information.

## <a name="overview"></a>Översikt

Hanterade identiteter för Azure-resurser tillhandahåller en [tjänstens huvudnamnsobjekt](../develop/developer-glossary.md#service-principal-object) , vilket är [skapas vid aktivering av hanterade identiteter för Azure-resurser](overview.md#how-does-it-work) på den virtuella datorn. Tjänstens huvudnamn kan få åtkomst till Azure-resurser, och används som en identitet av skript/kommandoradsbaserad-Command-Line klienter för inloggning och åtkomst till resurser. För att komma åt skyddade resurser under sin egen identitet traditionellt har måste en skript-klient du:  

   - registrera och godkänt villkoren med Azure AD som ett konfidentiellt/web-klientprogram
   - logga in under dess huvudnamn för tjänsten med hjälp av appens autentiseringsuppgifter (som är sannolikt inbäddad i skriptet)

Med hanterade identiteter för Azure-resurser, skript-klienten inte längre behöver göra något, eftersom det kan logga in under hanterade identiteter för tjänstens huvudnamn för Azure-resurser. 

## <a name="azure-cli"></a>Azure CLI

Följande skript visar hur du:

1. Logga in på Azure AD under den Virtuella datorns hanterad identitet för tjänstens huvudnamn för Azure-resurser  
2. Anropa Azure Resource Manager och få den Virtuella datorns service principal-ID. CLI tar hand om hanteringen token förvärv/använda automatiskt åt dig. Se till att ersätta ditt VM-namn för `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Följande skript visar hur du:

1. Logga in på Azure AD under den Virtuella datorns hanterad identitet för tjänstens huvudnamn för Azure-resurser  
2. Anropa en Azure Resource Manager-cmdlet för att få information om den virtuella datorn. PowerShell tar hand om hanteringen token används åt dig automatiskt.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Resurs-ID: N för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](services-support-msi.md#azure-services-that-support-azure-ad-authentication) för en lista över resurser som stöder Azure AD och har testats med hanterade identiteter för Azure-resurser och deras respektive resurs-ID.

## <a name="error-handling-guidance"></a>Vägledning för hantering av fel 

Svar som följande kan tyda på att den Virtuella datorns hanterad identitet för Azure-resurser inte har konfigurerats korrekt:

- PowerShell: *Invoke-WebRequest: Det går inte att ansluta till fjärrservern*
- CLI: *MSI: Det gick inte att hämta en token från ”http://localhost:50342/oauth2/token” med ett fel av ' HTTPConnectionPool (värd = localhost, port = 50342)* 

Om du får något av dessa fel, gå tillbaka till den virtuella Azure-datorn i den [Azure-portalen](https://portal.azure.com) och:

- Gå till den **identitet** sidan och se till att **systemtilldelad** är inställd på ”Yes”.
- Gå till den **tillägg** sidan och se till att hanterade identiteter för Azure-resurser tillägget **(planerad för utfasning i januari 2019)** har distribuerats.

Om något är fel, kan du behöva distribuera om de hanterade identiteterna för Azure-resurser på resursen igen eller felsöka distributionsfel. Se [konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration.

## <a name="next-steps"></a>Nästa steg

- För att aktivera hanterade identiteter för Azure-resurser på en Azure VM, se [konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av PowerShell](qs-configure-powershell-windows-vm.md), eller [konfigurera hanterade identiteter för Azure-resurser på en Azure VM med Azure CLI](qs-configure-cli-windows-vm.md)






