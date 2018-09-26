---
title: 'Hur du använder hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure SDK: er'
description: 'Kodexempel för att använda Azure SDK: er med en Azure-dator som har hanterade identiteter för Azure-resurser.'
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
ms.openlocfilehash: 6827b01e72cd72d3f017df36205ccb985b4f242e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47107032"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Hur du använder hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure SDK: er 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller en lista över SDK-prov som demonstrerar användningen av deras respektive Azure SDK-stöd för hanterade identiteter för Azure-resurser.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Alla exempel kod eller skript i den här artikeln förutsätter att klienten körs på en virtuell dator med hanterade identiteter för Azure-resurser aktiverat. Använd funktionen ”ansluta” virtuell dator i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i [konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure portal](qs-configure-portal-windows-vm.md), eller någon av varianten artiklar (med PowerShell, CLI, en mall eller en Azure SDK). 

## <a name="sdk-code-samples"></a>SDK-kodexempel

| SDK             | Kodexempel |
| --------------- | ----------- |
| .NET            | [Distribuera en Azure Resource Manager-mall från en Windows virtuell dator med hjälp av hanterade identiteter för Azure-resurser](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Anropa Azure-tjänster från en Linux VM med hjälp av hanterade identiteter för Azure-resurser](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Hantera resurser med hjälp av hanterade identiteter för Azure-resurser](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Använda hanterade identiteter för Azure-resurser för att autentisera bara från inuti en virtuell dator](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Hantera resurser från en virtuell dator med hanterade identiteter för Azure-resurser aktiverat](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Nästa steg

- Se [Azure SDK: er](https://azure.microsoft.com/downloads/) för en fullständig lista över Azure SDK-resurser, inklusive biblioteket nedladdningar och dokumentation.
- För att aktivera hanterade identiteter för Azure-resurser på en Azure VM, se [konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure portal](qs-configure-portal-windows-vm.md).








