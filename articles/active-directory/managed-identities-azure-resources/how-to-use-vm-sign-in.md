---
title: Använda hanterade identiteter på en virtuell Azure-dator för inloggning – Azure ADV
description: Stegvisa instruktioner och exempel på hur du använder en Azure VM-hanterade identiteter för Azure-tjänstens huvud namn för inloggnings-och resurs åtkomst till skript klienter.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 02815003bf5167a73c1e2362d9270deb867c84b7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999418"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Använda hanterade identiteter för Azure-resurser på en virtuell Azure-dator för inloggning 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller exempel på PowerShell-och CLI-skript för inloggning med hanterade identiteter för Azure Resources-tjänstens huvud namn och rikt linjer för viktiga ämnen som fel hantering.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell-eller Azure CLI-exemplen i den här artikeln måste du installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Alla exempel skript i den här artikeln förutsätter att kommando rads klienten körs på en virtuell dator med hanterade identiteter för Azure-resurser aktiverade. Använd funktionen för att ansluta till den virtuella datorn i Azure Portal för att fjärrans luta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md)eller en av variant-artiklarna (med POWERSHELL, CLI, mall eller Azure SDK). 
> - För att förhindra fel under resurs åtkomst måste den virtuella datorns hanterade identitet ges minst "läsare"-åtkomst i lämpligt omfång (den virtuella datorn eller högre) för att tillåta Azure Resource Manager åtgärder på den virtuella datorn. Mer information finns i [tilldela hanterade identiteter för Azure-resurser åtkomst till en resurs med hjälp av Azure Portal](howto-assign-access-portal.md) .

## <a name="overview"></a>Översikt

Hanterade identiteter för Azure-resurser tillhandahåller ett [huvud namn för tjänsten](../develop/developer-glossary.md#service-principal-object) som [skapas när du aktiverar hanterade identiteter för Azure-resurser](overview.md) på den virtuella datorn. Tjänstens huvud namn kan ges åtkomst till Azure-resurser och används som identitet av skript-/kommando rads klienter för inloggning och resurs åtkomst. För att få åtkomst till skyddade resurser under sin egen identitet skulle en skript klient behöva:  

   - vara registrerad och meddelad med Azure AD som ett konfidentiellt/webb klient program
   - Logga in under tjänstens huvud namn med hjälp av appens autentiseringsuppgifter (som troligen är inbäddade i skriptet)

Med hanterade identiteter för Azure-resurser behöver skript klienten inte längre göra något, eftersom den kan logga in under hanterade identiteter för Azure Resources-tjänstens huvud namn. 

## <a name="azure-cli"></a>Azure CLI

Följande skript visar hur du:

1. Logga in på Azure AD under den virtuella datorns hanterade identitet för Azure Resources-tjänstens huvud namn  
2. Anropa Azure Resource Manager och hämta den virtuella datorns ID för tjänstens huvud namn. CLI sköter hanteringen av hämtning/användning av token automatiskt. Var noga med att ersätta namnet på den virtuella datorn för `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Följande skript visar hur du:

1. Logga in på Azure AD under den virtuella datorns hanterade identitet för Azure Resources-tjänstens huvud namn  
2. Anropa en Azure Resource Manager-cmdlet för att hämta information om den virtuella datorn. PowerShell tar hand om att hantera token-användning automatiskt.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) för en lista över resurser som stöder Azure AD och som har testats med hanterade identiteter för Azure-resurser och deras respektive resurs-ID.

## <a name="error-handling-guidance"></a>Vägledning för fel hantering 

Svar som följande kan tyda på att den virtuella datorns hanterade identitet för Azure-resurser inte har kon figurer ATS korrekt:

- PowerShell: *Invoke-WebRequest: det går inte att ansluta till fjärrservern*
- CLI: *MSI: det gick inte att hämta en token från `http://localhost:50342/oauth2/token` med felet ' HTTPConnectionPool (Host = ' localhost ', Port = 50342)* 

Om du får ett av dessa fel kan du gå tillbaka till den virtuella Azure-datorn i [Azure Portal](https://portal.azure.com) och:

- Gå till sidan **identitet** och se till att **tilldelat system** är inställt på "Ja".
- Gå till sidan **tillägg** och se till att hanterade identiteter för Azures resurs tillägg **(planerat för utfasning i januari 2019)** har distribuerats.

Om något är felaktigt kan du behöva distribuera hanterade identiteter för Azure-resurser på resursen igen eller felsöka distributions fel. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfigurationen.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera hanterade identiteter för Azure-resurser på en virtuell Azure-dator, se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av PowerShell](qs-configure-powershell-windows-vm.md)eller [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator](qs-configure-cli-windows-vm.md)






