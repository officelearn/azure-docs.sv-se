---
title: Återställning av lösenord för licens - Azure Active Directory
description: Lär dig mer om skillnaden i licenskrav för licensiering av lösenordsåterställning av lösenord i Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393063"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licenskrav för azure Active Directory självbetjäningslösenordsåterställning

För att minska antalet supportsamtal och minskad produktivitet när en användare inte kan logga in på sin enhet eller ett program kan användarkonton i Azure Active Directory (Azure AD) aktiveras för återställning av lösenord för självbetjäning (SSPR). Funktioner som utgör SSPR inkluderar lösenordsändring, återställning, upplåsning och tillbakaskrivning till en lokal katalog. Grundläggande SSPR-funktioner är tillgängliga för Office 365 och alla Azure AD-användare utan kostnad.

I den här artikeln beskrivs olika sätt att återställning av lösenord för självbetjäning kan licensieras och användas. Mer information om prissättning och fakturering finns på [sidan Azure AD-priser](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Jämför utgåvor och funktioner

SSPR är licensierat per användare. För att upprätthålla efterlevnad måste organisationer tilldela rätt licens till sina användare.

I följande tabell beskrivs de olika SSPR-scenarierna för lösenordsändring, återställning eller lokal tillbakaskrivning och vilka SKU:er som tillhandahåller funktionen.

| Funktion | Azure AD Kostnadsfri | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 eller P2 |
| --- |:---:|:---:|:---:|:---:|
| **Ändring av användarlösenord endast i molnet**<br />När en användare i Azure AD känner till sitt lösenord och vill ändra det till något nytt. | ● | ● | ● | ● |
| **Återställning av användarlösenord endast i molnet**<br />När en användare i Azure AD har glömt sitt lösenord och behöver återställa det. | | ● | ● | ● |
| **Hybridanvändare lösenord ändra eller återställa med on-prem tillbaka**<br />När en användare i Azure AD som synkroniseras från en lokal katalog med Azure AD Connect vill ändra eller återställa sitt lösenord och även skriva tillbaka det nya lösenordet till on-prem. | | | ● | ● |

> [!WARNING]
> Fristående Office 365-licensieringsplaner stöder inte SSPR med lokal tillbakaskrivning. Dessa Office 365-licensieringsplaner kräver azure AD Premium P1, Premium P2 eller Microsoft 365 Business för att den här funktionen ska fungera.

Mer licensinformation, inklusive kostnader, finns på följande sidor:

* [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funktioner och funktioner i Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Företag](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Företag](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Aktivera grupp- eller användarbaserad licensiering

Azure AD stöder gruppbaserad licensiering. Administratörer kan tilldela licenser i grupp av användare i grupp i stället för att tilldela dem en i taget. Mer information finns i [Tilldela, verifiera och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste administratören ange egenskapen **Användningsplats** för användaren. Tilldelning av licenser kan göras under avsnittet > **Användarprofilinställningar** > **Settings** i Azure-portalen. **User** *När du använder grupplicenstilldelning ärver alla användare utan angiven användningsplats platsen för katalogen.*

## <a name="next-steps"></a>Nästa steg

Slutför följande självstudiekurs för att komma igång med SSPR:

> [!div class="nextstepaction"]
> [Självstudiekurs: Aktivera återställning av lösenord för självbetjäning (SSPR)](tutorial-enable-sspr.md)
