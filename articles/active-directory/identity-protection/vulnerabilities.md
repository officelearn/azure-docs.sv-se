---
title: Sårbarheter som upptäckts av Azure Active Directory Identity Protection
description: Översikt över de sårbarheter som upptäckts av Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335146"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Sårbarheter som upptäckts av Azure Active Directory Identity Protection

Sårbarheter är svagheter i en miljö som kan utnyttjas av en angripare. Vi rekommenderar administratörer att lösa dessa sårbarheter för att förbättra organisationens säkerhets position.

![Sårbarheter rapporterade av identitets skydd](./media/vulnerabilities/identity-protection-vulnerabilities.png)

I följande avsnitt får du en översikt över de sårbarheter som rapporteras av identitets skyddet.

## <a name="multi-factor-authentication-registration-not-configured"></a>Multi-Factor Authentication-registrering har inte kon figurer ATS

Den här säkerhets risken hjälper till att utvärdera distributionen av Azure Multi-Factor Authentication i din organisation.

Azure Multi-Factor Authentication tillhandahåller ett andra säkerhets lager för användarautentisering. Det hjälper till att skydda åtkomsten till data och program samtidigt som du kan möta användarnas behov av en enkel inloggnings process. Med Azure Multi-Factor Authentication kan du enkelt använda verifierings alternativ som:

* Telefonsamtal
* Textmeddelande
* Mobilapp-meddelande
* Verifiera kod för eng ång slö sen ord

Vi rekommenderar att du kräver Azure Multi-Factor Authentication för användar inloggningar. Multi-Factor Authentication spelar en viktig roll i riskfyllda villkorliga åtkomst principer som är tillgängliga via identitets skydd.

Mer information finns i [Vad är Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Ohanterade molnappar

Denna sårbarhet hjälper dig att identifiera ohanterade molnappar i din organisation.

IT-personalen är ofta inte medvetna om alla moln program i organisationen. Det är enkelt att se varför administratörer har problem med obehörig åtkomst till företags data, möjliga data läckage och andra säkerhets risker.

Vi rekommenderar att du distribuerar Cloud Discovery för att identifiera ohanterade moln program och hantera dessa program med hjälp av Azure Active Directory.

Mer information finns i [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Säkerhetsaviseringar från Privileged Identity Management

Denna sårbarhet hjälper dig att identifiera och lösa aviseringar om privilegierade identiteter i din organisation.  

För att göra det möjligt för användare att utföra privilegierade åtgärder, behöver organisationer bevilja användare temporär eller permanent privilegie rad åtkomst i Azure AD-, Azure-eller Office 365-resurser eller andra SaaS-appar. Var och en av dessa privilegierade användare ökar angrepps ytan i din organisation. Med den här säkerhets risken kan du identifiera användare med onödig privilegie rad åtkomst och vidta lämpliga åtgärder för att minska eller eliminera risken de utgör.

Vi rekommenderar organisationer att använda Azure AD Privileged Identity Management för att hantera, kontrol lera och övervaka privilegierade identiteter i Azure AD samt andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune.

Mer information finns i [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Se också

[Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
