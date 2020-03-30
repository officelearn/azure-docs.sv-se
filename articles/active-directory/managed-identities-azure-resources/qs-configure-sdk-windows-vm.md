---
title: Använda en SDK för att konfigurera hanterade identiteter på en virtuell dator - Azure AD
description: Steg för steg instruktioner för att konfigurera och använda hanterade identiteter för Azure-resurser på en Azure VM, med hjälp av en Azure SDK.
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
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11cd51984f82bc20c02669e796d9ba21b9ed5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74183481"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurera en virtuell dator med hanterade identiteter för Azure-resurser med hjälp av en Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du aktiverar och tar bort hanterade identiteter för Azure-resurser för en Virtuell Azure med hjälp av en Azure SDK.

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDK:er med hanterade identiteter för Azure-resurser 

Azure stöder flera programmeringsplattformar via en serie [Azure SDK:er](https://azure.microsoft.com/downloads). Flera av dem har uppdaterats för att stödja hanterade identiteter för Azure-resurser och tillhandahålla motsvarande exempel för att demonstrera användning. Den här listan uppdateras när ytterligare stöd läggs till:

| SDK | Exempel |
| --- | ------ | 
| .NET   | [Hantera resurs från en virtuell dator aktiverad med hanterade identiteter för Azure-resurser aktiverade](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Hantera lagring från en virtuell dator som är aktiverad med hanterade identiteter för Azure-resurser](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Skapa en virtuell dator med systemtilldelad hanterad identitet aktiverad](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Skapa en virtuell dator med systemtilldelad hanterad identitet aktiverad](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Skapa Azure VM med en systemtilldelad identitet aktiverad](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nästa steg

- Se relaterade artiklar under **Konfigurera identitet för en Virtuell Azure**- om du vill veta hur du även kan använda Azure-portalen, PowerShell, CLI och resursmallar.
