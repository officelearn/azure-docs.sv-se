---
title: Aktivera program med flera trogna – Azure Digital Twins | Microsoft-dokument
description: Konfigurera Azure Active Directory-program med flera trogna för Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264941"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Aktivera program med flera trogna med Azure Digital Twins

Lösningar utvecklare som bygger på Azure Digital Twins kan finna att de vill stödja flera kunder med en enda tjänst eller lösning. Faktum är att *multitenant* program är bland de vanligaste Azure Digital Twins-konfigurationerna.

I det här dokumentet beskrivs hur du konfigurerar en Azure Digital Twins-app för att stödja flera Azure Active Directory-klienter och kunder.

## <a name="multitenancy"></a>Multitenancy

En *resurs med flera användare* är en enda etablerad instans som stöder flera kunder. Varje kund har sina egna oberoende data och privilegier. Varje kunds erfarenhet är isolerad från varandras så att deras "syn" på programmet är distinkt.

Mer information om multitenancy finns [i Multitenant Applications i Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Problemscenario

I det här fallet bör du överväga en utvecklare som bygger en Azure Digital**Twins-lösning (UTVECKLARE)** och en kund som använder den lösningen (**KUND):**

- **DEVELOPER** har en Azure-prenumeration med en Azure Active Directory-klientorganisation.
- **DEVELOPER** distribuerar en Azure Digital Twins-instans i sin Azure-prenumeration. Azure Active Directory skapade automatiskt ett tjänsthuvudnamn i **DEVELOPER's**Azure Active Directory-klient.
- Användare i **DEVELOPER's**Azure Active Directory-klient kan sedan [hämta OAuth 2.0-token](./security-authenticating-apis.md) från Azure Digital Twins-tjänsten.
- **UTVECKLAREN** skapar nu en mobilapp som direkt integreras med Azure Digital Twins Management API:er.
- **UTVECKLARE** tillåter **KUNDEN** användning av mobilapplikationen.
- **KUNDEN** måste ha behörighet att använda Azure Digital Twins Management API i **DEVELOPER's**program.

Problemet:

- När **KUNDEN** loggar in på **DEVELOPER's**program kan appen inte skaffa token för **KUNDENs**användare att autentisera med Azure Digital Twins Management API:er.
- Ett undantag utfärdas i Azure Active Directory som anger att Azure Digital Twins inte känns igen i **CUSTOMER's**katalog.

## <a name="problem-solution"></a>Problemlösning

För att lösa det tidigare problemscenariot krävs följande åtgärder för att skapa ett Azure Digital Twins-tjänsthuvudnamn i **CUSTOMER's**Azure Active Directory-klient:

- Om **KUNDEN** inte redan har en Azure-prenumeration med en Azure Active Directory-klient:

  - **KUNDEN:s**Azure Active Directory-klientadministratör måste skaffa en [Azure-prenumeration som du har.](https://azure.microsoft.com/offers/ms-azr-0003p/)
  - **KUNDEN's**Azure Active Directory-klientadministratör måste sedan [länka sin klient till den nya prenumerationen](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- På [Azure-portalen](https://portal.azure.com)tar **KUNDEN:s**Azure Active Directory-klientadministratör följande steg:

  1. Sök efter **prenumerationer** i det översta Sökfältet i Azure. Välj **Prenumerationer**.
  1. Välj den prenumeration som har Azure Active **DEVELOPER**Directory-klienten som ska användas i DEVELOPER's-programmet.

     [![Azure Active Directory-prenumerationer](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Välj **Resursleverantörer**.
  1. Sök efter **Microsoft.IoTSpaces**.
  1. Välj **Registrera**.

     [![Azure Active Directory-resursleverantörer](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Nästa steg

- Mer information om hur du använder användardefinierade funktioner med Azure Digital Twins läser du [Så här skapar du användardefinierade funktioner i Azure Digital Twins](./how-to-user-defined-functions.md).

- Mer information om hur du använder rollbaserad åtkomstkontroll för att ytterligare skydda programmet med rolltilldelningar läser du [Så här skapar och hanterar du Azure Digital Twins rollbaserad åtkomstkontroll](./security-create-manage-role-assignments.md).
