---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 2c7d312910c6d38c54b291da34bfb827246c7dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504296"
---
## <a name="prepare-for-akv-integration"></a>Förbered för AKV-integrering
Om du vill använda Azure Key Vault-integrering för att konfigurera din VIRTUELLA SQL Server finns det flera förutsättningar: 

1. [Installera Azure Powershell](#install)
2. [Skapa en Active Directory i Azure](#register)
3. [Skapa ett nyckelvalv](#createkeyvault)

I följande avsnitt beskrivs dessa förutsättningar och den information du behöver samla in för att senare köra PowerShell-cmdletar.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a>Installera Azure PowerShell
Kontrollera att du har installerat den senaste Azure PowerShell-modulen. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a>Registrera ett program i Din Azure Active Directory

Först måste du ha en [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) i din prenumeration. Bland många fördelar, Detta gör att du kan bevilja behörighet till din nyckel valv för vissa användare och program.

Registrera sedan en ansökan hos AAD. Detta ger dig ett tjänsthuvudnamnskonto som har åtkomst till ditt nyckelvalv, som din virtuella dator behöver. I azure key vault-artikeln hittar du de här stegen i avsnittet [Registrera ett program med Azure Active Directory,](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) eller så kan du se stegen med skärmbilder i avsnittet Hämta en identitet för **programavsnittet** i det [här blogginlägget](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Innan du slutför de här stegen måste du samla in följande information under den här registreringen som behövs senare när du aktiverar Azure Key Vault-integrering på din SQL VM.

* När programmet har lagts till hittar du **program-ID:t** (kallas även AAD ClientID eller AppID) på bladet **Registrerad app.**
    Program-ID:t tilldelas senare till parametern **$spName** (Tjänsthuvudnamn) i PowerShell-skriptet för att aktivera Azure Key Vault-integrering.

   ![Program-ID:t](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Under de här stegen när du skapar nyckeln kopierar du hemligheten för nyckeln som visas i följande skärmbild. Den här nyckelhemligheten tilldelas senare till parametern **$spSecret** (Service Principal secret) i PowerShell-skriptet.

   ![AAD hemlighet](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Program-ID och hemligheten kommer också att användas för att skapa en autentiseringsidentiella i SQL Server.

* Du måste auktorisera det nya program-ID:t (eller klient-ID) för att ha följande åtkomstbehörigheter: **get**, **wrapKey**, **unwrapKey**. Detta görs med cmdleten [Set-AzKeyVaultAccessPolicy.](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Mer information finns i [översikt över Azure Key Vault](../articles/key-vault/key-vault-overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a>Skapa ett nyckelvalv
För att kunna använda Azure Key Vault för att lagra nycklarna som du använder för kryptering i den virtuella datorn behöver du åtkomst till ett nyckelvalv. Om du inte redan har konfigurerat nyckelvalvet skapar du ett genom att följa stegen i artikeln [Komma igång med Azure Key Vault.](../articles/key-vault/key-vault-overview.md) Innan du slutför de här stegen finns det en del information som du behöver samla in under den här installationen som behövs senare när du aktiverar Azure Key Vault-integrering på din SQL VM.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

När du kommer till steget Skapa ett nyckelvalv noterar du egenskapen returnerade **vaultUri,** som är url:en för nyckelvalvet. I exemplet i det steget, som visas nedan, är nyckelvalvets namn ContosoKeyVault, därför skulle nyckelvalvets URL vara https://contosokeyvault.vault.azure.net/.

Nyckeln valvets URL tilldelas senare till **den $akvURL** parametern i PowerShell-skriptet för att aktivera Azure Key Vault-integrering.

När nyckelvalvet har skapats måste vi lägga till en nyckel till nyckelvalvet, den här nyckeln refereras när vi skapar en asymmetrisk nyckel skapa i SQL Server senare.
