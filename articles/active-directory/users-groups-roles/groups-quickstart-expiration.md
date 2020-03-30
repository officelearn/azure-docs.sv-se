---
title: Snabbstart för gruppförfalloprincip – Azure AD | Microsoft-dokument
description: Förfallotid för Office 365-grupper – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b6e4f521568c89e415e6e9fa621175052cf34d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026904"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Snabbstart: Ställ in Office 365-grupper att upphöra att gälla i Azure Active Directory

I den här snabbstarten anger du en förfalloprincip för dina Office 365-grupper. När användare kan konfigurera sina egna grupper kan det uppstå många oanvända grupper. Ett sätt att hantera oanvända grupper är att ställa in dessa grupper att upphöra, för att minska underhållet med att ta bort grupper manuellt.

Förfalloprinciper är enkla:

- Grupper med användaraktiviteter förnyas automatiskt när förfallodatumet närmar sig
- Gruppägare meddelas om att förnya en grupp som håller på att upphöra
- Grupper som inte förnyas tas bort
- En borttagen Office 365-grupp kan återställas inom 30 dagar av en gruppägare eller en Azure AD-administratör

> [!NOTE]
> Grupper använder nu Azure AD-intelligens för att automatiskt förnyas baserat på om de har använts nyligen. Det här förnyelsebeslutet baseras på användaraktivitet i grupper i Office 365-tjänster som Outlook, SharePoint, Teams, Yammer och andra.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisite"></a>Krav

 Den minst privilegierade roll som krävs för att ställa in gruppens förfallodatum är Användaradministratör i organisationen.

## <a name="turn-on-user-creation-for-groups"></a>Aktivera användarskapande för grupper

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett användarkonto för användaradministratör.

2. Välj **Grupper** och välj sedan **Allmänt**.
  
   ![Sida för gruppinställningar för självbetjäning](./media/groups-quickstart-expiration/self-service-settings.png)

3. Ställ in **Användare kan skapa Office 365-grupper** till **Ja**.

4. Välj **Spara** för att spara gruppinställningarna när du är klar.

## <a name="set-group-expiration"></a>Ange gruppförfallotid

1. Logga in på [Azure-portalen](https://portal.azure.com)och välj **Azure Active Directory** > **Groups** > **förfallodatum** för att öppna förfalloinställningarna.
  
   ![Sidan Förfallodatuminställningar för grupp](./media/groups-quickstart-expiration/expiration-settings.png)

2. Ange förfallointervallet. Välj ett förinställt värde eller ange ett anpassat värde över 31 dagar. 

3. Ange en e-postadress dit meddelanden om förfallotid ska skickas när en grupp inte har någon ägare.

4. För den här snabbstarten ställer du in **Aktivera förfallotid för de här Office 365-grupperna** till **Alla**.

5. Välj **Spara** för att spara förfalloinställningarna när du är klar.

Klart! I den här snabbstarten ställde du in en förfalloprincip för de valda Office 365-grupperna.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="to-remove-the-expiration-policy"></a>Så här tar du bort förfalloprincipen

1. Se till att du är inloggad på [Azure-portalen](https://portal.azure.com) med ett konto som är global administratör för klientorganisationen.
2. Välj**Förfallodatum**för **Azure Active Directory-grupper** > **Groups** > .
3. Ställ in **Aktivera förfallotid för de här Office 365-grupperna** på **Inga**.

### <a name="to-turn-off-user-creation-for-groups"></a>Så här stänger du av användarskapande för grupper

1. Välj**Allmänt**Azure Active > **Directory-grupper** > . **Azure Active Directory** 
2. Ställ in **Användare kan skapa Office 365-grupper i Azure-portaler** till **Nej**.

## <a name="next-steps"></a>Nästa steg

Mer information om förfallodatum, inklusive PowerShell-instruktioner och tekniska begränsningar, finns i följande artikel:

> [!div class="nextstepaction"]
> [PowerShell-princip för förfallodatum](groups-lifecycle.md)
