---
title: Azure Cloud Shell funktioner | Microsoft Docs
description: Översikt över funktioner i Azure Cloud Shell
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 1354f7befd8c38537a555e17733f431dd488cf60
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742058"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funktioner & verktyg för Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell körs på `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funktioner

### <a name="secure-automatic-authentication"></a>Säker automatisk autentisering

Cloud Shell säkert och autentiserar automatiskt konto åtkomst för Azure CLI och Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME beständighet mellan sessioner

För att spara filer över flera sessioner, Cloud Shell vägleder dig genom att ansluta en Azure-filresurs vid första starten.
När den har slutförts ansluts Cloud Shell automatiskt lagringen `$HOME\clouddrive`(monterad som) för alla kommande sessioner.
Dessutom sparas din `$HOME` katalog som en. img-fil i Azure-filresursen.
Filer utanför `$HOME` och dator status behålls inte mellan sessioner. Använd metod tips när du lagrar hemligheter som SSH-nycklar. Tjänster som [Azure Key Vault har självstudier för installation](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Läs mer om att spara filer i Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-enhet (Azure:)

PowerShell i Cloud Shell startar dig i Azure Drive (`Azure:`).
Med Azure-enheten kan du enkelt upptäcka och navigera i Azure-resurser, till exempel Compute, nätverk, lagring osv. liknar navigering i fil systemet.
Du kan fortsätta att använda de bekanta [Azure PowerShell](https://docs.microsoft.com/powershell/azure) -cmdletarna för att hantera dessa resurser oavsett vilken enhet du befinner dig i.
Alla ändringar som görs i Azure-resurserna, antingen direkt i Azure Portal eller genom Azure PowerShell-cmdletar, återspeglas i Azure-enheten.  Du kan köra `dir -Force` för att uppdatera dina resurser.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Hantera Exchange Online

PowerShell i Cloud Shell innehåller en privat version av Exchange Online-modulen.  Kör `Connect-EXOPSSession` för att hämta dina Exchange-cmdletar.

![](media/features-powershell/exchangeonline.png)

 Kör `Get-Command -Module tmp_*`
> [!NOTE]
> Modulnamnet ska börja med `tmp_`, om du har installerat moduler med samma prefix, kommer deras cmdlets också att placeras på skärmen. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Djupgående integrering med verktyg med öppen källkod

Cloud Shell innehåller förkonfigurerad autentisering för verktyg med öppen källkod, till exempel terraform, Ansible och chefs INSPEC. Prova genom gångs exemplet.

## <a name="tools"></a>Verktyg

|Category   |Namn   |
|---|---|
|Linux-verktyg            |bash<br> zsh<br> f<br> tmux<br> fram<br>               |
|Azure-verktyg            |[Azure CLI](https://github.com/Azure/azure-cli) och [Azure klassisk CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Text redigerare           |kod (Cloud Shell redigerare)<br> vim<br> Nano<br> emacs    |
|Käll kontroll         |git                    |
|Bygg verktyg            |Försäkra<br> Maven<br> npm<br> –         |
|Containrar             |[Docker-dator](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Databaser              |MySQL-klient<br> PostgreSql-klient<br> [SQLCMD-verktyg](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Annat                  |iPython-klient<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Puppet-bult](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Stöd för språk

|Språk   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Gå till         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.2.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 och 3,5 (standard)|

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell snabb start](quickstart.md) <br>
[PowerShell i Cloud Shell snabb start](quickstart-powershell.md) <br>
[Lär dig mer om Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Läs mer om Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
