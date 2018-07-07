---
title: 'Hur du använder hanterad tjänstidentitet en Azure virtuell dator med Azure SDK: er'
description: 'Kodexempel för att använda Azure SDK: er med en virtuell MSI-dator för Azure.'
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 1c7a0d10f8ff005d90bb77f33bf40a00f97e078f
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901764"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Hur du använder en Azure VM hanterad tjänstidentitet (MSI) med Azure SDK: er 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller en lista över SDK-exempel som visar användning av deras respektive Azure SDK-stöd för MSI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Alla exempel kod eller skript i den här artikeln förutsätter att klienten körs på en MSI-aktiverad virtuell dator. Använd funktionen ”ansluta” virtuell dator i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns i [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md), eller någon av varianten artiklar (med PowerShell, CLI, en mall eller ett Azure SDK). 

## <a name="sdk-code-samples"></a>SDK-kodexempel

| SDK             | Kodexempel |
| --------------- | ----------- |
| .NET            | [Distribuera en Azure Resource Manager-mall från en Windows virtuell dator med hjälp av hanterad tjänstidentitet](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Anropa Azure-tjänster från en Linux VM med hjälp av hanterad tjänstidentitet](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Hantera resurser med hjälp av hanterad tjänstidentitet](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Använd MSI för att autentisera bara från inuti en virtuell dator](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Hantera resurser från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Relaterat innehåll

- Se [Azure SDK: er](https://azure.microsoft.com/downloads/) för en fullständig lista över Azure SDK-resurser, inklusive biblioteket nedladdningar och dokumentation.
- För att aktivera MSI på en Azure VM, se [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.








