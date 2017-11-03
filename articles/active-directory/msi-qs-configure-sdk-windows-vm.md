---
title: "Så här konfigurerar du en MSI-aktiverade virtuella Azure-datorn med en Azure SDK"
description: "Steg för steg-instruktioner för hur du konfigurerar och använder en hanterad tjänst identitet (MSI) på en Azure-dator med en Azure SDK."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: bryanla
ms.openlocfilehash: 84fcb0181d635ecd8ebc68b31365fba9d6be21ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av en Azure SDK

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory (AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

Lär dig hur du aktiverar och ta bort MSI för en Azure-dator med en Azure SDK i den här artikeln.

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK: er med MSI-stöd 

Azure har stöd för flera programmeringsspråk plattformar genom en serie [Azure SDK](https://azure.microsoft.com/downloads). Flera av dem har uppdaterats för att stödja MSI och tillhandahålla motsvarande exempel för att visa användning. Den här listan uppdateras när ytterligare stöd har lagts till:

| SDK | Exempel |
| --- | ------ | 
| .NET   | [Hantera resurser från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Hantera lagring från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Skapa en virtuell dator med aktiverat MSI](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Skapa en virtuell dator med aktiverat MSI](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Skapa Azure virtuell dator med en MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nästa steg

- Se relaterade artiklar under ”Konfigurera MSI för en virtuell Azure-dator”, att lära dig hur du kan också använda Azure portal, PowerShell, CLI och resurs-mallar.

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
