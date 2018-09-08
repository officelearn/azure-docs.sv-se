---
title: Arbetsflödets gemensamma parametrar i Azure Stack-validering som en tjänst | Microsoft Docs
description: Arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c50e4b5c9eb81c9386e2cb0db96a88de70dcb9e9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157811"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Parametrarna är värden som miljövariabler och användaren autentiseringsuppgifter som krävs av alla tester i verifiering som en tjänst (VaaS). Du definierar dessa värden på arbetsflödesnivå. Du kan spara värdena när du skapar eller ändrar ett arbetsflöde. Schema för när arbetsflödet läser du in värdena för testet. 

## <a name="environment-parameters"></a>Miljö-parametrar

Miljö parametrar beskrivs Azure Stack-miljön under testet. Dessa värden måste anges genom att generera och överför konfigurationsfilen stämpel `&lt;link&gt;. [How to get the stamp info link].`

| Parameternamn | Krävs | Typ | Beskrivning |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Azure Stack-version | Krävs |  | Build-nummer för Azure Stack-distribution (till exempel 1.0.170330.9) |
| OEM-version | Ja |  | Versionsnummer för OEM-paketet som används under distributionen av Azure Stack. |
| OEM-signatur | Ja |  | Signaturen för OEM-paketet som används under distributionen av Azure Stack. |
| Klient-ID för AAD | Krävs |  | Azure Active Directory-klient GUID anges under distributionen av Azure Stack.|
| Region | Krävs |  | Azure Stack-distributionsregionen. |
| Klient Resource Manager-slutpunkten | Krävs |  | Slutpunkten för klient med Azure Resource Manager-åtgärder (till exempel https://management.<ExternalFqdn>) |
| Administratören Resource Manager-slutpunkten | Ja |  | Slutpunkt för klient Azure Resource Manager-åtgärder (till exempel https://adminmanagement.<ExternalFqdn>) |
| Externa FQDN | Ja |  | Externa kvalificerade fullständigt domännamn som används som suffix för slutpunkter. (till exempel local.azurestack.external eller redmond.contoso.com). |
| Antal noder | Ja |  | Antalet noder för distributionen. |

## <a name="test-parameters"></a>Parametrarna för webbtestet

Vanliga testparametrar innehåller känslig information som kan lagras i konfigurationsfiler och måste anges manuellt.

| Parameternamn | Krävs | Typ | Beskrivning |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klient-användarnamn | Krävs |  | Azure Active Directory-Klientadministratör som antingen har redan etablerats eller affärsbehov som ska etableras med tjänstadministratören i AAD-katalogen. Mer information om etablering klientkonto finns [Kom igång med Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). Det här värdet används av testet för klient på åtgärder som till exempel distribuera mallar för att etablera resurser (virtuella datorer, lagringskonton osv) och köra arbetsbelastningar. Det här värdet används av testet för klient på åtgärder som till exempel distribuera mallar för att etablera resurser (virtuella datorer, lagringskonton osv) och köra arbetsbelastningar. |
| Klient-lösenord | Krävs |  | Lösenordet för klientorganisationsanvändaren. |
| Tjänstadministratör användarnamn | Krävs: Lösning verifiering verifiera paketet<br>Krävs inte: testet |  | Azure Active Directory-administratör för AAD-Directory-klient som anges under distributionen av Azure Stack. |
| Administratörslösenord för tjänsten | Krävs: Lösning verifiering verifiera paketet<br>Krävs inte: testet |  | Lösenordet för användaren tjänstadministratör. |
| Molnadministratören användarnamn | Krävs |  | Azure Stack domänadministratörskonto (till exempel contoso\cloudadmin). Sök efter användarroll = ”CloudAdmin” i konfigurationsfilen och Välj värdet i UserName-taggen i konfigurationsfilen. |
| Administratörslösenord för molnet | Krävs |  | Lösenordet för användaren Molnadministratören. |
| Anslutningssträngen för diagnostik | Krävs |  | En SAS-URI för Azure Storage-kontot på vilka felsökning kopieras loggfilerna vid testkörning. Anvisningar för att generera SAS-URI är placerade [ställa in ett blob storage-konto](azure-stack-vaas-set-up-account.md). |


## <a name="next-steps"></a>Nästa steg

- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).
