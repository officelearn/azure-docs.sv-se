---
title: Integrera Nyckelvalv med SQLServer på virtuella Windows-datorer i Azure (klassisk) | Microsoft Docs
description: Lär dig hur du kan automatisera konfigurationen av SQL Server-kryptering för användning med Azure Key Vault. Det här avsnittet beskriver hur du använder Azure Key Vault-integrering med SQL Server skapar du virtuella datorer i den klassiska distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fd0fb1f8ac9bb0132c64c195d4cc9c86ef8edd0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399737"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurera Azure Key Vault-integrering för SQLServer på virtuella Azure-datorer (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisk](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Översikt
Det finns flera funktioner för SQL Server-kryptering, t.ex [transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [kryptering på kolumnen (r)](https://msdn.microsoft.com/library/ms173744.aspx), och [säkerhetskopia av krypteringsnyckeln](https://msdn.microsoft.com/library/dn449489.aspx). Dessa typer av kryptering måste du hantera och lagra de kryptografiska nycklar som du använder för kryptering. Tjänsten Azure Key Vault (AKV) är utformade för att förbättra säkerhet och hantering av dessa nycklar i en säker och hög tillgänglighet på. Den [SQL Server-anslutningen](http://www.microsoft.com/download/details.aspx?id=45344) aktiverar SQL Server att använda de här nycklarna från Azure Key Vault.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Om du kör SQL Server med lokala datorer, finns [steg som du kan följa för att komma åt Azure Key Vault från din lokala SQL Server-datorn](https://msdn.microsoft.com/library/dn198405.aspx). Men för SQL Server på virtuella Azure-datorer, du kan spara tid genom att använda den *Azure Key Vault-integrering* funktion. Med några Azure PowerShell-cmdletar för att aktivera den här funktionen, kan du automatisera konfigurationen krävs för en SQL-VM för att komma åt nyckelvalvet.

När den här funktionen aktiveras den automatiskt installerar SQL Server-anslutningen, konfigurerar EKM-providern för att komma åt Azure Key Vault och autentiseringsuppgifter så att du kan komma åt ditt valv. Om du tittar på stegen i ovanstående lokalt dokumentation, ser du att den här funktionen automatiserar steg 2 och 3. Det enda du behöver fortfarande göra manuellt är att skapa nyckelvalvet och nycklar. Därifrån är hela installationen av SQL-VM automatisk. När installationen har slutförts som den här funktionen, kan du köra T-SQL-uttryck för att börja kryptera dina databaser eller säkerhetskopieringar som vanligt.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurera AKV-integreringen
Använd PowerShell för att konfigurera Azure Key Vault-integrering. Följande avsnitt innehåller en översikt över de obligatoriska parametrarna och en PowerShell-exempelskript.

### <a name="install-the-sql-server-iaas-extension"></a>Installera SQL Server IaaS-tillägg
Första, [installera tillägg för SQL Server IaaS](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Förstå indataparametrar
I följande tabell visas de parametrar som krävs för att köra PowerShell-skriptet i nästa avsnitt.

| Parameter | Beskrivning | Exempel |
| --- | --- | --- |
| **$akvURL** |**Key vault-URL** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**Tjänstens huvudnamn** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Tjänstens huvudnamn hemlighet** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Namn på autentiseringsuppgifter**: AKV-integreringen skapar autentiseringsuppgifter på SQL-servern som gör att den virtuella datorn kan komma åt nyckelvalvet. Välj ett namn för autentiseringsuppgifterna. |"mycred1" |
| **$vmName** |**Namn på virtuell dator**: namnet på en tidigare skapad SQL-VM. |"myvmname" |
| **$serviceName** |**Tjänstnamnet**: tjänsten till molnet namnet som associeras med SQL-VM. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Aktivera AKV-integreringen med PowerShell
Den **ny AzureVMSqlServerKeyVaultCredentialConfig** cmdlet skapar ett konfigurationsobjekt för funktionen Azure Key Vault-integrering. Den **Set AzureVMSqlServerExtension** konfigurerar den här integreringen med den **KeyVaultCredentialSettings** parameter. Följande steg visar hur du använder dessa kommandon.

1. I Azure PowerShell först konfigurera indataparametrarna med dina specifika värden som beskrivs i föregående avsnitt i det här avsnittet. Följande skript är ett exempel.
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Använd sedan följande skript för att konfigurera och aktivera AKV-integreringen.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Tillägget SQL IaaS-agenten uppdateras VM SQL med den nya konfigurationen.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

