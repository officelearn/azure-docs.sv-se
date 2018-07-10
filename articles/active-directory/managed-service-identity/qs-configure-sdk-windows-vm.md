---
title: Så här konfigurerar du en MSI-aktiverad virtuell Azure-dator med hjälp av en Azure-SDK
description: Steg för steg-instruktioner för hur du konfigurerar och använder en hanterad tjänstidentitet (MSI) på en Azure-dator med hjälp av en Azure-SDK.
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
ms.openlocfilehash: dee4a3e27623150ce3fa648d73542db0cbb23e93
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901450"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Konfigurera en VM-Managed tjänstidentitet (MSI) med hjälp av en Azure-SDK

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och ta bort MSI för en Azure-dator med hjälp av en Azure-SDK.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK: er med stöd för MSI 

Azure har stöd för flera programmeringsspråk plattformar via ett antal [Azure SDK: er](https://azure.microsoft.com/downloads). Flera av dem har uppdaterats för att stödja MSI och tillhandahålla motsvarande exempel för att demonstrera användning. Den här listan uppdateras när ytterligare stöd har lagts till:

| SDK | Exempel |
| --- | ------ | 
| .NET   | [Hantera resurs från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Hantera lagring från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Skapa en virtuell dator med MSI aktiverat](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Skapa en virtuell dator med MSI aktiverat](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Skapa Azure virtuell dator med en MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nästa steg

- Se relaterade artiklar under ”Konfigurera MSI för en Azure-dator”, att lära dig hur du kan också använda Azure portal, PowerShell, CLI och resource-mallarna.

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
