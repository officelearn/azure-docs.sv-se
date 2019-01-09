---
title: Aktivera program för flera innehavare med Azure Digital Twins | Microsoft Docs
description: Hur du konfigurerar flera innehavare Azure Active Directory-program för Azure Digital Twins.
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: mavoge
ms.openlocfilehash: 2b4f9bf87122f047e496dca1dbd425db8ad7c16c
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119976"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Aktivera program för flera innehavare med Azure Digital Twins

Lösningar för utvecklare som bygger på Azure Digital Twins kanske upptäcker att de vill stöder flera kunder med en enskild tjänst eller lösning. I själva verket *multitenant* program är bland de vanligaste Azure Digital Twins konfigurationerna.

Det här dokumentet beskriver hur du konfigurerar en Digital Twins för Azure-app för att stödja flera Azure Active Directory-klienter och kunder.

## <a name="multitenancy"></a>Flera innehavare

En *multitenant* resursen är en etablerad instans som har stöd för flera kunder. Varje kund har sin egen oberoende data och behörigheter. Varje kundupplevelsen är isolerade från varandra så att deras ”visa” för programmet är distinkt.

Mer information om multitenancy [Multitenant-program i Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Problem med scenario

I det här scenariot du överväga att utvecklare att skapa en Digital Twins för Azure-lösning (**DEVELOPER**) och en kund som använder lösningen (**kunden**):

- **DEVELOPER** har en Azure-prenumeration med en Azure Active Directory-klient.
- **DEVELOPER** distribuerar en Azure Digital Twins-instans till deras Azure-prenumeration. Azure Active Directory skapas automatiskt ett tjänstens huvudnamn i **DEVELOPER**'s Azure Active Directory-klient.
- Användare inom **DEVELOPER**användarens Azure Active Directory-klient kan sedan [hämta OAuth 2.0-token](./security-authenticating-apis.md) från tjänsten Azure Digital Twins.
- **DEVELOPER** skapar nu en mobilapp som är direkt integrerad med Azure Digital Twins Management API: erna.
- **DEVELOPER** tillåter **kunden** användningen av mobila program.
- **KUNDEN** måste ha behörighet att använda Azure Digital Twins Management API i **DEVELOPER**'s program.

Problem:

- När **kunden** loggar in på **DEVELOPER**'s program, appen inte går att hämta token för **kunden**'s användare att autentisera med Azure Digital Twins Management API: erna.
- Ett undantag är utfärdat i Azure Active Directory som anger att Azure Digital Twins inte känns igen inom **kunden**'s directory.

## <a name="problem-solution"></a>Problem-lösning

För att lösa problemet scenariot ovan följande åtgärder behövs för att skapa en Digital Twins Azure tjänstens huvudnamn i den **kunden**'s Azure Active Directory-klient:

- Om **kunden** inte redan har en Azure-prenumeration med en Azure Active Directory-klient:

  - **KUNDEN**'s Azure Active Directory-klientadministratör måste skaffa en [användningsbaserad Azure-prenumeration](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - **KUNDEN**användarens Azure Active Directory-klientadministratör och sedan måste [länka klientorganisationen med den nya prenumerationen](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- På den [Azure-portalen](https://portal.azure.com), **kunden**'s Azure Active Directory-klientadministratör tar följande steg:

  1. Öppna **prenumerationer**.
  1. Välj den prenumeration som har Azure Active Directory-klient som ska användas i **DEVELOPER**'s program.

     ![Azure Active Directory-prenumerationer][1]

  1. Välj **Resursprovidrar**.
  1. Sök efter **Microsoft.IoTSpaces**.
  1. Välj **Registrera**.

     ![Azure Active Directory-resursprovidrar][2]
  
## <a name="next-steps"></a>Nästa steg

- Mer information om hur du använder användardefinierade funktioner med Azure Digital Twins [så här skapar du användardefinierade funktioner i Azure Digital Twins](./how-to-user-defined-functions.md).

- Läs hur du använder rollbaserad åtkomstkontroll för att ytterligare skydda program med rolltilldelningar [hur du skapar och hanterar Azure Digital Twins rollbaserad åtkomstkontroll](./security-create-manage-role-assignments.md).

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png
