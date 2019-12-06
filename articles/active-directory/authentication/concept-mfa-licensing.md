---
title: Azure MFA-versioner och förbruknings planer – Azure Active Directory
description: Information om Multi-Factor Authentication-klienten och de olika metoder och versioner som är tillgängliga.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61a2afad33aea7fa2a21135dca0995ad862a2258
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848671"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Så här hämtar du Azure-Multi-Factor Authentication

När det gäller att skydda dina konton bör tvåstegsverifiering vara standard i hela organisationen. Den här funktionen är särskilt viktig för konton som har privilegie rad åtkomst till resurser. Av den anledningen erbjuder Microsoft grundläggande verifierings funktioner i två steg till Office 365 och Azure Active Directory (Azure AD)-administratörer utan extra kostnad. Om du vill uppgradera funktionerna för dina administratörer eller utöka tvåstegsverifiering till resten av användarna kan du köpa Azure Multi-Factor Authentication på flera olika sätt.

> [!IMPORTANT]
> Den här artikeln är avsedd att vara en guide som hjälper dig att förstå de olika sätten att köpa Azure Multi-Factor Authentication. För detaljerad information om priser och fakturering bör du alltid referera till [sidan Multi-Factor Authentication priser](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Tillgängliga versioner av Azure Multi-Factor Authentication

I följande tabell beskrivs skillnaderna mellan versioner av Multi-Factor Authentication:

| Version | Beskrivning |
| --- | --- |
| Kostnads fritt alternativ | Kunder som utnyttjar de kostnads fria fördelarna med Azure AD kan använda [säkerhets inställningar](../conditional-access/concept-conditional-access-security-defaults.md) för att aktivera Multi-Factor Authentication i sin miljö. |
| Multi-Factor Authentication för Office 365 | Den här versionen hanteras från Office 365 eller Microsoft 365-portalen. Administratörer kan [skydda Office 365-resurser med](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)tvåstegsverifiering. Den här versionen ingår i en Office 365-prenumeration. |
| Multi-Factor Authentication för Azure AD-administratörer | Användare som har tilldelats rollen global administratör i Azure AD i Azure AD-klienter kan aktivera tvåstegsverifiering utan extra kostnad. |
| Azure Multi-Factor Authentication | Azure Multi-Factor Authentication kallas ofta för "full"-versionen och erbjuder de många funktioner som finns. Det ger ytterligare konfigurations alternativ via [Azure Portal](https://portal.azure.com), avancerad rapportering och stöd för en rad lokala program och moln program. Azure Multi-Factor Authentication är en funktion i [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) och [Microsoft 365 Business](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> Nya kunder kan inte längre köpa Azure Multi-Factor Authentication som ett fristående erbjudande från den 1 september 2018. Multi-Factor Authentication kommer att fortsätta vara tillgängligt som en funktion i Azure AD Premium-eller Microsoft 365 Business licenser.

## <a name="feature-comparison-of-versions"></a>Funktions jämförelse av versioner

Följande tabell innehåller en lista över de funktioner som är tillgängliga i olika versioner av Azure Multi-Factor Authentication.

> [!NOTE]
> I jämförelse tabellen diskuteras de funktioner som ingår i varje version av Multi-Factor Authentication. Om du har fullständig Azure Multi-Factor Authentication-tjänst kanske vissa funktioner inte är tillgängliga beroende på om du använder [MFA i molnet eller MFA lokalt](concept-mfa-whichversion.md).
>

| Funktion | Multi-Factor Authentication för Office 365 | Multi-Factor Authentication för Azure AD-administratörer | Azure Multi-Factor Authentication | Standardinställningar för säkerhet | 
| --- |:---:|:---:|:---:|:---:|
| Skydda Azure AD admin-konton med MFA |● |● (Endast Azure AD global administratörs konton) |● |● |
| Mobilapp som en andra faktor |● |● |● |● |
| Telefonsamtal som en andra faktor |● |● |● |   |
| SMS som en andra faktor |● |● |● |   |
| Applösenord för klienter som inte stöder MFA |● |● |● |   |
| Administratörs kontroll över verifierings metoder |● |● |● |   |
| Skydda konton som inte är administratörer med MFA |● | |● |● |
| PIN-läge | | |● |   |
| Bedrägerivarning | | |● |   |
| MFA-rapporter | | |● |   |
| Förbikoppling, en gång | | |● |   |
| Anpassade hälsningar för telefonsamtal | | |● |   |
| Anpassat samtals-ID för telefonsamtal | | |● |   |
| Tillförlitliga IP-adresser | | |● |   |
| MFA sparas för betrodda enheter |● |● |● |   |
| MFA för lokala program | | |● |   |

> [!IMPORTANT]
> Från och med mars 2019 kommer Telefonsamtals alternativen inte att vara tillgängliga för MFA-och SSPR-användare i kostnads fria/utvärderings versioner av Azure AD-klienter. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtalet fortsätter att vara tillgängligt för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnads fria/utvärderings versioner av Azure AD.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Aktivera Azure-Multi-Factor Authentication för Azure AD-administratörer

Användare som har tilldelats rollen global administratör i Azure AD-klienter kan aktivera tvåstegsverifiering för sina Azure AD global administratörs konton utan extra kostnad. Om du använder ett Microsoft-konto kan du registrera dig för Multi-Factor Authentication med hjälp av rikt linjerna i Microsoft-konto Support artikel [Om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Om du inte använder ett Microsoft-konto aktiverar du Multi-Factor Authentication för globala administratörer med hjälp av rikt linjerna i artikeln [så här kräver du tvåstegsverifiering för en användare eller grupp](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Så här köper du Azure-Multi-Factor Authentication

Köp licenser som innehåller Azure Multi-Factor Authentication, till exempel Azure Active Directory Premium eller ett licens paket som innehåller Azure AD Premium eller villkorlig åtkomst och tilldela dem till dina användare i Azure Active Directory.

### <a name="consumption-based-licensing"></a>Konsumtions-baserad licensiering

Konsumtions-baserad licens är inte längre tillgänglig för nya kunder från och med den 1 september 2018.

Från och med 1 september 2018 kan nya auth providers inte längre skapas. Befintliga auth-providers kan fortsätta att användas och uppdateras. Multi-Factor Authentication kommer att fortsätta vara en tillgänglig funktion i Azure AD Premium licenser.

När du använder en Azure Multi-Factor Authentication-provider finns det två tillgängliga användnings modeller som debiteras genom din Azure-prenumeration:

1. **Per aktive rad användare** – för företag som vill aktivera tvåstegsverifiering för ett fast antal medarbetare som regelbundet behöver autentisering. Fakturering per användare baseras på antalet användare som Aktiver ATS för MFA i din Azure AD-klient och din Azure MFA-Server. Om användarna är aktiverade för MFA i både Azure AD och Azure MFA Server, och om domän synkronisering (Azure AD Connect) är aktive rad, så räknar vi den större uppsättningen användare. Om domän synkronisering inte är aktive rad räknas summan av alla användare som är aktiverade för MFA i Azure AD och Azure MFA Server. Faktureringen beräknas och rapporteras till Commerce system dagligen.

   > [!NOTE]
   > Fakturerings exempel 1: du har 5 000 användare aktiverade för MFA idag. MFA-systemet delar in den siffran med 31 och rapporterar 161,29 användare för den dagen. I framtiden aktiverar du ytterligare 15 användare, så MFA-systemet rapporterar 161,77 användare för den dagen. I slutet av fakturerings perioden lägger det totala antalet användare som faktureras mot din Azure-prenumeration upp till cirka 5 000.
   >
   > Fakturerings exempel 2: du har en blandning av användare med licenser och användare utan, så att du kan använda Azure MFA-providern per användare för att utföra skillnaden. Det finns 4 500 Enterprise Mobility + Security licenser för din klient, men 5 000 användare som är aktiverade för MFA. Din Azure-prenumeration debiteras för 500 användare, proportionellt och rapporteras dagligen som 16,13-användare.
   >

1. **Per autentisering** – för företag som vill aktivera tvåstegsverifiering för en stor grupp användare som sällan behöver autentisering. Faktureringen baseras på antalet begär Anden om tvåstegsverifiering, oavsett om kontrollerna lyckas eller nekas. Faktureringen visas i din Azure-användnings rapport i paket om 10 autentiseringar och rapporteras dagligen.

   > [!NOTE]
   > Fakturerings exempel 3: idag tog Azure MFA-tjänsten emot 3 105 2-steg-verifierings begär Anden. Din Azure-prenumeration debiteras för 310,5-autentiseringspaket.
   >

Det är viktigt att du noterar att du kan ha licenser, men ändå debiteras för konsumtions-baserad konfiguration. Om du konfigurerar en Azure MFA-Provider per autentisering debiteras du för varje tvåstegsverifiering, även de begär Anden som utförs av användare som har licenser. Om du konfigurerar en Azure MFA-Provider per användare på en domän som inte är länkad till din Azure AD-klient debiteras du per aktive rad användare även om dina användare har licenser i Azure AD.

## <a name="next-steps"></a>Nästa steg

- Mer pris information finns i [priser för Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
