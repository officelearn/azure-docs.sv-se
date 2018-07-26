---
title: Så här konfigurerar du en hanterad tjänstidentitet aktiverad virtuell Azure-dator med hjälp av en Azure-SDK
description: Steg för steg-instruktioner för att konfigurera och använda en hanterad tjänstidentitet på en Azure-dator med hjälp av en Azure-SDK.
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
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 2763c78d309f5a90d68429caa46581e50f8b4303
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257668"
---
# <a name="configure-a-vm-managed-service-identity-using-an-azure-sdk"></a>Konfigurera en VM-Managed tjänstidentitet med hjälp av en Azure-SDK

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och ta bort hanterad tjänstidentitet för en Azure-dator med hjälp av en Azure-SDK.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-service-identity-support"></a>Azure SDK: er med stöd för hanterad tjänstidentitet 

Azure har stöd för flera programmeringsspråk plattformar via ett antal [Azure SDK: er](https://azure.microsoft.com/downloads). Flera av dem har uppdaterats för att stödja hanterad tjänstidentitet och tillhandahålla motsvarande exempel för att demonstrera användning. Den här listan uppdateras när ytterligare stöd har lagts till:

| SDK | Exempel |
| --- | ------ | 
| .NET   | [Hantera resurs från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Hantera lagring från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Skapa en virtuell dator med MSI aktiverat](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Skapa en virtuell dator med MSI aktiverat](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Skapa Azure virtuell dator med en MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nästa steg

- Se relaterade artiklar under ”Konfigurera Managed Service Identity för en Azure-dator”, att lära dig hur du kan också använda Azure portal, PowerShell, CLI och resource-mallarna.

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
