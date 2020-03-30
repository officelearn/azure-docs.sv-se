---
title: Azure Cloud Shell-funktioner | Microsoft-dokument
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
ms.openlocfilehash: 0aa71e4b78df8087093f183b146c525d2a8a0f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366248"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funktioner & verktyg för Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell `Ubuntu 16.04 LTS`körs på .

## <a name="features"></a>Funktioner

### <a name="secure-automatic-authentication"></a>Säker automatisk autentisering

Cloud Shell autentiserar kontoåtkomst på ett säkert och automatiskt sätt för Azure CLI och Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME envishet mellan sessioner

För att spara filer över sessioner går Cloud Shell igenom att bifoga en Azure-filresurs vid första lanseringen.
När det är klart kommer Cloud Shell automatiskt `$HOME\clouddrive`att ansluta din lagring (monterad som) för alla framtida sessioner.
Dessutom sparas `$HOME` katalogen som en .img i din Azure File-resurs.
Filer utanför `$HOME` och datortillstånd sparas inte mellan sessioner. Använd metodtips när du lagrar hemligheter som SSH-nycklar. Tjänster som [Azure Key Vault har självstudier för installation](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Läs mer om beständiga filer i Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-enhet (Azure:)

PowerShell i Cloud Shell startar`Azure:`dig i Azure-enhet ( ).
Azure-enheten möjliggör enkel identifiering och navigering av Azure-resurser som Beräkning, Nätverk, Lagring etc. som liknar filsystemnavigering.
Du kan fortsätta att använda de välbekanta [Azure PowerShell-cmdlets för](https://docs.microsoft.com/powershell/azure) att hantera dessa resurser oavsett vilket enhet du befinner dig i.
Alla ändringar som görs i Azure-resurserna, antingen gjorda direkt i Azure-portalen eller via Azure PowerShell-cmdlets, återspeglas i Azure-enheten.  Du kan `dir -Force` köra för att uppdatera dina resurser.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Hantera Exchange Online

PowerShell i Cloud Shell innehåller en privat version av Exchange Online-modulen.  Kör `Connect-EXOPSSession` för att hämta dina Exchange-cmdlets.

![](media/features-powershell/exchangeonline.png)

 Kör `Get-Command -Module tmp_*`
> [!NOTE]
> Modulnamnet ska `tmp_`börja med , om du har installerat moduler med samma prefix, kommer deras cmdlets också att visas. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Djup integrering med verktyg med öppen källkod

Cloud Shell innehåller förkonfigurerad autentisering för verktyg med öppen källkod som Terraform, Ansible och Chef InSpec. Prova det från exemplet genomgångar.

## <a name="tools"></a>Verktyg

|Kategori   |Namn   |
|---|---|
|Linux-verktyg            |bash<br> Zsh<br> Sh<br> tmux<br> Gräva<br>               |
|Azure-verktyg            |[Klassiska AZURE CLI](https://github.com/Azure/azure-cli) och [Azure](https://github.com/Azure/azure-xplat-cli)<br> [AzKopia](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer (blobxfer)](https://github.com/Azure/blobxfer)|
|Textredigerare           |kod (Cloud Shell-redigerare)<br> vim<br> nano<br> emacs    |
|Källkontroll         |git                    |
|Bygg verktyg            |märke<br> maven<br> npm<br> Pip         |
|Containrar             |[Docker-dator](https://github.com/docker/machine)<br> [Kubectl (på)](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Rodret](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Databaser              |MySQL-klient<br> PostgreSql-klient<br> [sqlcmd-verktyg](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Annat                  |iPython-klient<br> [Molngjuteri CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible (Ansible)](https://www.ansible.com/microsoft-azure)<br> [Inspec](https://www.chef.io/inspec/)<br> [Marionettbult](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Stöd för språk

|Språk   |Version   |
|---|---|
|.NET Core  |2.2.402       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 och 3.5 (standard)|

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell Snabbstart](quickstart.md) <br>
[PowerShell i Snabbstart i Cloud Shell](quickstart-powershell.md) <br>
[Läs mer om Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Lär dig mer om Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
