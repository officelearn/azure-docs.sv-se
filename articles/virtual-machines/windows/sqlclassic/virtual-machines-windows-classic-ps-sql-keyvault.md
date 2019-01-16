---
title: Integrera Key Vault med SQLServer på Windows virtuella datorer i Azure (klassisk) | Microsoft Docs
description: Lär dig att automatisera konfigurationen av SQL Server-kryptering för användning med Azure Key Vault. Det här avsnittet förklarar hur du använder Azure Key Vault-integrering med SQL Server VM skapa i den klassiska distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
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
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3711277ca11346cf73ff37c37ef4185d8bfdf6b9
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329879"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurera Azure Key Vault-integrering för SQLServer på Azure Virtual Machines (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisk](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Översikt
Det finns flera funktioner för SQL Server-kryptering, till exempel [transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [kolumnen filnivåkryptering (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), och [kryptering](https://msdn.microsoft.com/library/dn449489.aspx). Dessa typer av kryptering måste du hantera och lagra de kryptografiska nycklarna som du använder för kryptering. Tjänsten Azure Key Vault (AKV) är utformad för att förbättra säkerhet och hantering av de här nycklarna på en säker och mycket tillgänglig plats. Den [SQL Server-anslutningen](https://www.microsoft.com/download/details.aspx?id=45344) gör det möjligt för SQL Server att använda de här nycklarna från Azure Key Vault.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Om du kör SQL Server med lokala datorer, finns [steg som du kan följa för att få åtkomst till Azure Key Vault från en lokal SQL Server-dator](https://msdn.microsoft.com/library/dn198405.aspx). Men för SQL Server i virtuella Azure-datorer, kan du spara tid genom att använda den *Azure Key Vault-integrering* funktionen. Med några Azure PowerShell-cmdletar för att aktivera den här funktionen, kan du automatisera konfigurationen som krävs för en SQL-VM till ditt nyckelvalv.

När den här funktionen aktiveras den automatiskt installerar SQL Server-anslutningen, konfigurerar EKM-providern för att komma åt Azure Key Vault och autentiseringsuppgifter så att du kan komma åt ditt valv. Du kan se att den här funktionen automatiserar steg 2 och 3 om du har tittat på stegen i den tidigare nämnda lokala-dokumentationen. Det enda du behöver fortfarande göra manuellt är att skapa nyckelvalvet och nycklar. Därifrån kan är hela konfigurationen av din SQL-VM automatisk. När den här funktionen har slutfört den här konfigurationen, kan du köra T-SQL-uttryck för att börja kryptera dina databaser eller säkerhetskopior som vanligt.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurera AKV-integreringen
Konfigurera Azure Key Vault-integrering med hjälp av PowerShell. Följande avsnitt innehåller en översikt över de obligatoriska parametrarna och sedan ett exempelskript för PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Installera SQL Server IaaS-tillägget
Först [installera SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Förstå indataparametrarna
I följande tabell visas de parametrar som krävs för att köra PowerShell-skriptet i nästa avsnitt.

| Parameter | Beskrivning | Exempel |
| --- | --- | --- |
| **$akvURL** |**Key vault-Webbadress** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**Tjänstens huvudnamn** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Hemlighet för tjänstens huvudnamn** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Namn på autentiseringsuppgift**: AKV-integreringen skapar autentiseringsuppgifter på SQL Server så att den virtuella datorn ska ha åtkomst till nyckelvalvet. Välj ett namn för autentiseringsuppgifterna. |"mycred1" |
| **$vmName** |**Namn på virtuell dator**: Namnet på en tidigare skapad SQL-VM. |"myvmname" |
| **$serviceName** |**Tjänstnamnet**: Namnet på Molntjänsten som associeras med SQL-VM. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Aktivera AKV-integreringen med PowerShell
Den **New AzureVMSqlServerKeyVaultCredentialConfig** cmdlet skapar ett konfigurationsobjekt för funktionen Azure Key Vault-integrering. Den **Set-AzureVMSqlServerExtension** konfigurerar den här integreringen med den **KeyVaultCredentialSettings** parametern. Följande steg visar hur du använder dessa kommandon.

1. I Azure PowerShell först konfigurera indataparametrarna med din specifika värden enligt beskrivningen i föregående avsnitt i det här avsnittet. Följande skript är ett exempel.
   
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

SQL IaaS Agent-tillägget kommer att uppdatera SQL-VM med den nya konfigurationen.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

