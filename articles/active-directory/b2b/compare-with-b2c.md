---
title: Jämför B2B-samarbete och B2C i Azure Active Directory | Microsoft Docs
description: Vad är skillnaden mellan Azure Active Directory B2B-samarbete och Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: overview
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 42fbb8b08a2dc24ced436c4a6104f03ae3bca1e9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982818"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Jämför B2B-samarbete och B2C i Azure Active Directory

Både Azure Active Directory (Azure AD) B2B-samarbete och Azure AD B2C låter dig arbeta med externa användare i Azure AD. Men hur jämförs de?

**Azure AD B2B** är för företag som vill dela filer och resurser på ett säkert sätt med externa användare så att de kan samarbeta. En Azure-administratör konfigurerar B2B i Azure-portalen och Azure AD tar hand om federation mellan din verksamhet och din externa partner. Användare loggar in på delade resurser med hjälp av en enkel process för inbjudan och inlösen med sina arbets- eller skolkonton, eller ett e-postkonto.
 
**Azure AD B2C** är främst avsett för företag och utvecklare som skapar kundriktade appar. Med Azure AD B2C kan utvecklare använda Azure AD som ett fullständigt identitetssystem i sina program, samtidigt som kunder kan logga in med en identitet som de redan har etablerat (till exempel Facebook eller Gmail).

Tabellen nedan ger en detaljerad jämförelse.


B2B-samarbetsfunktioner |     Azure AD B2C-fristående erbjudande
-------- | --------
Avsedd för: organisationer som vill kunna autentisera användare från en partnerorganisation oavsett identitetsprovider. | Avsedd för: att bjuda in kunder för dina mobila och webbappar oavsett om det rör sig om individuella, institutionella eller organisationella kunder till din Azure AD.
Identiteter som stöds: anställda med arbets- eller skolkonton, partners med arbets- eller skolkonton eller valfria e-postadresser. Stödjer snart direkt federation.  | Identiteter som stöds: konsumentanvändare med lokala programkonton (e-postadresser eller användarnamn) eller någon social identitet med direkt federation som stöds.
Vilken katalog som partneranvändare finns i: partneranvändare från den externa organisationen hanteras i samma katalog som anställda, men kommenteras särskilt. De kan hanteras på samma sätt som anställda, kan läggas till i samma grupper och så vidare  | Vilken katalog kundens användarentiteter finns i: I programkatalogen. Hanteras separat från organisationens anställda och partnerkatalogen (i förekommande fall.
Enkel inloggning (SSO) för alla Azure AD-anslutna appar stöds. Du kan till exempel ge åtkomst till Office 365 eller lokala appar och till andra SaaS-appar som Salesforce eller Workday.  |  Stöd för enkel inloggning till kundägda appar i Azure AD B2C-klientorganisationer. Enkel inloggning till Office 365 eller andra Microsoft- och icke-Microsoft-SaaS-appar stöds inte.
Livscykel för partner: hanteras av värden/den inbjudande organisationen.  | Livscykel för kunden: självbetjäning eller hanteras av programmet.
Säkerhetsprinciper och efterlevnad: hanteras av värden/den inbjudande organisationen (till exempel med [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Säkerhetsprincip och efterlevnad: hanteras av programmet.
Anpassning: Värden/den inbjudande organisationens varumärke används.  |    Anpassning: hanteras av programmet. Brukar vanligtvis vara produktanpassad med organisationens varumärke intonat i bakgrunden.
Mer information: [Blogginlägg](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentation](what-is-b2b.md)  | Mer information: [Produktsida](https://azure.microsoft.com/services/active-directory-b2c/), [Dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Användaregenskaper för B2B-samarbete](user-properties.md)

