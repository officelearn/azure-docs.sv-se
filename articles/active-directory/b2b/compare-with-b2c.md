---
title: Jämför B2B-samarbete och B2C i Azure Active Directory | Microsoft Docs
description: Vad är skillnaden mellan Azure Active Directory B2B-samarbete och Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 19e8e9d22938fa8a18299d67aa77824aaae3f6da
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Jämför B2B-samarbete och B2C i Azure Active Directory

Både Azure Active Directory (AD Azure) B2B-samarbete och Azure AD B2C kan du arbeta med externa användare i Azure AD. Men hur jämför de?


Funktioner för B2B-samarbete |     Azure AD B2C fristående erbjudande
-------- | --------
Avsedd för: organisationer som vill kunna autentisera användare från en partnerorganisation, oavsett identitetsleverantör. | Avsedd för: bjuda in kunder i din mobil- och webb-appar om individer, institutionella eller organisationens kunder i din Azure AD.
Identiteter som stöds: anställda med arbets- eller skolkonton, samarbetar med arbets-eller skolkonton eller en e-postadress. Snart för att stödja direkt federation.  | Identiteter som stöds: konsumentanvändare med lokala program konton (alla e-adress eller användaren name) eller någon stöds sociala identitet med hjälp av direkt federation.
Vilka partner-användare finns i katalogen: Partner användare från externa organisation hanteras i samma katalog som anställda, men särskilt kommenterade. De kan hanteras på samma sätt som anställda kan läggas till samma grupper och så vidare  | Vilken katalog kunden användaren entiteter är i: I programkatalogen. Hanteras separat från organisationens anställda och partners directory (om sådan finns.
Enkel inloggning (SSO) till alla Azure AD-anslutna appar stöds. Du kan till exempel ge åtkomst till Office 365 eller lokala appar och andra SaaS-appar, till exempel Salesforce eller Workday.  |  Enkel inloggning till kund ägs appar i Azure AD B2C-klienter stöds. Enkel inloggning till Office 365 eller andra Microsoft och icke - Microsoft SaaS-appar stöds inte.
Partner livscykel: hanteras av den värd/bjuda in organisation.  | Kunden livscykel: självbetjäning eller hanteras av programmet.
Säkerhetsprinciper och efterlevnad: hanteras av den värd/bjuda in organisation.  | Säkerhetsprinciper och efterlevnad: hanteras av programmet.
Anpassning: Värd/bjuda in organisation varumärken används.  |    Anpassning: Hanteras av programmet. Vanligtvis brukar vara produkten som är märkta med organisation toning i bakgrunden.
Mer information: [blogginlägget](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentation](what-is-b2b.md)  | Mer information: [produktsidan](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Egenskaper för användare av B2B-samarbete](user-properties.md)

