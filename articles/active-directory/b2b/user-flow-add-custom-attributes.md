---
title: Lägg till anpassade attribut för Azure AD-användarkonton
description: Läs om hur du anpassar attributen för dina självbetjänings registrerings användar flöden.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597452"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Definiera anpassade attribut för användar flöden (förhands granskning)
|     |
| --- |
| Funktionen för anpassade användarattribut är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.|
|     |

För varje program kan du ha olika krav för den information som du vill samla in under registreringen. Azure AD levereras med en inbyggd uppsättning information som lagras i attribut, till exempel namn, efter namn, stad och post nummer. Med Azure AD kan du utöka uppsättningen med attribut som lagras på ett gäst konto när den externa användaren loggar in via ett användar flöde.

Du kan skapa anpassade attribut i Azure Portal och använda dem i dina användar flöden för självbetjänings registrering. Du kan också läsa och skriva attributen med hjälp av [Microsoft Graph-API: et](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). Microsoft Graph API har stöd för att skapa och uppdatera en användare med attribut för tillägg. Attribut för tillägg i Graph API namnges med hjälp av konventionen `extension_<Application-client-id>_attributename` . Till exempel:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Du hittar `<Application-client-id>` på sidan **Appregistreringar** bredvid **program-ID: t (klient)**. Detta ID är bara för din klient.

## <a name="create-a-custom-attribute"></a>Skapa ett anpassat attribut

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Under **Azure-tjänster**väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **anpassade användarattribut (förhands granskning)**. De tillgängliga användarattribut visas.

   ![Välj användarattribut för registrering](media/user-flow-add-custom-attributes/user-attributes.png)

5. Välj **Lägg**till om du vill lägga till ett attribut.
6. Ange följande värden i fönstret **Lägg till ett attribut** :

   - **Namn** – ange ett namn för det anpassade attributet (till exempel "Shoesize").
   - **Datatyp** – Välj en datatyp (**sträng**, **boolesk**eller **int**).
   - **Beskrivning** – alternativt anger du en beskrivning av det anpassade attributet för intern användning. Den här beskrivningen visas inte för användaren.

   ![Lägg till ett attribut](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Välj **Skapa**.

Det anpassade attributet är nu tillgängligt i listan över användarattribut och för användning i dina användar flöden. Ett anpassat attribut skapas bara första gången det används i alla användar flöden och inte när du lägger till det i listan med användarattribut.

När du har skapat en ny användare med ett användar flöde som använder det nya anpassade attributet, kan du frågas om objektet i [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Nu bör du se **ShoeSize** i listan över attribut som samlats in under registreringen och se den i den token som skickas tillbaka till ditt program. Information om hur du tar med dessa anspråk i den token som skickas tillbaka till programmet finns i [Konfigurera katalog tillägg valfria anspråk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims)

## <a name="next-steps"></a>Nästa steg

[Lägga till ett användar flöde för självbetjänings registrering i en app](self-service-sign-up-user-flow.md)