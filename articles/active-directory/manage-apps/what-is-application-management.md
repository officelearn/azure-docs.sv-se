---
title: Hantera klientprogram med Azure Active Directory | Microsoft Docs
description: Den här artikeln presenterar fördelarna med att integrera Azure Active Directory med dina lokala program samt med moln- och SaaS-program.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/30/2018
ms.author: barbkess
ms.reviewer: arvinh
ms.openlocfilehash: e9cbc29950b1e5363691d44aa6e34391c541762b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155912"
---
# <a name="application-management-with-azure-active-directory"></a>Programhantering med Azure Active Directory

Azure Active Directory (Azure AD) ger säker och smidig åtkomst till både molnprogram och lokala program. Användare kan logga in en gång för att få åtkomst till Office 365 och andra affärsprogram från Microsoft, SaaS-program (Programvara som en tjänst), lokala program och verksamhetsspecifika appar. Minska administrativa kostnader genom att automatisera etableringen av användare. Använd multifaktorautentisering och principer för villkorsstyrd åtkomst för att ge säker åtkomst till program.

![Appar som federeras via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Varför hantera program med en molnlösning?

Organisationer har ofta hundratals program som användarna behöver för att utföra sitt arbete. Användare använder dessa program på många enheter och platser. Nya program läggs till, utvecklas och tas ur bruk varje dag. Med så många program och åtkomstpunkter är det mer kritiskt än någonsin att använda en molnbaserad lösning för att hantera åtkomst till alla program.

## <a name="manage-risk-with-conditional-access-policies"></a>Hantera risker med principer för villkorsstyrd åtkomst
Azure AD och enkel inloggning (SSO) tillsammans med principer för villkorsstyrd åtkomst ger mycket säker åtkomst till program. Säkerhetsfunktionerna omfattar identitetsskydd i molnskala, riskbaserad åtkomstkontroll, inbyggd multifaktorautentisering och principer för villkorsstyrd åtkomst. De här funktionerna möjliggör detaljerade kontrollprinciper baserade på program eller på grupper som behöver högre säkerhet.

## <a name="improve-productivity-with-single-sign-on"></a>Förbättra produktiviteten med enkel inloggning
Aktivera enkel inloggning (SSO) för alla program och för Office 365 för bästa möjliga inloggning för befintliga användare genom färre eller inga inloggningsuppmaningar. Användarnas miljöer känns mer sammanhängande och mindre störande med färre uppmaningar och utan behov för flera lösenord. Affärsgruppen kan hantera och godkänna åtkomst via självbetjäning och dynamiska medlemskap. Då rätt personer i företaget hanterar åtkomst till program förbättras även säkerheten för ID-system.

Enkel inloggning ger bättre säkerhet. *Utan enkel inloggning* måste administratörer skapa och uppdatera användarkonton för varje enskilt program, vilket kräver tid. Användare måste dessutom hålla reda på flera autentiseringsuppgifter för att använda olika program. Därmed tenderar användarna att skriva ned sina lösenord eller använda andra lösningar för lösenordshantering, vilket medför datasäkerhetsrisker. 

## <a name="address-governance-and-compliance"></a>Ta itu med styrning och efterlevnad
Med Azure AD kan du övervaka programinloggningar via rapporter som utnyttjar verktyg för säkerhetsinformation och händelsehantering (SIEM). Du kommer åt rapporterna i portalen eller med API:er. Granska programmatiskt vem som har åtkomst till dina program och ta bort åtkomst för inaktiva användare via åtkomstgranskningar.

## <a name="manage-costs"></a>Hantera kostnader
Genom att migrera till Azure AD kan du minska kostnaderna och slippa besväret med att hantera en lokal infrastruktur. Azure AD tillhandahåller även åtkomst till program via självbetjäning, vilket sparar tid för både administratörer och användare. Enkel inloggning eliminerar programspecifika lösenord. Den här möjligheten att logga in en enda gång sänker kostnaderna i samband med återställning av lösenord för program och förlorad produktivitet under hämtning av lösenord.

