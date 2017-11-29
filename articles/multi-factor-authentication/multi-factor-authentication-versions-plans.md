---
title: "Azure MFA-versioner och förbrukning planer | Microsoft Docs"
description: "Information om Multifaktorautentisering klienten och olika metoder och versioner som är tillgängliga. Information om varje förbrukning plan"
keywords: 
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: ddac8feedc8ded73e0f436c1e5dd6391016943a9
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Hur du hämtar Azure Multi-Factor Authentication

När det gäller att skydda dina konton, vara tvåstegsverifiering standard i din organisation. Den här funktionen är särskilt viktigt för administrativa konton som har privilegierad åtkomst till resurser. Därför Microsoft erbjuder grundläggande tvåstegsverifiering verifiering funktioner på Office 365 och Azure administratörer för inget extra kostnad. Om du vill uppgradera funktionerna för dina administratörer eller utöka tvåstegsverifiering till resten av dina användare kan du köpa Azure Multi-Factor Authentication. 

Den här artikeln förklarar skillnaden mellan de versioner som erbjuds för administratörer och den fullständiga versionen av Azure MFA. Om du är redo att distribuera den fullständiga Azure MFA ger beskriver senare avsnitt implementeringsalternativ och hur Microsoft beräknar förbrukning.


>[!IMPORTANT]
>Den här artikeln är avsedd att vara en guide som hjälper dig att förstå de olika sätten att köpa Azure Multi-Factor Authentication. Specifik information om priser och fakturering bör du alltid referera till den [Multifaktorautentisering sida med priser](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Tillgängliga versioner av Azure Multi-Factor Authentication

I följande tabell beskrivs skillnaderna mellan tre versioner av Multi-Factor authentication:

| Version | Beskrivning |
| --- | --- |
| Multi-Factor Authentication för Office 365 |Den här versionen fungerar endast med Office 365-program och hanteras från Office 365-portalen. Administratörer kan [skydda Office 365-resurser med tvåstegsverifiering](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Den här versionen är en del av en prenumeration på Office 365. |
| Multi-Factor Authentication för Azure-administratörer | Globala administratörer av Azure innehavare kan aktivera tvåstegsverifiering för sina globala administratörskonton utan extra kostnad.|
| Azure Multi-Factor Authentication | Azure Multi-Factor Authentication erbjuder kallas ofta ”full” versionen och bra uppsättning funktioner. Det ger ytterligare konfigurationsalternativ via den [klassiska Azure-portalen](https://manage.windowsazure.com)avancerade rapportering och stöd för ett antal lokala program och molnprogram. Azure Multi-Factor Authentication ingår i [Azure Active Directory Premium-planer](https://www.microsoft.com/cloud-platform/azure-active-directory-features) och [Enterprise Mobility + säkerhetsplaner](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing), och kan användas antingen i molnet eller lokalt. |

## <a name="feature-comparison-of-versions"></a>Funktionsjämförelse mellan versioner
Följande tabell innehåller en lista över funktioner som är tillgängliga i de olika versionerna av Azure Multi-Factor Authentication.

> [!NOTE]
> Den här jämförelsetabellen beskrivs de funktioner som ingår i varje version av Multi-Factor Authentication. Om du har fullständig Azure Multi-Factor Authentication-tjänsten kan vissa funktioner kanske inte är tillgängliga beroende på om du använder [MFA i molnet eller MFA lokalt](multi-factor-authentication-get-started.md).


| Funktion | Multi-Factor Authentication för Office 365 | Multi-Factor Authentication för Azure-administratörer | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Skydda administratörskonton med MFA |● |● (endast för konton i en Global administratör) |● |
| Mobilappar som ett andra alternativ |● |● |● |
| Telefonsamtal som ett andra alternativ |● |● |● |
| SMS som ett andra alternativ |● |● |● |
| Applösenord för klienter som inte har stöd för MFA |● |● |● |
| -Administratörer kontroll över verifieringsmetoderna |● |● |● |
| Skydda icke-administratörskonton med MFA |● (endast för Office 365-program) | |● |
| PIN-läge | | |● |
| Bedrägerivarning | | |● |
| MFA-rapporter | | |● |
| Engångsförbikoppling | | |● |
| Anpassade hälsningar för telefonsamtal | | |● |
| Anpassade Uppringarens ID för telefonsamtal | | |● |
| Tillförlitliga IP-adresser | | |● |
| MFA sparas för betrodda enheter |● |● |● |
| MFA SDK | | |● (föråldrad) | 
| MFA för lokala program | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>Hur du hämtar Azure Multi-Factor Authentication
Om du vill att alla funktioner som erbjuds av Azure Multi-Factor Authentication, finns det flera alternativ:

### <a name="option-1---mfa-licenses"></a>Alternativ 1 - MFA-licenser

Köpa licenser för Azure Multi-Factor Authentication och tilldela dem till användare i Azure Active Directory. 

Om du använder det här alternativet kan endast skapa Azure Multi-Factor Authentication-leverantör om du behöver ange tvåstegsverifiering för vissa användare som inte har licenser. Annars kan kanske du att debiteras två gånger.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>Alternativ 2 - paketerade licenser som innehåller MFA

Köpa licenser som innehåller Azure Multi-Factor Authentication som Azure Active Directory Premium eller Enterprise Mobility + Security och tilldela dem till användare i Azure Active Directory. 

Om du använder det här alternativet kan skapa du Azure Multi-Factor Authentication-leverantör om du behöver ange tvåstegsverifiering för vissa användare som inte har licenser. Annars kan kanske du att debiteras två gånger. 

### <a name="option-3---mfa-consumption-based-model"></a>Alternativ 3 - MFA förbrukningsbaserad modellen

Skapa ett Azure Multi-Factor Authentication-leverantör i en Azure-prenumeration. Azure MFA-Providers är Azure-resurser som debiteras mot din Enterprise-avtal, Azure i förskott eller kreditkort som alla andra Azure-resurser. Dessa providers kan bara skapas i kompletta Azure-prenumerationer inte begränsat Azure-prenumerationer som har ett $-0 utgiftsgräns. Begränsad prenumerationer skapas när du aktiverar licenser, som i alternativ 1 och 2. 

När du använder Azure Multi-Factor Authentication-leverantör, finns det två användning modeller som är tillgängliga som debiteras via din Azure-prenumeration:  

1. **Per aktiverad användare** – för företag som vill aktivera tvåstegsverifiering för ett fast antal anställda som regelbundet behöver autentisering. Per användare fakturering baseras på antalet användare som har aktiverats för Multifaktorautentisering i Azure AD-klient och Azure MFA-Server. Om användarna har aktiverats för MFA i både Azure AD och Azure MFA-Server och domän synkronisering (Azure AD Connect) är aktiverat och sedan vi räkna större uppsättning användare. Om domänen synkronisering har inte aktiverats, så vi räkna summan av alla användare som har aktiverats för Multifaktorautentisering i Azure AD och Azure MFA-Server. Fakturering linjärt och rapporteras till Commerce-systemet varje dag. 

  > [!NOTE]
  > Fakturering exempel 1: du har 5 000 användare som har aktiverats för MFA idag. Systemets MFA dividerar numret med 31 och rapporter 161.29 användare för den dagen. I morgon aktiverar du 15 fler användare, så att systemet MFA rapporterar 161.77 användare för den dagen. I slutet av faktureringsperioden läggs det totala antalet användare som faktureras mot Azure-prenumerationen upp till runt 5 000. 
  >
  > Fakturering exempel 2: du har en blandning av användare med licenser och användare utan, så att du har en användarspecifik Azure MFA-leverantör för att skapa skillnaden. Det finns 4,500 Enterprise Mobility + Security licenser på din klient, men 5 000 användare som har aktiverats för Multifaktorautentisering. Din Azure-prenumeration debiteras för 500 användare, linjärt och rapporteras dagligen som 16.13 användare. 

2. **Per autentisering** – för företag som vill aktivera tvåstegsverifiering för en stor grupp med användare som behöver autentisering mer sällan. Fakturering baseras på antalet tvåstegsverifiering verifiering begäranden, oavsett om dessa kontroller lyckas eller nekas. Den här fakturering visas på din användning av Azure-instruktionen i packs av 10 autentiseringar och rapporteras varje dag. 

  > [!NOTE]
  > Fakturering exempel 3: idag Azure MFA-tjänsten har tagit emot 3,105 tvåstegsverifiering verifiering begäranden. Din Azure-prenumeration faktureras för 310.5 autentisering packs. 

Det är viktigt att Observera att du kan ha Azure MFA licenser, men fortfarande få debiteras för förbrukning-baserad konfiguration. Om du ställer in en per autentisering Azure MFA-leverantören debiteras du för varje begäran om identitetsverifiering tvåstegsverifiering, även de som utförs av användare som har licenser. Om du ställer in en användarspecifik Azure MFA-Provider på en domän som inte är länkad till din Azure AD-klient debiteras du per aktiverad användare även om användarna har licenser på Azure AD. 

## <a name="next-steps"></a>Nästa steg

- Mer information om priser finns i [priser för Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Välj om du vill distribuera Azure MFA [i molnet eller lokalt](multi-factor-authentication-get-started.md)
