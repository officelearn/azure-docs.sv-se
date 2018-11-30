---
title: Azure MFA-versioner och förbrukningsplaner | Microsoft Docs
description: Information om Multi-Factor Authentication-klienten och olika metoder och versioner som är tillgängliga.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 393b90395698c18fdbd4fc8ba4d8bc79bd6287be
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495182"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Så här hämtar du Azure Multi-Factor Authentication

När det gäller att skydda dina konton ska tvåstegsverifiering vara standard för din organisation. Den här funktionen är särskilt viktigt för konton som har privilegierad åtkomst till resurser. Därför Microsoft erbjuder grundläggande tvåstegsverifiering verifiering funktioner på Office 365 och Azure Active Directory (Azure AD)-administratörer för utan extra kostnader. Om du vill uppgradera funktioner för dina administratörer eller utöka tvåstegsverifiering till resten av dina användare kan du köpa Azure Multi-Factor Authentication på flera olika sätt.

> [!IMPORTANT]
> Den här artikeln är avsedd att vara en guide som hjälper dig att förstå de olika sätten att köpa Azure Multi-Factor Authentication. Specifik information om priser och fakturering du bör alltid använda den [Multifaktorautentisering prissättningssidan](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Tillgängliga versioner av Azure Multi-Factor Authentication

I följande tabell beskrivs skillnaderna mellan tre versioner av multifaktorautentisering:

| Version | Beskrivning |
| --- | --- |
| Multi-Factor Authentication för Office 365 |Den här versionen fungerar enbart med Office 365-program och hanteras via Office 365-portalen. Administratörer kan [skydda Office 365-resurser med tvåstegsverifiering](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Den här versionen är en del av Office 365-prenumeration. |
| Multi-Factor Authentication för Azure AD-administratörer | Användare som har tilldelats rollen Global administratör för Azure AD i Azure AD-klienter kan aktivera tvåstegsverifiering utan extra kostnad.|
| Azure Multi-Factor Authentication | Azure Multi-Factor Authentication erbjuder kallas ofta ”full” version, den bästa möjliga uppsättningen funktioner. Det ger ytterligare konfigurationsalternativ via den [Azure-portalen](https://portal.azure.com), avancerad rapportering och stöd för flera olika lokala och molnprogram. Azure Multi-Factor Authentication är en funktion i [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features), och kan distribueras antingen i molnet eller lokalt. |

> [!NOTE]
> Nya kunder kan inte längre att köpa Azure Multi-Factor Authentication som en fristående erbjuder effektiva September 1 2018. Multifaktorautentisering fortsätter att vara tillgängliga som en funktion i Azure AD Premium-licenser.

## <a name="feature-comparison-of-versions"></a>Jämförelse av versioner

Följande tabell innehåller en lista över de funktioner som är tillgängliga i de olika versionerna av Azure Multi-Factor Authentication.

> [!NOTE]
> Den här jämförelsetabellen beskrivs de funktioner som ingår i varje version av Multi-Factor Authentication. Om du har fullständig Azure Multi-Factor Authentication-tjänsten kan vissa funktioner kanske inte är tillgängliga beroende på om du använder [lokala MFA i molnet eller MFA](concept-mfa-whichversion.md).
>

| Funktion | Multi-Factor Authentication för Office 365 | Multi-Factor Authentication för Azure AD-administratörer | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Skydda Azure AD-administratörskonton med MFA |● |● (endast Global administratör för Azure AD-konton) |● |
| Mobilappen som en andra faktor |● |● |● |
| Telefonsamtal som en andra faktor |● |● |● |
| SMS som en andra faktor |● |● |● |
| Applösenord för klienter som inte stöder MFA |● |● |● |
| Administratörskontroll över verifieringsmetoder |● |● |● |
| Skydda konton med MFA |● (endast för Office 365-program) | |● |
| PIN-läge | | |● |
| Bedrägerivarning | | |● |
| MFA-rapporter | | |● |
| Engångsförbikoppling | | |● |
| Anpassade hälsningar för telefonsamtal | | |● |
| Anpassade Nummerpresentation för telefonsamtal | | |● |
| Tillförlitliga IP-adresser | | |● |
| MFA sparas för betrodda enheter |● |● |● |
| MFA för lokala program | | |● |

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Aktivera Azure Multi-Factor Authentication för Azure AD-administratörer

Användare som har tilldelats rollen Global administratör i Azure AD-klienter kan aktivera tvåstegsverifiering för sina Azure AD globala administratörskonton utan extra kostnad. Om du använder ett Account kan du registrera dig för multifaktorautentisering med hjälp av vägledningen i Microsoft-konto support-artikeln [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Om du inte använder ett Account, aktivera multifaktorautentisering för globala administratörer med hjälp av vägledningen som finns i artikeln [kräva tvåstegsverifiering för en användare eller grupp](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Så här köper du Azure Multi-Factor Authentication

Om du vill ha den fullständiga funktionaliteten som erbjuds av Azure Multi-Factor Authentication, finns det flera alternativ:

### <a name="option-1---licenses-that-include-mfa"></a>Alternativ 1 - licenser som täcks av MFA

Köpa licenser som täcks av Azure Multi-Factor Authentication, som Azure Active Directory Premium eller ett licens-paket som innehåller Azure AD Premium, och tilldela dem till dina användare i Azure Active Directory.

### <a name="option-2---mfa-consumption-based-model"></a>Alternativ 2 – MFA konsumtionsbaserad modell

Det här alternativet är inte längre tillgängliga för nya kunder effektiva den 1 September 2018.

Effektiva den 1 September 2018 nya auth-providers kan inte längre skapas. Befintliga autentiseringsleverantörer kan fortsätta att användas och uppdateras. Multifaktorautentisering fortsätter att vara en tillgänglig funktion i Azure AD Premium-licenser.

När du använder en Azure Multi-Factor Authentication-Provider, finns det två användningsmodeller som är tillgängliga som faktureras via din Azure-prenumeration:

1. **Per aktiverad användare** – för företag som vill aktivera tvåstegsverifiering för ett fast antal anställda som regelbundet behöver autentisering. Fakturering per användare baseras på antalet användare som har aktiverats för MFA i Azure AD-klienten och Azure MFA-servern. Om användarna är aktiverade för MFA i både Azure AD och Azure MFA Server och är aktiverad för synkronisering av domänen (Azure AD Connect) och sedan vi räknar större uppsättning användare. Om inte är aktiverad för synkronisering av domänen, så vi räknar summan av alla användare som har aktiverats för MFA i Azure AD och Azure MFA Server. Fakturering beräknas och har rapporterats till handelssystem dagligen.

  > [!NOTE]
  > Fakturering – exempel 1: du har 5 000 användare som har aktiverats för MFA idag. MFA-systemet dividerar det numret med 31 och rapporter 161.29 användare för den dagen. Du aktiverar morgon 15 fler användare, så att systemet MFA rapporterar 161.77 användare för den dagen. I slutet av faktureringsperioden lägger det totala antalet användare som faktureras mot din Azure-prenumeration upp till runt 5 000.
  >
  > Fakturering exempel 2: du har en blandning av användare med licenser och användare utan, så att du har en per användare Azure MFA-Provider till utgör skillnaden. Det finns 4 500 Enterprise Mobility + Security-licenser på din klient, men 5 000 användare som har aktiverats för MFA. Din Azure-prenumeration faktureras för 500 användare, beräknas och rapporteras per dag som 16.13 användare.
  >

1. **Per autentisering** – för företag som vill aktivera tvåstegsverifiering för en stor grupp med användare som behöver sällan autentisering. Fakturering baseras på antalet begäranden om tvåstegsverifiering, oavsett om dessa verifiering lyckas eller nekas. Den här Faktureringsmetoden visas på ditt kontoutdrag för Azure-användning i packs om 10 autentiseringar och rapporteras per dag.

  > [!NOTE]
  > Fakturering exempel 3: idag, Azure MFA-tjänsten har tagit emot 3,105 begäranden om tvåstegsverifiering. Din Azure-prenumeration faktureras efter 310.5 autentisering hanteringspaket.
  >

Det är viktigt att Observera att du kan ha licenser, men får betala för förbrukning-baserad konfiguration. Om du har konfigurerat en per autentisering Azure MFA-Provider, debiteras du för varje förfrågningar om tvåstegsverifiering, även de som görs av användare som har licenser. Om du ställer in en per användare Azure MFA-Provider på en domän som inte är länkad till din Azure AD-klient, debiteras du per aktiverad användare även om användarna har licenser på Azure AD.

## <a name="next-steps"></a>Nästa steg

- Mer information om priser finns i [priser för Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Välj om du vill distribuera Azure MFA [i molnet eller lokalt](concept-mfa-whichversion.md)
