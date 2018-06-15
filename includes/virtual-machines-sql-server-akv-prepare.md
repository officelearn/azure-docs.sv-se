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
ms.openlocfilehash: 19be449528481b4e35cad4418f82f2250917966b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32787424"
---
## <a name="prepare-for-akv-integration"></a>Förbereda för AKV-integreringen
Om du vill använda Azure Key Vault-integrering för att konfigurera SQL Server-VM, finns det flera förutsättningar: 

1. [Installera Azure Powershell](#install)
2. [Skapa en Azure Active Directory](#register)
3. [Skapa ett nyckelvalv](#createkeyvault)

I följande avsnitt beskrivs dessa krav och den information du behöver samla in för att senare Kör PowerShell-cmdlets.

### <a id="install"></a> Installera Azure PowerShell
Kontrollera att du har installerat den senaste Azure PowerShell SDK. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a id="register"></a> Registrera ett program i Azure Active Directory

Måste du ha en [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) i din prenumeration. Bland många fördelar kan du ge behörighet till nyckelvalvet för vissa användare och program.

Registrera ett program med AAD. Detta ger ett huvudnamn för tjänsten-konto som har åtkomst till nyckelvalvet, vilket den virtuella datorn behöver. Azure Key Vault-artikeln hittar de här stegen i den [registrera ett program med Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register) avsnitt eller du kan se steg med skärmdumpar i den **hämta en identitet för programavsnittet**  av [i det här blogginlägget](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Innan du slutför stegen som du behöver samla in följande information under registreringen som behövs för senare när du aktiverar Azure Key Vault-integrering på din SQL-VM.

* När programmet har lagts till, finns det **program-ID** på den **registrerad app** bladet.
    Program-ID tilldelas senare till den **$spName** (Service Principal name)-parametern i PowerShell-skript för att aktivera Azure Key Vault-integrering.

   ![Program-ID:t](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Under de här stegen när du skapar din nyckel för att kopiera hemligheten för din nyckel som visas i följande skärmbild. Den här nyckeln hemlig tilldelas senare till den **$spSecret** (Service Principal hemliga) parameter i PowerShell-skript.

   ![AAD hemlighet](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Program-ID och hemligheten som också används för att skapa en autentiseringsuppgift i SQL Server.

* Du måste auktorisera den här nya klient-ID har följande åtkomstbehörighet: **kryptera**, **dekryptera**, **wrapKey**, **unwrapKey**, **logga**, och **Kontrollera**. Detta görs med den [Set AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) cmdlet. Mer information finns i [tillåta program att använda nyckel eller hemlighet](../articles/key-vault/key-vault-get-started.md#authorize).

### <a id="createkeyvault"></a> Skapa ett nyckelvalv
För att kunna använda Azure Key Vault för lagring av nycklar som ska användas för kryptering i den virtuella datorn behöver åtkomst till en nyckelvalvet. Om du redan inte har lagt upp ditt nyckelvalv kan skapa en genom att följa stegen i den [komma igång med Azure Key Vault](../articles/key-vault/key-vault-get-started.md) artikel. Innan du utför de här stegen finns information du behöver samla in under den här uppsättningen upp som behövs senare när du aktiverar Azure Key Vault-integrering på din SQL-VM.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

När du kommer att skapa ett nyckelvalv steg, notera den returnerade **vaultUri** -egenskap som är key vault-URL. I exemplet i det steg som visas nedan, namnet på nyckelvalv är ContosoKeyVault, därför nyckelvalvet URL: en skulle vara https://contosokeyvault.vault.azure.net/.

Key vault-URL är tilldelad senare till den **$akvURL** parameter i PowerShell-skript för att aktivera Azure Key Vault-integrering.

När nyckelvalvet skapas, vi behöver lägga till en nyckel i nyckelvalvet, som den här nyckeln anges när du skapar vi skapa en asymmetrisk nyckel senare i SQL Server.
