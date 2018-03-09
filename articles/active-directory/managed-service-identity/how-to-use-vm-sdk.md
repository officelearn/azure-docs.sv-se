---
title: "Använda hanterade tjänstidentiteten en Azure VM med Azure SDK"
description: "Kodexempel för användning av Azure SDK: er med en Azure VM MSI."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 6b3f5ab86b06675c4b50ee6bab1f81a538ee7185
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Hur du använder en Azure VM hanterade tjänsten identitet (MSI) med Azure SDK 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller en lista med SDK-exempel som demonstrerar användningen av deras respektive Azure SDK stöd för MSI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Alla exempel kod eller skript i den här artikeln förutsätter klienten körs på en MSI-aktiverad virtuell dator. Använd funktionen ”ansluta” VM i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](qs-configure-portal-windows-vm.md), eller en variant artiklar (med PowerShell, CLI, en mall eller en Azure SDK). 

## <a name="sdk-code-samples"></a>SDK-kodexempel

| SDK             | Kodexempel |
| --------------- | ----------- |
| .NET            | [Distribuera en Azure Resource Manager-mall från en virtuell Windows-dator med hjälp av hanterade tjänstidentiteten](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Anropa Azure-tjänster från en Linux VM som använder hanterade tjänstidentiteten](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Hantera resurser med hjälp av hanterade tjänstidentiteten](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Använda MSI för att autentisera bara från inuti en virtuell dator](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Hantera resurser från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Relaterat innehåll

- Se [Azure SDK: er](https://azure.microsoft.com/downloads/) för en fullständig lista över Azure SDK-resurser, inklusive biblioteket hämtningar och dokumentation.
- För att aktivera MSI på en Azure VM, se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](qs-configure-portal-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.








