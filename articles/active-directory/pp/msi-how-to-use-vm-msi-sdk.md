---
title: "Hur du använder en Användartilldelad hanterade tjänstidentiteten som från Azure SDK: er på en virtuell dator"
description: "Kodexempel för användning av Azure SDK: er med en Användartilldelad MSI på en virtuell dator."
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 808b0357494adac8c8ad7f51e668317e378d290d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>Använd Azure SDK: er med en Användartilldelad hanteras Service identitet (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här artikeln innehåller en lista med SDK-exempel som demonstrerar användningen av deras respektive Azure SDK-stöd för Användartilldelad MSI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - Alla exempel kod eller skript i den här artikeln förutsätter klienten körs på en MSI-aktiverad virtuell dator. Använd funktionen ”ansluta” VM i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md), eller en variant artiklar (med PowerShell, Azure-portalen, en mall eller en Azure SDK). 

## <a name="sdk-code-samples"></a>SDK-kodexempel

| SDK             | Kodexempel |
| --------------- | ----------- |
| Python          | [Använda MSI för att autentisera bara från inuti en virtuell dator](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Hantera resurser från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Nästa steg

- Se [Azure SDK: er](https://azure.microsoft.com/downloads/) för en fullständig lista över Azure SDK-resurser, inklusive biblioteket hämtningar och dokumentation.

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.








