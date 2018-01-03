---
title: "Hur du konfigurerar en Användartilldelad MSI för en virtuell Azure-dator med en Azure SDK"
description: "Steg för steg-instruktioner för hur du konfigurerar en Användartilldelad hanteras Service identitet (MSI) för en Azure-dator med en Azure SDK."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19b6179803b8de4102818c1522b00e6b4881d0f0
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Konfigurera en Användartilldelad hanteras Service identitet (MSI) för en virtuell dator med en Azure SDK

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

Lär dig hur du aktiverar och ta bort en Användartilldelad MSI för en Azure-dator med en Azure SDK i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK: er med MSI-stöd 

Azure har stöd för flera programmeringsspråk plattformar genom en serie [Azure SDK](https://azure.microsoft.com/downloads). Flera av dem har uppdaterats för att stödja MSI och tillhandahålla motsvarande exempel för att visa användning. Den här listan uppdateras när ytterligare stöd har lagts till:

| SDK | Exempel |
| --- | ------ | 
| Python | [Skapa en virtuell dator med aktiverat MSI](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Skapa Azure virtuell dator med en MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nästa steg

- Se relaterade artiklar under ”Konfigurera MSI för en virtuell Azure-dator”, att lära dig hur du kan konfigurera en Användartilldelad MSI på en Azure VM.

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
