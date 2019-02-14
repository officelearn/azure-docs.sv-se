---
title: Jämför B2B-samarbete och B2C i Azure Active Directory | Microsoft Docs
description: Vad är skillnaden mellan Azure Active Directory B2B-samarbete och Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 01/30/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: c37114f15ae7c3c7a0c1c923b3c81cfa1a1fe4d1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206794"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Jämför B2B-samarbete och B2C i Azure Active Directory

Både Azure Active Directory (Azure AD) B2B-samarbete och Azure AD B2C låter dig arbeta med externa användare i Azure AD. Men hur jämförs de?

**Azure AD B2B** är för företag som vill dela filer och resurser på ett säkert sätt med externa användare så att de kan samarbeta. En Azure-administratör konfigurerar B2B i Azure-portalen och Azure AD tar hand om federation mellan din verksamhet och din externa partner. Användare loggar in på delade resurser med hjälp av en enkel process för inbjudan och inlösen med sina arbets- eller skolkonton, eller ett e-postkonto.
 
**Azure AD B2C** är främst avsett för företag och utvecklare som skapar kundriktade appar. Med Azure AD B2C kan utvecklare använda Azure AD som ett fullständigt identitetssystem i sina program, samtidigt som kunder kan logga in med en identitet som de redan har etablerat (till exempel Facebook eller Gmail).

Tabellen nedan ger en detaljerad jämförelse.


B2B-samarbetsfunktioner |     Azure AD B2C-fristående erbjudande
-------- | --------
Avsett för: Organisationer som vill kunna autentisera användare från en partnerorganisation oavsett identitetsprovider. | Avsett för: Att bjuda in kunder för dina mobila och webbappar oavsett om det rör sig om individuella, institutionella eller organisationella kunder till din Azure AD.
Identiteter som stöds: Anställda med arbets- eller skolkonton, partner med arbets- eller skolkonton eller valfri e-postadress. Stödjer snart direkt federation.  | Identiteter som stöds: Konsumentanvändare med lokala programkonton (e-postadresser eller användarnamn) eller någon social identitet med direkt federation som stöds.
Externa användare hanteras i samma katalog som anställda, men kommenteras särskilt. De kan hanteras på samma sätt som anställda, kan läggas till i samma grupper och så vidare  | Externa användare hanteras i programkatalogen. De hanteras separat från organisationens anställda och partnerkatalogen (i förekommande fall).
Enkel inloggning (SSO) för alla Azure AD-anslutna appar stöds. Du kan till exempel ge åtkomst till Office 365 eller lokala appar och till andra SaaS-appar som Salesforce eller Workday.  |  Stöd för enkel inloggning till kundägda appar i Azure AD B2C-klientorganisationer. Enkel inloggning till Office 365 eller andra Microsoft- och icke-Microsoft-SaaS-appar stöds inte.
Livscykel för partner: Hanteras av värden/den inbjudande organisationen.  | Livscykel för kunden: Självbetjäning eller hanteras av programmet.
Säkerhetsprincip och efterlevnad: Hanteras av värden/den inbjudande organisationen (till exempel med [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Säkerhetsprincip och efterlevnad: Hanteras av programmet.
Varumärke: Värden/den inbjudande organisationens varumärke används.  |    Varumärke: Hanteras av programmet. Brukar vanligtvis vara produktanpassad med organisationens varumärke intonat i bakgrunden.
Mer information: [Blogginlägg](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentation](what-is-b2b.md)  | Mer information: [Produktsida](https://azure.microsoft.com/services/active-directory-b2c/), [Dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Användaregenskaper för B2B-samarbete](user-properties.md)

