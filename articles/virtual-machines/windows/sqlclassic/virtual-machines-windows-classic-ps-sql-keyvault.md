---
title: Integrera Key Vault med en klassisk virtuell Azure SQL Server-dator
description: Lär dig hur du automatiserar konfigurationen av SQL Server-kryptering för användning med Azure Key Vault. I det här avsnittet beskrivs hur du använder Azure Key Vault-integrering med virtuella SQL Server-datorer som skapar i den klassiska distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: f878c6f7a59328e2f68ffbaee066bba4a5b6c898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978132"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurera Integrering av Azure Key Vault för SQL Server på virtuella Azure-datorer (Klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisk](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Översikt
Det finns flera SQL Server-krypteringsfunktioner, till exempel [transparent datakryptering (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [kryptering på kolumnnivå (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)och [kryptering för säkerhetskopiering](https://msdn.microsoft.com/library/dn449489.aspx). Dessa krypteringsformer kräver att du hanterar och lagrar de kryptografiska nycklar som du använder för kryptering. Azure Key Vault (AKV) -tjänsten är utformad för att förbättra säkerheten och hanteringen av dessa nycklar på en säker och högtillgänglig plats. [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) gör det möjligt för SQL Server att använda dessa nycklar från Azure Key Vault.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Om du kör SQL Server med lokala datorer finns det [steg du kan följa för att komma åt Azure Key Vault från din lokala SQL Server-dator](https://msdn.microsoft.com/library/dn198405.aspx). Men för SQL Server i virtuella Azure-datorer kan du spara tid med hjälp av azure *key vault-integreringsfunktionen.* Med några Azure PowerShell-cmdletar för att aktivera den här funktionen kan du automatisera konfigurationen som krävs för en VIRTUELL SQL för att komma åt nyckelvalvet.

När den här funktionen är aktiverad installeras SQL Server Connector automatiskt, EKM-providern konfigureras för åtkomst till Azure Key Vault och skapar autentiseringsuppgifterna så att du kan komma åt ditt valv. Om du har tittat på stegen i den tidigare nämnda lokala dokumentationen kan du se att den här funktionen automatiserar steg 2 och 3. Det enda du fortfarande skulle behöva göra manuellt är att skapa nyckeln valv och nycklar. Därifrån är hela installationen av din SQL VM automatiserad. När den här funktionen har slutfört den här installationen kan du köra T-SQL-uttryck för att börja kryptera dina databaser eller säkerhetskopior som vanligt.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurera AKV-integrering
Använd PowerShell för att konfigurera Integrering av Azure Key Vault. Följande avsnitt innehåller en översikt över de parametrar som krävs och sedan ett exempel på PowerShell-skript.

### <a name="install-the-sql-server-iaas-extension"></a>Installera IaaS-tillägget för SQL Server
Installera först [SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Förstå indataparametrarna
I följande tabell visas de parametrar som krävs för att köra PowerShell-skriptet i nästa avsnitt.

| Parameter | Beskrivning | Exempel |
| --- | --- | --- |
| **$akvURL** |**URL:en för nyckelvalv** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Tjänstens huvudnamn** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Tjänstens huvudmanshemlighet** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Namn på autentiseringsuppgifter**: AKV-integreringen skapar autentiseringsuppgifter på SQL-servern som gör att den virtuella datorn kan komma åt nyckelvalvet. Välj ett namn för autentiseringsuppgifterna. |"mycred1" |
| **$vmName** |**Namn på virtuell dator:** Namnet på en tidigare skapad SQL VM. |"myvmname" |
| **$serviceName** |**Tjänstnamn**: Namnet på molntjänsten som är associerat med SQL VM. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Aktivera AKV-integrering med PowerShell
**Cmdlet new-AzureVMSqlServerKeyVaultCredentialConfig** skapar ett konfigurationsobjekt för azure key vault-integreringsfunktionen. **Set-AzureVMSqlServerExtension** konfigurerar den här integreringen med parametern **KeyVaultCredentialSettings.** Följande steg visar hur du använder dessa kommandon.

1. I Azure PowerShell konfigurerar du först indataparametrarna med dina specifika värden enligt beskrivningen i föregående avsnitt i det här avsnittet. Följande skript är ett exempel.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Använd sedan följande skript för att konfigurera och aktivera AKV-integrering.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS-agenttillägget uppdaterar SQL VM med den nya konfigurationen.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

