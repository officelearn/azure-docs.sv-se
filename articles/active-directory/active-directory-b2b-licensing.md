---
title: Azure Active Directory B2B-samarbete licensiering vägledning | Microsoft Docs
description: Azure Active Directory B2B collaboration inte kräver betald Azure AD-licenser, men du kan också hämta betalda funktioner för B2B gästanvändare
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 08/09/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5709c6db422a2357589f35fd77a2b1681a3e446a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928872"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Vägledning om Azure Active Directory B2B-samarbete och licenser

Du kan använda Azure AD B2B-samarbetesfunktioner för att bjuda in gästanvändare i Azure AD-klienten så att de kan komma åt Azure AD-tjänster och andra resurser i din organisation. Om du vill ge åtkomst till betald Azure AD-funktioner, lämpliga licenser för Azure AD B2B-samarbete gästanvändare måste licensieras med. 

Närmare bestämt:
* Azure AD ledigt funktioner är tillgängliga för gästanvändare utan ytterligare licenser.
* Om du vill ge åtkomst till betald Azure AD-funktioner för B2B-användare, måste du ha tillräckligt många licenser för att stödja dessa B2B-gästanvändare.
* En bjuda in klient med en Azure AD betald licens har B2B-samarbete använda rättigheter till en ytterligare fem B2B gästanvändare bjudits in till klienten.
* Kunden som äger bjuda in klienten måste vara en att avgöra hur många B2B-samarbete som användarna behöver betald funktionerna i Azure AD. Beroende på betald Azure AD-funktioner som du vill använda för din gästanvändare måste du ha tillräckligt med Azure AD betald licenser för att täcka B2B-samarbete användare i samma 5:1-förhållande.

Gästanvändare B2B-samarbete läggs till som en användare från ett partnerföretag, inte en medarbetare i din organisation eller en anställd i ett annat företag i din konglomerat. En B2B-gästanvändare kan logga in med autentiseringsuppgifter för externa eller ägs av organisationen som beskrivs i den här artikeln. 

Med andra ord anges B2B-licensiering inte av hur användaren autentiseras utan i stället av relationen för användare i organisationen. Om användarna inte partners, hanteras de på olika sätt i licensvillkoren. De anses inte vara en B2B-samarbete användare för licensiering även om deras UserType har markerats som ”gäst”. De ska vara licensierade normalt en licens per användare. Dessa användare inkluderar:
* Dina anställda
* Personal som loggar in med hjälp av externa identiteter
* En medarbetare i ett annat företag i din konglomerat


## <a name="licensing-examples"></a>Exempel-licensiering
- En kund vill bjuda in 100 B2B-samarbete användare till sina Azure AD-klient. Kunden tilldelar åtkomsthantering och etablering för alla användare, men 50 användare kräver också MFA och villkorlig åtkomst. Kunden måste köpa 10 Azure AD Basic licenser och 10 Azure AD Premium P1-licenser för att täcka dessa B2B-användare på rätt sätt. Om kunden planerar att använda funktionerna för identitetsskydd med B2B-användare, måste de ha Azure AD Premium P2 licenser för att täcka inbjudna användare i samma 5:1-förhållande.
- En kund har 10 anställda som licensieras alla med Azure AD Premium P1. De vill bjuda in 60 B2B-användare som kräver Multi-Factor authentication (MFA). Kunden måste ha minst 12 Azure AD Premium P1-licenser för att täcka alla 60 B2B-samarbete användare under licensiering regel 5:1. Eftersom de redan har 10 Premium P1-licenser för sina 10 anställda kan ha behörighet att bjuda in 50 B2B-användare med Premium P1-funktioner som MFA. I det här exemplet sedan köpa de 2 ytterligare Premium P1-licenser för att omfatta återstående 10 B2B-samarbete användare.

> [!NOTE]
> Det finns inget sätt än att tilldela licenser direkt till B2B-användarna för att aktivera dessa B2B-samarbete användarrättigheter.

Kunden som äger bjuda in klienten måste vara en att avgöra hur många B2B-samarbete som användarna behöver betald funktionerna i Azure AD. Du måste ha tillräckligt med Azure AD betald licenser för att täcka B2B-samarbete användare i 5:1-förhållande beroende på vilket betald Azure AD-funktioner som du vill använda för din gästanvändare. 

## <a name="additional-licensing-details"></a>Ytterligare information om licensiering
- Det finns inget behov av att faktiskt tilldela licenser till användarkonton B2B. Baserat på 5:1-förhållande, licensiering automatiskt beräknas och rapporteras.
- Om ingen betald Azure AD-licens finns i klienten, varje inbjudna användare hämtar de rättigheter som Azure AD-gratisversion erbjuder.
- Om en B2B collaboration användare redan har en betald Azure AD licens från organisationen, upp de inte en B2B-samarbete licenser för bjuda in innehavaren.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Avancerade diskussion: Vad är licensiering överväganden när vi lägger till användare från en konglomerat organisation som ”medlem” med din API: er?
Gästanvändare B2B är något som uppmanas att arbeta med organisationen värden från en partnerorganisation. Normalt annan inte uppfyller kraven som B2B även den använder B2B-funktioner. Nu ska vi titta på två fall särskilt:

1. Om en värd inbjuder en medarbetare genom att använda en konsument
  * Det här scenariot är inte kompatibel med vår licensiering principer och rekommenderas inte.

2. Om en organisation värden lägger till en användare från en annan konglomerat organisation
  1. I det här fallet uppmanas användaren med B2B-API: er, men det här fallet är inte traditionellt B2B. Vi rekommenderar ska vi ha dessa organisationer Bjud in andra användare i organisationer som medlemmar (vårt API tillåter som). I det här fallet har licenser som ska tilldelas dessa medlemmar för dem åtkomst till resurser i bjuda in organisationen.

  2. Vissa organisationer vilja lägga till andra organisationens användare som ska läggas till som ”gäst” som en princip. Det finns två fall här:
      * Konglomerat organisationen använder redan Azure AD och licensieras inbjudna användare i den andra organisationen: i det här fallet inbjudna användarna måste följa 1:5 formeln utvecklas tidigare i det här dokumentet inte förväntas. 

      * Konglomerat organisationen inte använder Azure AD eller inte har rätt licenser: I det här fallet följer 1:5 formeln utvecklas tidigare i det här dokumentet.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar artiklar på Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Vanliga och frågor svar om Azure Active Directory B2B-samarbete](active-directory-b2b-faq.md)
