---
title: Använda hanterade identiteter på en Virtuell Azure-dator för inloggning - Azure AD
description: Steg för steg instruktioner och exempel för att använda en Azure VM-hanterade identiteter för Azure resources service huvudnamn för skriptklient inloggning och resursåtkomst.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f4dc749c0254b5aa4e9ff018d2a869832de3f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547390"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Så här använder du hanterade identiteter för Azure-resurser på en Azure VM för inloggning 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller Exempel på PowerShell- och CLI-skript för inloggning med hanterade identiteter för Azure-resurstjänstens huvudnamn och vägledning om viktiga ämnen som felhantering.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda exemplen Azure PowerShell eller Azure CLI i den här artikeln måste du installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Alla exempelskript i den här artikeln förutsätter att kommandoradsklienten körs på en virtuell dator med hanterade identiteter för Azure-resurser aktiverade. Använd funktionen "Anslut" för virtuella datorer i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen](qs-configure-portal-windows-vm.md)eller en av variantartiklarna (med PowerShell, CLI, en mall eller en Azure SDK). 
> - För att förhindra fel under resursåtkomst måste den virtuella datorns hanterade identitet ges minst "Reader"-åtkomst vid lämpligt scope (den virtuella datorn eller högre) för att tillåta Azure Resource Manager-åtgärder på den virtuella datorn. Mer information finns [i Tilldela hanterade identiteter för Azure-resurser till en resurs som använder Azure-portalen.](howto-assign-access-portal.md)

## <a name="overview"></a>Översikt

Hanterade identiteter för Azure-resurser tillhandahåller ett [huvudobjekt](../develop/developer-glossary.md#service-principal-object) för tjänsten som [skapas när hanterade identiteter för Azure-resurser](overview.md#how-does-the-managed-identities-for-azure-resources-work) aktiveras på den virtuella datorn. Tjänstens huvudnamn kan få åtkomst till Azure-resurser och användas som en identitet av skript-/kommandoradsklienter för inloggning och resursåtkomst. Traditionellt, för att få tillgång till säkrade resurser under sin egen identitet, skulle en skriptklient behöva:  

   - registreras och godkännas med Azure AD som ett konfidentiellt/webbklientprogram
   - logga in under tjänstens huvudnamn med hjälp av appens autentiseringsuppgifter (som troligen är inbäddade i skriptet)

Med hanterade identiteter för Azure-resurser behöver skriptklienten inte längre göra något av dem, eftersom den kan logga in under de hanterade identiteterna för Azure-resurstjänstens huvudnamn. 

## <a name="azure-cli"></a>Azure CLI

Följande skript visar hur du:

1. Logga in på Azure AD under den virtuella datorns hanterade identitet för Azure Resources-tjänstens huvudnamn  
2. Anropa Azure Resource Manager och hämta den virtuella datorns huvud-ID för tjänsten. CLI tar hand om att hantera token förvärv / användning för dig automatiskt. Var noga med att ersätta `<VM-NAME>`din virtuella dator namn för .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Följande skript visar hur du:

1. Logga in på Azure AD under den virtuella datorns hanterade identitet för Azure Resources-tjänstens huvudnamn  
2. Anropa en Azure Resource Manager-cmdlet för att få information om den virtuella datorn. PowerShell tar hand om att hantera tokenanvändning för dig automatiskt.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Resurs-ID:er för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) för en lista över resurser som stöder Azure AD och har testats med hanterade identiteter för Azure-resurser och deras respektive resurs-ID.

## <a name="error-handling-guidance"></a>Vägledning för felhantering 

Svar som följande kan tyda på att den virtuella datorns hanterade identitet för Azure-resurser inte har konfigurerats korrekt:

- PowerShell: *Invoke-WebRequest : Det går inte att ansluta till fjärrservern*
- CLI: *MSI: Det gick `http://localhost:50342/oauth2/token` inte att hämta en token från med ett fel i HTTPConnectionPool(host='localhost", port=50342)* 

Om du får något av dessa fel går du tillbaka till den virtuella Azure-datorn i [Azure-portalen](https://portal.azure.com) och:

- Gå till sidan **Identitet** och se till att **det tilldelade systemet** är inställt på "Ja".
- Gå till sidan **Tillägg** och se till att de hanterade identiteterna för Azure-resurstillägg **(som planeras för utfasning i januari 2019)** har distribuerats.

Om något av dem är felaktigt kan du behöva distribuera om hanterade identiteter för Azure-resurser på din resurs igen eller felsöka distributionsfelet. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Hjälp av Azure-portalen](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration.

## <a name="next-steps"></a>Nästa steg

- Information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en Azure-virtuell dator med PowerShell](qs-configure-powershell-windows-vm.md)eller [Konfigurera hanterade identiteter för Azure-resurser på en Azure VM med Azure CLI](qs-configure-cli-windows-vm.md)






