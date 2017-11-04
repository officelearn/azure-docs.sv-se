---
title: "Krav för Azure Data Catalog | Microsoft Docs"
description: "Läs mer om de krav som du behöver att komma igång med Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/01/2017
ms.author: maroche
ms.openlocfilehash: cf32ef4c80fa0ee68ce3dc1289467a419aab39c9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-prerequisites"></a>Förutsättningar för Azure Data Catalog

Du måste ta hand om några saker innan du kan ställa in Azure Data Catalog. Oroa dig inte, den här processen tar inte lång tid.

## <a name="azure-subscription"></a>Azure-prenumeration
Du måste vara ägare eller Medägare för Azure-prenumeration om du vill konfigurera Data Catalog.

Azure-prenumerationer kan du organisera åtkomst till Molntjänsten resurser, till exempel Data Catalog. Prenumerationer hjälpa dig att styra hur Resursanvändning rapporterade, debiteras och betalat för. Varje prenumeration kan ha separata fakturerings- och inställningar, så du kan ha prenumerationer och scheman som varierar beroende på avdelning, projekt, lokalkontor och så vidare. Varje molntjänst som hör till en prenumeration och du måste ha en prenumeration innan du installerar Data Catalog. Mer information finns i [Hantera konton, prenumerationer och administrativa roller](../active-directory/active-directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Om du vill konfigurera Data Catalog måste du logga in med ett användarkonto i Azure Active Directory (AD Azure).

Azure AD tillhandahåller ett enkelt sätt för ditt företag att hantera identitet och åtkomst, både i molnet och lokalt. Användarna kan använda en enda arbets- eller skolkonto för enkel inloggning till alla molnet och lokala webbprogram. Data Catalog använder Azure AD för att autentisera inloggningen. Läs mer i [vad är Azure Active Directory?](../active-directory/active-directory-whatis.md).

> [!NOTE]
> Med hjälp av den [Azure-portalen](http://portal.azure.com/), du kan logga in med ett personligt microsoftkonto eller ett Azure Active Directory arbets- eller skolkonto. Att ställa in Data Catalog genom att använda antingen Azure-portalen eller [Data Catalog-portalen](http://www.azuredatacatalog.com), du måste logga in med ett Azure Active Directory-konto, inte ett personligt konto.
>
>

## <a name="active-directory-policy-configuration"></a>Active Directory-principkonfiguration
Du kan hända att du kan logga in till Data Catalog-portalen, men när du försöker logga in på registreringsverktyget för datakällor, du får ett felmeddelande som hindrar dig från att logga in. Det här problemet kan inträffa när du är på företagets nätverk eller uppstå endast när du kopplar från utanför företagets nätverk.

Registreringsverktyget för datakällor använder formulärautentisering för att verifiera dina autentiseringsuppgifter mot Active Directory. Som hjälper dig att logga in, måste en Active Directory-administratör aktivera formulärautentisering i den globala autentiseringsprincipen.

I den globala autentiseringsprincipen kan autentiseringsmetoder aktiveras separat för intranät och extranät-anslutningar som visas i följande skärmbild. Logga in fel kan inträffa om formulär för autentisering inte har aktiverats för nätverket som du ansluter.

 ![Globala autentiseringsprincipen i Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Nästa steg
Mer information finns i [konfigurera autentiseringsprinciper](https://technet.microsoft.com/library/dn486781.aspx).
