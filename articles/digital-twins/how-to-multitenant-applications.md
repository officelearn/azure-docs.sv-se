---
title: Så här aktiverar du program med flera klienter med Azure Digital Twins | Microsoft Docs
description: Förstå hur du registrerar dina kunders Azure Active Directory-klienter med Azure Digital Twins
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324369"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>Så här aktiverar du program med flera klienter med Azure Digital Twins

Utvecklare som använder Azure Digital Twins plattformen förmodligen vill bygga program för flera innehavare. En *program med flera klienter* är en enda etablerade instans som används för att stödja flera kunder varje med sina egna oberoende data och behörigheter.

Det här dokumentet beskriver hur du skapar en Azure Digital Twins fleranvändarapp som stöd för flera klienter för Azure Active Directory (AD) och kunder.

## <a name="scenario-summary"></a>Sammanfattning

I det här scenariot, Överväg att utvecklare D och kunden C:

- Developer D har en Azure-prenumeration med en Azure AD-klient.
- Developer D har distribuerat en Digital Twins-instans till deras Azure-prenumeration.
- Användare inom Developer D Azure AD-klient kan hämta token mot Digital Twins tjänsten eftersom Azure AD har skapat ett tjänstens huvudnamn i Azure AD-klient för utvecklare D.
- Developer D skapar nu en mobilapp som är direkt integrerad med digitala Twins Management-API.
- Developer D kan sedan kunden C användningen av mobila program.
- Nu kan måste kunden C ha behörighet att använda den digitala Twins API: et i Developer D program.

  > [!IMPORTANT]
  > - När kunden C loggar i Developer D program, kan appen inte att hämta token för kunden C användare att kommunicera med Management-API.
  > - Azure AD sedan genererar ett felmeddelande om att digitala Twins inte känns igen inom kunden C-katalog.

## <a name="solution"></a>Lösning

För att lösa scenariot ovan krävs följande åtgärder för att skapa en Digital Twins-tjänstens huvudnamn i Azure AD-klient för kunden C:

- Om kunden C inte redan har en Azure-prenumeration med Azure AD-klient:

  - Azure AD-klient Kundadmin C: s måste du skaffa en [användningsbaserad Azure-prenumeration](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Azure AD-klient Kundadmin C: s måste sedan [länka klientorganisationen med den nya prenumerationen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Från den [Azure-portalen](https://portal.azure.com), kunden C Azure AD Tenant Admin bör sedan:
  1. Öppna **prenumerationer**.
  1. Välj den prenumeration som har Azure AD-klient som ska användas i Developer D program.
  1. Välj **Resursprovidrar**.
  1. Sök efter **Microsoft.IoTSpaces**.
  1. Klicka på **registrera**.
  
## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder användardefinierade funktioner med Azure Digital Twins [Azure Digital Twins UDF](how-to-user-defined-functions.md).

Läs hur du använder rollbaserad åtkomstkontroll för att ytterligare skydda program med rolltilldelningar [digitala Twins rollbaserad åtkomstkontroll](security-create-manage-role-assignments.md).
