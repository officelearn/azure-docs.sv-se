---
title: Aktivera program för flera innehavare med Azure Digital Twins | Microsoft Docs
description: Förstå hur du registrerar dina kunders Azure Active Directory-klienter med Azure Digital Twins
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259895"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Aktivera program för flera innehavare med Azure Digital Twins

Utvecklare som använder Azure Digital Twins vanligtvis vill bygga program för flera innehavare. En *program med flera klienter* är en etablerad instans som har stöd för flera kunder. Varje kund har sin egen oberoende data och behörigheter.

Det här dokumentet beskriver hur du skapar en Azure Digital Twins multitenant-app som har stöd för flera klienter för Azure Active Directory (Azure AD) och kunder.

## <a name="scenario-summary"></a>Sammanfattning

I det här scenariot, Överväg att utvecklare D och kunden C:

- Utvecklare D får en Azure-prenumeration med en Azure AD-klient.
- Developer D distribuerar en Azure Digital Twins-instans till deras Azure-prenumeration.
- Användare inom Developer D Azure AD-klient kan hämta token mot tjänsten Azure Digital Twins eftersom Azure AD skapade en tjänsten huvudnamn i Developer D Azure AD-klient.
- Developer D skapar nu en mobilapp som är direkt integrerad med Azure-API för hantering av digitala Twins.
- Developer D kan kunden C användningen av mobila program.
- Kunden C måste ha behörighet att använda Azure Digital Twins Management API: et i Developer D-programmet.

  > [!IMPORTANT]
  > - När kunden C-loggar i Developer D program kan inte appen hämta token för kunden C användare att kommunicera med Management-API.
  > - Azure AD genererar ett fel, vilket betyder att Azure Digital Twins inte känns igen inom kunden C-katalog.

## <a name="solution"></a>Lösning

Lös scenariot ovan genom behövs följande åtgärder för att skapa en Digital Twins Azure tjänstens huvudnamn i Azure AD-klient för kunden C:

- Om kunden C inte redan har en Azure-prenumeration med en Azure AD-klient:

  - Kunden C: s Azure AD-administratör måste skaffa en [användningsbaserad Azure-prenumeration](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Sedan måste kunden C: s Azure AD-Innehavaradministratör [länka klientorganisationen med den nya prenumerationen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- På den [Azure-portalen](https://portal.azure.com), kunden C Azure AD-administratör tar följande steg:

  1. Öppna **prenumerationer**.
  1. Välj den prenumeration som har Azure AD-klient som ska användas i Developer D program.
  1. Välj **Resursprovidrar**.
  1. Sök efter **Microsoft.IoTSpaces**.
  1. Välj **Registrera**.
  
## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder användardefinierade funktioner med Azure Digital Twins [Azure Digital Twins UDF](how-to-user-defined-functions.md).

Läs hur du använder rollbaserad åtkomstkontroll för att ytterligare skydda program med rolltilldelningar [Azure Digital Twins rollbaserad åtkomstkontroll](security-create-manage-role-assignments.md).
