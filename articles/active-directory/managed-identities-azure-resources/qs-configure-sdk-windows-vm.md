---
title: Använd ett Azure SDK för att konfigurera en virtuell dator med hanterade identiteter för Azure-resurser
description: Steg för steg-instruktioner för att konfigurera och använda hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av en Azure-SDK.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: priyamo
ms.openlocfilehash: dfe0b46411c5ed4cb37cdb2c95ddb1b57407f435
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193632"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurera en virtuell dator med hanterade identiteter för Azure-resurser med hjälp av en Azure-SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och ta bort hanterade identiteter för Azure-resurser för en Azure-dator med hjälp av en Azure-SDK.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Stöd för Azure SDK: er med hanterade identiteter för Azure-resurser 

Azure har stöd för flera programmeringsspråk plattformar via ett antal [Azure SDK: er](https://azure.microsoft.com/downloads). Flera av dem har uppdaterats för att stödja hanterade identiteter för Azure-resurser och tillhandahålla motsvarande exempel för att demonstrera användning. Den här listan uppdateras när ytterligare stöd har lagts till:

| SDK | Exempel |
| --- | ------ | 
| .NET   | [Hantera resurs från en virtuell dator aktiveras med hanterade identiteter för Azure-resurser aktiverat](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Hantera lagring på en virtuell dator aktiveras med hanterade identiteter för Azure-resurser](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Skapa en virtuell dator med systemtilldelade hanterad identitet aktiverat](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Skapa en virtuell dator med systemtilldelade hanterad identitet aktiverat](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Skapa virtuell Azure-dator med en automatiskt genererad identitet som aktiverats](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nästa steg

- Se relaterade artiklar under **konfigurera identitet för en Azure VM**du vill veta hur du kan också använda Azure portal, PowerShell, CLI och resource-mallarna.
