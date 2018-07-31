---
title: Jämför B2B-samarbete och B2C i Azure Active Directory | Microsoft Docs
description: Vad är skillnaden mellan Azure Active Directory B2B-samarbete och Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b59dba541394c105370cfd3af0768a8477ddb4e6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348522"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Jämför B2B-samarbete och B2C i Azure Active Directory

Både Azure Active Directory (Azure AD) B2B-samarbete och Azure AD B2C kan du arbeta med externa användare i Azure AD. Men hur jämföra de?


B2B-samarbetsfunktioner |     Azure AD B2C-fristående erbjudande
-------- | --------
Avsedd för: organisationer som vill kunna autentisera användare från en partnerorganisation, oavsett identitetsprovider. | Avsedd för: bjuda in kunder för dina mobila och web apps om personer, institutionella eller organisationens kunder till din Azure AD.
Identiteter som stöds: anställda med arbets- eller skolkonton, partner med arbets-eller skolkonto eller valfri e-postadress. Snart att stödja direkt federation.  | Identiteter som stöds: konsumentanvändare med konton för lokalt program (alla e-postadress eller användarnamn namn) eller någon stöds sociala identitet med hjälp av direkt federation.
Vilken katalog som partneranvändare finns i: partneranvändare från extern organisation hanteras i samma katalog som anställda, men kommenterade särskilt. De kan hanteras på samma sätt som anställda kan läggas till samma grupper och så vidare  | Vilken katalog användarentiteter kund finns i: I programkatalogen. Hanteras separat från organisationens anställda och partner directory (i förekommande fall.
Enkel inloggning (SSO) för alla Azure AD-anslutna appar stöds. Du kan till exempel ge åtkomst till Office 365 eller lokala appar och andra SaaS-appar, till exempel Salesforce eller Workday.  |  Enkel inloggning till kundägda appar i Azure AD B2C-klienter stöds. Enkel inloggning till Office 365 eller andra Microsoft- och icke - Microsoft-SaaS-appar stöds inte.
Livscykel för partner: hanteras av den värd/bjuda in organisation.  | Kundlivscykeln: självbetjäning eller hanteras av programmet.
Säkerhetsprinciper och efterlevnad: hanteras av den värd/bjuda in organisation (till exempel med [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Säkerhetsprinciper och efterlevnad: hanteras av programmet.
Anpassning: Värd/bjuda in organisationens varumärke används.  |    Anpassning: Hanteras av programmet. Vanligtvis tenderar att vara produkten som är märkta med organisation Tona in i bakgrunden.
Mer information: [blogginlägget](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentation](what-is-b2b.md)  | Mer information: [produktsidan](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Egenskaper för användare av B2B-samarbete](user-properties.md)

