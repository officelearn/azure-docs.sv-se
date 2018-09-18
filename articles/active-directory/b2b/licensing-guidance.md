---
title: Azure Active Directory B2B-samarbete och licenser vägledning | Microsoft Docs
description: Azure Active Directory B2B collaboration inte kräver betalda Azure AD-licenser, men du kan också få betalt för funktioner för B2B-gästanvändare
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 08/09/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1d92f68bbb5e8c001594e4f78f90cb10496aaf29
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984500"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Vägledning om Azure Active Directory B2B-samarbete och licenser

Du kan använda Azure AD B2B-samarbetsfunktioner för att bjuda in gästanvändare i Azure AD-klienten så att de kan få åtkomst till Azure AD-tjänster och andra resurser i din organisation. Om du vill ge åtkomst till Azure AD-betalfunktioner, lämpliga B2B-samarbete gästanvändarna vara licensierade med Azure AD-licenser. 

Närmare bestämt:
* Azure AD kostnadsfri funktioner är tillgängliga för gästanvändare utan ytterligare licenser.
* Om du vill ge åtkomst till Azure AD-betalfunktioner till B2B-användare, måste du ha tillräckligt med licenser för dessa B2B-gästanvändare.
* En inbjudande innehavaren med en Azure AD betald licens har B2B-samarbete användningsrättigheter till en ytterligare fem B2B gästanvändare bjudits in till klienten.
* Kunden som äger inbjudande innehavaren måste vara den mallen för att avgöra hur många B2B-samarbete som användarna behöver betalda Azure AD-funktioner. Beroende på den betalda Azure AD-funktioner som du vill använda för din gästanvändare måste du ha tillräckligt med Azure AD betald licenser så att användare i B2B-samarbetet i samma 5:1-förhållande.

En gästanvändare för B2B-samarbete läggs till som en användare från ett partnerföretag, inte anställd hos din organisation eller en person anställd hos ett annat företag i din konglomerat. En B2B-gästanvändare kan logga in med externa autentiseringsuppgifter eller autentiseringsuppgifter som ägs av organisationen enligt beskrivningen i den här artikeln. 

Med andra ord anges B2B-licensiering inte av hur användaren autentiseras utan i stället av relationen för användaren till din organisation. Om dessa användare inte partner, hanteras de på olika sätt i licensvillkoren. De anses inte vara en B2B-samarbete användare för licensiering, även om deras UserType är märkt ”gäst”. De ska licensieras vanligtvis på en licens per användare. Dessa användare inkluderar:
* Dina anställda
* Personal som loggar in med hjälp av externa identiteter
* Anställd hos ett annat företag i din konglomerat


## <a name="licensing-examples"></a>Exempel-licensiering
- En kund vill bjuda in 100 B2B-samarbete användare till dess Azure AD-klient. Kunden tilldelar åtkomsthantering och driftsättning för alla användare, men 50 användare också kräva MFA och villkorlig åtkomst. Kunden måste köpa 10 Azure AD Basic-licenser och 10 Azure AD Premium P1 licenser så att dessa B2B-användare på rätt sätt. Om kunden planerar att använda funktionerna för Identity Protection med B2B-användare, måste de ha Azure AD Premium P2-licenser för att täcka de inbjudna användarna i samma 5:1-förhållande.
- En kund har 10 anställda som har alla nu finns en licens för Azure AD Premium P1. De nu vill bjuda in 60 B2B-användare som kräver multifaktorautentisering (MFA). Kunden måste ha minst 12 Azure AD Premium P1 licenser så att alla 60 B2B-samarbetare under licensiering regel 5:1. Eftersom de redan har 10 Premium P1-licenser för de 10 anställda, har de behörighet att bjuda in 50 B2B-användare med Premium P1-funktioner som MFA. I det här exemplet, sedan kan köpa de 2 extra Premium P1-licenser för att täcka de återstående 10 B2B-samarbete användarna.

> [!NOTE]
> Det går inte att tilldela licenser direkt till B2B-användare att aktivera dessa användarrättigheter för B2B-samarbete.

Kunden som äger inbjudande innehavaren måste vara den mallen för att avgöra hur många B2B-samarbete som användarna behöver betalda Azure AD-funktioner. Du måste ha tillräckligt med Azure AD betald licenser för att täcka användare i B2B-samarbetet i 5:1-förhållande beroende på vilket betalda Azure AD-funktioner som du vill använda för din gästanvändare. 

## <a name="additional-licensing-details"></a>Ytterligare information för licensiering
- Det finns inget behov att faktiskt tilldela licenser till B2B-användarkonton. Baserat på 5:1-förhållande, licensiering automatiskt beräknas och rapporteras.
- Om ingen betald Azure AD-licens finns i klienten, varje inbjudna användare hämtar de rättigheter som den kostnadsfria versionen av Azure AD erbjuder.
- Om en användare redan har en betald Azure AD B2B licensiera från organisationen, förbrukar de inte en B2B-samarbete licenser för inbjudande innehavaren.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Avancerade diskussion: Vad är licensiering överväganden när vi lägger till användare från en konglomerat organisation som ”medlemmar” med hjälp av dina API: er?
En B2B-gästanvändare är en som bjuds in från en partnerorganisation att arbeta med värd-organisation. Normalt annan inte uppfyller kraven som B2B även den använder B2B-funktioner. Låt oss titta på två fall särskilt:

1. Om en värd bjuder in en medarbetare med en konsument-adress
  * Det här scenariot är inte kompatibel med våra principer för licensiering och rekommenderas inte.

2. Om en värd-organisation lägger till en användare från en annan konglomerat organisation
  1. I det här fallet användaren uppmanas med B2B-API: er, men det här fallet är inte traditionellt B2B. Vi bör helst ha dessa organisationer bjuda in andra användare i organisationer som medlemmar (vårt API tillåter som). I det här fallet har licenser som ska tilldelas till dessa medlemmar för dem att komma åt resurser i organisationen som bjuder in.

  2. Vissa organisationer vilja lägga till den andra organisationens användare som ska läggas till som ”gäst” som en princip. Det finns två fall här:
      * Konglomerat organisationen använder redan Azure AD och de inbjudna användarna licensieras i den andra organisationen: i det här fallet vi förväntar oss inte inbjudna användare måste följa 1:5-formeln som beskrivs tidigare i det här dokumentet. 

      * Konglomerat organisationen inte använder Azure AD eller inte har tillräckliga licenser: I det här fallet, följ 1:5-formeln som beskrivs tidigare i det här dokumentet.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar på Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Vanliga frågor (och svar FAQ) om Azure Active Directory B2B-samarbete](faq.md)
