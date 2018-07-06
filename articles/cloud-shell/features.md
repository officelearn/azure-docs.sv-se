---
title: Bash i Azure Cloud Shell-funktioner | Microsoft Docs
description: Översikt över funktioner i Bash i Azure Cloud Shell
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: juluk
ms.openlocfilehash: f0be50a3e8328c26651e0db5c8fae708518a0ea1
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861896"
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Funktioner och verktyg för Bash i Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell som körs på `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funktioner

### <a name="secure-automatic-authentication"></a>Säker automatisk autentisering

Cloudshell autentiserar på ett säkert sätt och automatiskt kontoåtkomst för Azure CLI 2.0 och Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$Home bevarad mellan sessioner

För att spara filer mellan sessioner, vägleder dig genom att koppla en Azure-filresurs vid första start i Cloud Shell.
När klar koppla Cloud Shell automatiskt din lagring (monterats som `$Home\clouddrive`) för alla framtida sessioner.
Dessutom kan din `$Home` directory sparas som en .img i din Azure-filresurs.
Filer utanför `$Home` och datorn är inte beständiga mellan sessioner. Följ rekommenderade säkerhetsmetoder när du lagrar hemligheter som SSH-nycklar. Tjänster såsom [Azure Key Vault har självstudier för installation](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Läs mer om bevara filer i Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-enheten (Azure:)

PowerShell i Cloud Shell (förhandsversion) startar du i Azure-enheten (`Azure:`).
Azure-enhetslagringen kan enkelt hitta och navigering i Azure-resurser som beräkning, nätverk, lagring osv liknar filsystem navigering.
Du kan fortsätta att använda vanliga [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure) hantera dem oavsett den enhet som du tillhör.
Ändringar som görs till Azure-resurser, antingen direkt i Azure-portalen eller via Azure PowerShell-cmdletar, återspeglas i Azure-enheten.  Du kan köra `dir -Force` att uppdatera dina resurser.

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>Djupgående integrering med verktyg för öppen källkod

Cloudshell innehåller förkonfigurerade autentisering för open source-verktyg som Terraform, Ansible och InSpec Chef. Prova från exempel-genomgångar.

## <a name="tools"></a>Verktyg

|Kategori   |Namn   |
|---|---|
|Linux-verktyg            |Bash<br> zsh<br> SH<br> tmux<br> fördjupa<br>               |
|Azure-verktyg            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) och [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) |
|Textredigerare           |VIM<br> nano<br> emacs       |
|Källkontroll         |git                    |
|Skapa verktyg            |Kontrollera<br> maven<br> npm<br> PIP         |
|Behållare             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Databaser              |MySQL-klienten<br> PostgreSql-klient<br> [SQLCMD-verktyget](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Annat                  |iPython klienten<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)| 

## <a name="language-support"></a>Språkstöd

|Språk   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.0.2](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 och 3.5 (standard)|

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell-Snabbstart](quickstart.md) <br>
[PowerShell i Snabbstarta Cloud Shell (förhandsversion)](quickstart-powershell.md) <br>
[Lär dig mer om Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Lär dig mer om Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
