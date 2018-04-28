---
title: Hur du använder hanterade tjänstidentiteten en Azure VM för inloggning
description: Stegvisa anvisningar och exempel för med en Azure VM MSI tjänstens huvudnamn för skript-klient loggar in och resurs för åtkomst till.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: bae2d1c823c606cdb3202f2af1bdc4d577126868
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Hur du använder en Azure VM hanterade tjänsten identitet (MSI) för inloggning 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller exempel på PowerShell och CLI skript för att logga in med ett MSI tjänstens huvudnamn och vägledning om viktiga ämnen, till exempel felhantering.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell eller Azure CLI exemplen i den här artikeln bör du installera den senaste versionen av [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) eller [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Alla exempelskript i den här artikeln förutsätter kommandoradsverktyget klienten körs på en MSI-aktiverad virtuell dator. Använd funktionen ”ansluta” VM i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](qs-configure-portal-windows-vm.md), eller en variant artiklar (med PowerShell, CLI, en mall eller en Azure SDK). 
> - För att förhindra fel vid åtkomst till företagsresurser MSI för den virtuella datorn måste ges minst ”Reader” komma åt på en lämplig omfattning (den virtuella datorn eller högre) så att Azure Resource Manager-åtgärder på den virtuella datorn. Se [tilldelar en hanterad tjänst identitet (MSI) åtkomst till en resurs med hjälp av Azure portal](howto-assign-access-portal.md) mer information.

## <a name="overview"></a>Översikt

En MSI som innehåller en [tjänstens huvudnamn objekt](../develop/active-directory-dev-glossary.md#service-principal-object) , vilket är [skapas vid aktivering av MSI](overview.md#how-does-it-work) på den virtuella datorn. Tjänstens huvudnamn kan få åtkomst till Azure-resurser och används som en identitet av skriptet/Kommandotolken-Command-Line klienter för inloggning och åtkomst till företagsresurser. För att komma åt skyddade resurser under sin egen identitet traditionellt måste en skript-klient du:  

   - registreras och godkänt med Azure AD som ett konfidentiellt/klienten webbprogram
   - logga in under dess huvudnamn för tjänsten med hjälp av appens autentiseringsuppgifter (som är sannolikt inbäddad i skriptet)

Med MSI, skript-klienten inte längre behöver göra något, eftersom den kan logga in under MSI tjänstens huvudnamn. 

## <a name="azure-cli"></a>Azure CLI

Följande skript visar hur du:

1. logga in på Azure AD under den virtuella datorn MSI-tjänstens huvudnamn  
2. Anropa Azure Resource Manager och hämta den virtuella datorn service principal-ID. CLI hand tar om hanteringen token förvärv/Använd åt dig automatiskt. Se till att ersätta ditt namn på virtuell dator för `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Följande skript visar hur du:

1. logga in på Azure AD under den virtuella datorn MSI-tjänstens huvudnamn  
2. Anropa en Azure Resource Manager-cmdlet för att få information om den virtuella datorn. PowerShell hand tar om hanteringen token används åt dig automatiskt.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](overview.md#azure-services-that-support-azure-ad-authentication) en lista över resurser som stöder Azure AD och har testats med MSI och deras respektive resurs-ID.

## <a name="error-handling-guidance"></a>Riktlinjer för hantering av fel 

Svar till exempel följande kan tyda på att den virtuella datorn MSI inte har konfigurerats korrekt:

- PowerShell: *Invoke-WebRequest: Det gick inte att ansluta till fjärrservern*
- CLI: *MSI: Det gick inte att hämta en token från 'http://localhost:50342/oauth2/token' med ett fel av ' HTTPConnectionPool (värden = localhost, port = 50342)* 

Om något av dessa fel, gå tillbaka till den virtuella Azure-datorn i den [Azure-portalen](https://portal.azure.com) och:

- Gå till den **Configuration** sidan och se till att ”hanterade tjänstidentiteten” är inställt på ”Ja”.
- Gå till den **tillägg** sidan och kontrollera MSI-tillägget har distribuerats.

Om något är fel, kan du behöva distribuera om MSI-filerna på din resurs eller felsöka distributionen misslyckades. Se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration.

## <a name="related-content"></a>Relaterat innehåll

- För att aktivera MSI på en Azure VM, se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av PowerShell](qs-configure-powershell-windows-vm.md), eller [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure CLI](qs-configure-cli-windows-vm.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.








