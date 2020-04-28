---
title: Integrera Key Vault med en klassisk Azure-SQL Server VM
description: Lär dig hur du automatiserar konfigurationen av SQL Server kryptering för användning med Azure Key Vault. I det här avsnittet beskrivs hur du använder Azure Key Vault-integrering med SQL Server virtuella datorer som skapas i den klassiska distributions modellen.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978132"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurera Azure Key Vault-integrering för SQL Server på Azure-Virtual Machines (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisk](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Översikt
Det finns flera SQL Server krypterings funktioner, till exempel [transparent data kryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE (Column Level Encryption)](https://msdn.microsoft.com/library/ms173744.aspx)och [kryptering av säkerhets kopior](https://msdn.microsoft.com/library/dn449489.aspx). Dessa krypterings former kräver att du hanterar och lagrar de kryptografiska nycklar som du använder för kryptering. Tjänsten Azure Key Vault (AKV) är utformad för att förbättra säkerheten och hanteringen av dessa nycklar på en säker plats med hög tillgänglighet. Med [SQL Server-anslutning](https://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server använda nycklarna från Azure Key Vault.

> [!IMPORTANT] 
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Om du kör SQL Server med lokala datorer finns det [steg du kan följa för att få åtkomst till Azure Key Vault från din lokala SQL Server dator](https://msdn.microsoft.com/library/dn198405.aspx). Men för SQL Server i virtuella Azure-datorer kan du spara tid genom att använda funktionen för *Azure Key Vault-integrering* . Med några Azure PowerShell-cmdletar för att aktivera den här funktionen, kan du automatisera konfigurationen som krävs för en virtuell SQL-dator för att få åtkomst till ditt nyckel valv.

När den här funktionen är aktive rad installerar den automatiskt SQL Server-anslutning, konfigurerar EKM-providern för att få åtkomst till Azure Key Vault och skapar autentiseringsuppgiften så att du kan komma åt ditt valv. Om du har tittat på stegen i den tidigare nämnda dokumentationen kan du se att den här funktionen automatiserar steg 2 och 3. Det enda du behöver göra är att skapa nyckel valvet och nycklarna manuellt. Därifrån kommer hela installationen av den virtuella SQL-datorn att automatiseras. När den här funktionen har slutfört den här installationen kan du köra T-SQL-uttryck för att börja kryptera dina databaser eller säkerhets kopior på vanligt sätt.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurera AKV-integrering
Använd PowerShell för att konfigurera Azure Key Vault-integrering. I följande avsnitt får du en översikt över de nödvändiga parametrarna och ett exempel på PowerShell-skript.

### <a name="install-the-sql-server-iaas-extension"></a>Installera SQL Server IaaS-tillägget
Installera först [SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Förstå indataparametrarna
I följande tabell visas de parametrar som krävs för att köra PowerShell-skriptet i nästa avsnitt.

| Parameter | Beskrivning | Exempel |
| --- | --- | --- |
| **$akvURL** |**Nyckel valvets URL** |"https:\//contosokeyvault.Vault.Azure.net/" |
| **$spName** |**Tjänstens huvud namn** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Hemlighet för tjänstens huvud namn** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =" |
| **$credName** |**Namn på autentiseringsuppgifter**: AKV-integreringen skapar autentiseringsuppgifter på SQL-servern som gör att den virtuella datorn kan komma åt nyckelvalvet. Välj ett namn för autentiseringsuppgifterna. |"mycred1" |
| **$vmName** |**Namn på virtuell dator**: namnet på en tidigare skapad virtuell SQL-dator. |"myvmname" |
| **$serviceName** |**Tjänst namn**: namnet på moln tjänsten som är associerat med den virtuella SQL-datorn. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Aktivera AKV-integrering med PowerShell
Cmdlet **: en New-AzureVMSqlServerKeyVaultCredentialConfig** skapar ett konfigurations objekt för funktionen Azure Key Vault-integrering. **Set-AzureVMSqlServerExtension** konfigurerar den här integreringen med parametern **KeyVaultCredentialSettings** . Följande steg visar hur du använder dessa kommandon.

1. I Azure PowerShell konfigurerar du först indataparametrarna med dina speciella värden enligt beskrivningen i föregående avsnitt i det här avsnittet. Följande skript är ett exempel.
   
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

SQL IaaS agent-tillägget kommer att uppdatera den virtuella SQL-datorn med den nya konfigurationen.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

