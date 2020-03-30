---
title: Använda hanterade identiteter på en Virtuell Azure-dator med Azure SDK - Azure AD
description: Kodexempel för att använda Azure SDK:er med en Virtuell Azure-dator som har hanterade identiteter för Azure-resurser.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184157"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Så här använder du hanterade identiteter för Azure-resurser på en Virtuell Azure-dator med Azure SDK:er 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller en lista över SDK-exempel som visar användningen av deras respektive Azure SDK:s stöd för hanterade identiteter för Azure-resurser.

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Alla exempelkod/skript i den här artikeln förutsätter att klienten körs på en virtuell dator med hanterade identiteter för Azure-resurser aktiverade. Använd funktionen "Anslut" för virtuella datorer i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen](qs-configure-portal-windows-vm.md)eller en av variantartiklarna (med PowerShell, CLI, en mall eller en Azure SDK). 

## <a name="sdk-code-samples"></a>Exempel på SDK-kod

| SDK             | Kodexempel |
| --------------- | ----------- |
| .NET            | [Distribuera en Azure Resource Manager-mall från en Virtuell Windows-dator med hanterade identiteter för Azure-resurser](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Anropa Azure-tjänster från en virtuell Linux-dator med hanterade identiteter för Azure-resurser](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Hantera resurser med hanterade identiteter för Azure-resurser](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Använda hanterade identiteter för Azure-resurser för att autentisera helt enkelt inifrån en virtuell dator](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Hantera resurser från en virtuell dator med hanterade identiteter för Azure-resurser aktiverade](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Nästa steg

- Se [Azure SDK:er](https://azure.microsoft.com/downloads/) för en fullständig lista över Azure SDK-resurser, inklusive hämtningar av bibliotek, dokumentation med mera.
- Information om hur du aktiverar hanterade identiteter för Azure-resurser på en Virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen](qs-configure-portal-windows-vm.md).








