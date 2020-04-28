---
title: Aktivera program med flera innehavare – Azure Digitals flätar | Microsoft Docs
description: Så här konfigurerar du Azure Active Directory program för flera innehavare för Azures digitala dubbla.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76264941"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Aktivera program med flera klient organisationer med digitala Azure-dubbla

Lösningar utvecklare som bygger på digitala Azure-multiplar kan upptäcka att de vill stödja flera kunder med en enda tjänst eller lösning. Program med flera *klienter* är i själva verket bland de vanligaste konfigurationerna för Azure Digitals dubbla.

I det här dokumentet beskrivs hur du konfigurerar en Azure Digitals-app med stöd för flera Azure Active Directory klienter och kunder.

## <a name="multitenancy"></a>Multitenancy

En resurs för flera *innehavare* är en enda etablerad instans som stöder flera kunder. Varje kund har sina egna oberoende data och behörigheter. Varje kunds upplevelse är isolerad från varandra så att deras "vy" av programmet är distinkt.

Om du vill veta mer om flera organisationer kan du läsa [program med flera klienter i Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Problem scenario

I det här scenariot bör du överväga en utvecklare som skapar en Azure digital-lösning (**utvecklare**) och en kund som använder lösningen (**kund**):

- **Utvecklaren** har en Azure-prenumeration med en Azure Active Directory klient.
- **Utvecklare** distribuerar en digital Azure-instans i Azure-prenumerationen. Azure Active Directory automatiskt skapat ett huvud namn för tjänsten i **utvecklarens**Azure Active Directory-klient.
- Användare i **utvecklarens**Azure Active Directory-klient kan sedan [Hämta OAuth 2,0-token](./security-authenticating-apis.md) från Azures digitala dubbla-tjänster.
- **Utvecklare** skapar nu en mobilapp som direkt kan integreras med API: er för Azure Digitals sammanflätade hantering.
- **Utvecklare** gör det möjligt för **kunden** att använda det mobila programmet.
- **Kunden** måste ha behörighet att använda Azure Digitals hanterings-API i **utvecklarens**program.

Problemet:

- När **kunden** loggar in i **utvecklarens**program kan appen inte hämta tokens för **kundens**användare att autentisera med API: er för Azure Digitals sammanflätade hantering.
- Ett undantag utfärdas i Azure Active Directory som anger att Azure Digital-dubbla är okända i **kundens**katalog.

## <a name="problem-solution"></a>Problem lösning

För att lösa det tidigare problem scenariot krävs följande åtgärder för att skapa ett Azure digital-tjänst huvud huvud i **kundens**Azure Active Directory klient organisation:

- Om **kunden** inte redan har en Azure-prenumeration med en Azure Active Directory klient:

  - **Kundens**Azure Active Directory klient organisations administratör måste skaffa en [Azure-prenumeration enligt principen betala per](https://azure.microsoft.com/offers/ms-azr-0003p/)användning.
  - **Kundens**Azure Active Directory klient organisations administratör måste sedan [Länka sin klient med den nya prenumerationen](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- På den [Azure Portal](https://portal.azure.com)vidtar **kund**Azure Active Directory klient administratören följande steg:

  1. Sök efter **prenumerationer** i det översta Azure Search-fältet. Välj **Prenumerationer**.
  1. Välj den prenumeration som har den Azure Active Directory klienten som ska användas i **utvecklarens**program.

     [![Azure Active Directory prenumerationer](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Välj **resurs leverantörer**.
  1. Sök efter **Microsoft. IoTSpaces**.
  1. Välj **Registrera**.

     [![Azure Active Directory resurs leverantörer](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig mer om hur du använder användardefinierade funktioner med Azure Digitals finns mer information [i så här skapar du Azure Digitals inbyggda funktioner](./how-to-user-defined-functions.md).

- Om du vill lära dig hur du använder rollbaserad åtkomst kontroll för att ytterligare skydda programmet med roll tilldelningar läser [du skapa och hantera Azure Digitals, rollbaserad åtkomst kontroll](./security-create-manage-role-assignments.md).
