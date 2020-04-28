---
title: Återställning av lösen ord för självbetjäning – Azure Active Directory
description: Lär dig mer om skillnaden Azure Active Directory licens krav för lösen ords återställning via självbetjäning
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393063"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licensierings krav för Azure Active Directory återställning av lösen ord för självbetjäning

För att minska antalet support samtal och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program, kan användar konton i Azure Active Directory (Azure AD) aktive ras för lösen ords återställning via självbetjäning (SSPR). Funktioner som utgör SSPR inkluderar lösen ords ändring, återställning, upplåsning och tillbakaskrivning av en lokal katalog. Grundläggande SSPR-funktioner är tillgängliga för Office 365 och alla Azure AD-användare utan kostnad.

Den här artikeln beskriver de olika sätt som lösen ords återställning via självbetjäning kan licensieras och användas. Detaljerad information om priser och fakturering finns på [pris sidan för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Jämför versioner och funktioner

SSPR är licensierat per användare. Organisationer måste tilldela rätt licens till sina användare för att upprätthålla efterlevnad.

I följande tabell beskrivs olika SSPR-scenarier för ändring av lösen ord, återställning eller lokala tillbakaskrivning och vilka SKU: er som tillhandahåller funktionen.

| Funktion | Azure AD Kostnadsfri | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 eller P2 |
| --- |:---:|:---:|:---:|:---:|
| **Endast moln ändring av användar lösen ord**<br />När en användare i Azure AD känner till sitt lösen ord och vill ändra det till något nytt. | ● | ● | ● | ● |
| **Enbart molnbaserad återställning av användar lösen ord**<br />När en användare i Azure AD har glömt sitt lösen ord och behöver återställa det. | | ● | ● | ● |
| **Ändra lösen ord för Hybrid användare eller Återställ med lokal tillbakaskrivning**<br />När en användare i Azure AD som är synkroniserad från en lokal katalog som använder Azure AD Connect vill ändra eller återställa sitt lösen ord och även skriva det nya lösen ordet tillbaka till lokal. | | | ● | ● |

> [!WARNING]
> Fristående Office 365-licens planer stöder inte SSPR med lokal tillbakaskrivning. Dessa Office 365-licens planer kräver Azure AD Premium P1, Premium P2 eller Microsoft 365 Business för att den här funktionen ska fungera.

För ytterligare licens information, inklusive kostnader, se följande sidor:

* [Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funktioner och funktioner](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Företag](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Aktivera grupp-eller användar baserad licensiering

Azure AD stöder gruppbaserad licensiering. Administratörer kan tilldela licenser i bulk till en grupp användare i stället för att tilldela dem en i taget. Mer information finns i [tilldela, verifiera och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste administratören ange egenskapen för **användnings plats** för användaren. Tilldelning av licenser kan göras i avsnittet **användar** > **profils** > **Inställningar** i Azure Portal. *När du använder grupp licens tilldelning ärver alla användare som saknar en användnings plats platsen för katalogen.*

## <a name="next-steps"></a>Nästa steg

Kom igång med SSPR genom att följa de här självstudierna:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera självbetjäning för återställning av lösen ord (SSPR)](tutorial-enable-sspr.md)
