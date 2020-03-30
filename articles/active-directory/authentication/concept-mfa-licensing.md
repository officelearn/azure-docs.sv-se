---
title: Versioner och förbrukningsplaner för Azure Multi Factor-autentisering
description: Lär dig mer om Azure Multi-factor Authentication-klienten och olika metoder och versioner som är tillgängliga.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648010"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funktioner och licenser för Azure Multi-Factor-autentisering

För att skydda användarkonton i organisationen bör multifaktorautentisering användas. Den här funktionen är särskilt viktig för konton som har privilegierad åtkomst till resurser. Grundläggande multifaktorautentiseringsfunktioner är tillgängliga för Office 365- och Azure Active Directory-administratörer (Azure AD) utan extra kostnad. Om du vill uppgradera funktionerna för administratörerna eller utöka multifaktorautentiseringen till resten av användarna kan du köpa Azure Multi-Factor Authentication på flera sätt.

> [!IMPORTANT]
> I den här artikeln beskrivs de olika sätt som Azure Multi-Factor Authentication kan licensieras och användas. Mer information om prissättning och fakturering finns på prissidan för [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Tillgängliga versioner av Azure Multi Factor-autentisering

Azure Multi-Factor Authentication kan användas och licensieras på några olika sätt beroende på organisationens behov. Du kanske redan har rätt att använda Azure Multi-Factor Authentication beroende på Azure AD-, Office 365-, EMS- eller Microsoft 365-licensen som du för närvarande har. I följande tabell beskrivs olika sätt att hämta Azure Multi-Factor-autentisering och några av de funktioner och användningsfall för varje.

| Om du använder | Funktioner och användningsfall |
| --- | --- |
| EMS eller Microsoft 365 E3 och E5 | EMS E3 eller Microsoft 365 E3 (som inkluderar EMS och Office 365), innehåller Azure AD Premium P1. EMS E5 eller Microsoft 365 E5 innehåller Azure AD Premium P2. Du kan använda samma villkorsstyrda åtkomstfunktioner som anges i följande avsnitt för att ge flerafaktorsautentiseringer till användare. |
| Azure AD Premium P1 | Du kan använda [Azure AD Villkorlig åtkomst](../conditional-access/overview.md) för att fråga användare om multifaktorautentisering under vissa scenarier eller händelser som passar dina affärskrav. |
| Azure AD Premium P2 | Ger den starkaste säkerhetspositionen och förbättrad användarupplevelse. Lägger till [riskbaserad villkorlig åtkomst](../conditional-access/howto-conditional-access-policy-risk.md) till Azure AD Premium P1-funktionerna som anpassar sig till användarens mönster och minimerar autentiseringsmeddelanden med flera faktorer. |
| Office 365 Business Premium, E3 eller E5 | Azure Multi-Factor Authentication är antingen aktiverat eller inaktiverat för alla användare, för alla inloggningshändelser. Det finns ingen möjlighet att bara aktivera multifaktorautentisering för en delmängd av användare, eller bara under vissa scenarier. Ledningen sker via Office 365-portalen. För en förbättrad användarupplevelse, uppgradera till Azure AD Premium P1 eller P2 och använda villkorlig åtkomst. Mer information finns i [säkra Office 365-resurser med multifaktorautentisering](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD Kostnadsfri | Du kan använda [säkerhetsstandarder](../fundamentals/concept-fundamentals-security-defaults.md) för att aktivera multifaktorautentisering för alla användare, varje gång en autentiseringsbegäran görs. Du har inte detaljerad kontroll över aktiverade användare eller scenarier, men det ger det ytterligare säkerhetssteget.<br /> Även om standardvärden för säkerhet inte används för att aktivera multifaktorautentisering för alla kan användare som tilldelats azure *AD Global Administrator-rollen* konfigureras för att använda multifaktorautentisering. Den här funktionen på den kostnadsfria nivån säkerställer att de kritiska administratörskontona skyddas av multifaktorautentisering. |

## <a name="feature-comparison-of-versions"></a>Jämförelse av funktioner av versioner

Följande tabell innehåller en lista över de funktioner som är tillgängliga i de olika versionerna av Azure Multi-Factor Authentication. Planera dina behov för att skydda användarautentisering och bestäm sedan vilken metod som uppfyller dessa krav. Även om Azure AD Free till exempel tillhandahåller standardvärden för säkerhet som ger Azure Multi-Factor Authentication, kan endast den mobila autentiseringsappen användas för autentiseringsprompten, inte ett telefonsamtal eller SMS. Den här metoden kan vara en begränsning om du inte kan se till att mobilappen för mobilautentisering är installerad på en användares personliga enhet.

| Funktion | Azure AD Free - Säkerhet standardvärden | Azure AD Free - Azure AD-globala administratörer | Office 365 Business Premium, E3 eller E5 | Azure AD Premium P1 eller P2 |
| --- |:---:|:---:|:---:|:---:|
| Skydda Azure AD-klientadministratörskonton med MFA | ● | ● (endast*Azure AD Global Administrator-konton)* | ● | ● |
| Mobilapp som en andra faktor | ● | ● | ● | ● |
| Telefonsamtal som en andra faktor | | ● | ● | ● |
| SMS som en andra faktor | | ● | ● | ● |
| Administratörskontroll över verifieringsmetoder | | ● | ● | ● |
| Bedrägerivarning | | | | ● |
| MFA-rapporter | | | | ● |
| Anpassade hälsningar för telefonsamtal | | | | ● |
| Anpassat nummerpresentatör för telefonsamtal | | | | ● |
| Tillförlitliga IP-adresser | | | | ● |
| MFA sparas för betrodda enheter | | ● | ● | ● |
| MFA för lokala applikationer | | | | ● |

> [!IMPORTANT]
> Från och med mars 2019 är samtalsalternativ inte längre tillgängliga för Azure Multi-Factor Authentication och Azure Self-Service Password Reset-användare i Azure AD Free / trial-klienter. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtal fortsätter att vara tillgängliga för användare i Azure AD Premium P1- eller P2-klienter eller använder eller Office 365 Business Premium, E3 eller E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Köpa och aktivera Azure Multi-Factor-autentisering

Om du vill använda Azure Multi-Factor Authentication registrerar du dig för eller köper en berättigad Azure AD-nivå. Azure AD finns i fyra versioner – Kostnadsfri, Office 365-apputgåva (för Office 365 Business Premium E3 eller E5-kunder), Premium P1 och Premium P2.

Den kostnadsfria versionen ingår i en Azure-prenumeration. Se [avsnittet nedan](#azure-ad-free-tier) för information om hur du använder säkerhetsstandarder eller skyddar konton med azure *AD Global Administrator-rollen.*

Azure AD Premium-utgåvorna är tillgängliga via din Microsoft-representant, [Open Volume License Program](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)och Cloud Solution Providers.The Azure AD Premium editions are available through your Microsoft representative, the Open Volume License Program , and the Cloud Solution Providers [program](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Azure- och Office 365-prenumeranter kan också köpa Azure Active Directory Premium P1 och P2 online. [Logga in](https://portal.office.com/Commerce/Catalog.aspx) för att köpa.

> [!IMPORTANT]
> Konsumtionsbaserad licensiering är inte längre tillgänglig för nya kunder från och med den 1 september 2018. Befintliga kunder som använder den förbrukningsbaserade modellen kan fortsätta att använda antingen per aktiverad användare eller per autentiseringsfakturering.

När du har köpt den nödvändiga Azure [AD-nivån planerar och distribuerar du Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Kostnadsfri azure AD-nivå

Alla användare i en Azure AD Free-klient kan använda Azure Multi-Factor-autentisering med hjälp av säkerhetsstandarder. Dessa säkerhetsstandarder aktiverar Azure Multi-Factor-autentisering för alla användare, varje gång de loggar in. Den mobila autentiseringsappen är den enda metoden som kan användas för Azure Multi-Factor Authentication när du använder Azure AD Free-säkerhetsstandarder.

* [Läs mer om standardinställningar för Azure AD-säkerhet](../fundamentals/concept-fundamentals-security-defaults.md)
* [Aktivera standardvärden för säkerhet för användare i Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Om du inte vill aktivera Azure Multi-Factor-autentisering för alla användare och varje inloggningshändelse kan du i stället välja att bara skydda användarkonton med azure *AD Global Administrator-rollen.* Den här metoden innehåller ytterligare autentiseringsuppmaningar för kritiska administratörskonton. Du aktiverar Azure Multi Factor-autentisering på något av följande sätt, beroende på vilken typ av konto du använder:

* Om du använder ett [Microsoft-konto registrerar du dig för multifaktorautentisering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Om du inte använder ett [Microsoft-konto aktiverar du multifaktorautentisering för en användare eller grupp i Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Nästa steg

Mer information om kostnader finns i [azure multifaktorautentisering.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)
