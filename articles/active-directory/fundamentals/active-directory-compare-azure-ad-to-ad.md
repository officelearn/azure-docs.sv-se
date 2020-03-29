---
title: Jämföra Active Directory med Azure Active Directory
description: I det här dokumentet jämförs Active Directory Domain Services (ADD) med Azure Active Directory (AD). Den beskriver viktiga begrepp i både identitetslösningar och förklarar hur det är annorlunda eller liknande.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 5075ae57df6a7306f0c860690931c846e52c2a89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926887"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Jämföra Active Directory med Azure Active Directory

Azure Active Directory är nästa utveckling av identitets- och åtkomsthanteringslösningar för molnet. Microsoft introducerade Active Directory Domain Services i Windows 2000 för att ge organisationer möjlighet att hantera flera lokala infrastrukturkomponenter och system med en enda identitet per användare.

Azure AD tar den här metoden till nästa nivå genom att ge organisationer en IDaaS-lösning (Identity as a Service) för alla sina appar i molnet och lokalt.

De flesta IT-administratörer känner till Active Directory Domain Services-begreppen. I följande tabell beskrivs skillnaderna och likheterna mellan Active Directory-begrepp och Azure Active Directory.

|Begrepp|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**Användare**|||
|Etablering: användare | Organisationer skapar interna användare manuellt eller använder ett internt eller automatiserat etableringssystem, till exempel Microsoft Identity Manager, för att integreras med ett HR-system.|Befintliga AD-organisationer använder [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) för att synkronisera identiteter till molnet.</br> Azure AD lägger till stöd för att automatiskt skapa användare från [moln-HR-system](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial). </br>Azure AD kan etablera identiteter i [SCIM-aktiverade](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) SaaS-appar för att automatiskt ge appar den information som krävs för att tillåta åtkomst för användare. |
|Etablering: externa identiteter| Organisationer skapar externa användare manuellt som vanliga användare i en dedikerad extern AD-skog, vilket resulterar i administrationskostnader och andra för att hantera livscykeln för externa identiteter (gästanvändare)| Azure AD tillhandahåller en särskild identitetsklass för att stödja externa identiteter. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) hanterar länken till den externa användaridentiteten för att se till att de är giltiga. |
| Hantering och grupper för berättigande| Administratörer gör användare till medlemmar i grupper. App- och resursägare ger sedan grupper åtkomst till appar eller resurser.| [Grupper](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) är också tillgängliga i Azure AD och administratörer kan också använda grupper för att bevilja behörigheter till resurser. I Azure AD kan administratörer tilldela medlemskap i grupper manuellt eller använda en fråga för att dynamiskt inkludera användare till en grupp. </br> Administratörer kan använda [rättighetshantering](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) i Azure AD för att ge användarna åtkomst till en samling appar och resurser med hjälp av arbetsflöden och, om nödvändigt, tidsbaserade kriterier. |
| Administratörshantering|Organisationer använder en kombination av domäner, organisationsenheter och grupper i AD för att delegera administrativa rättigheter för att hantera katalogen och resurserna som den kontrollerar.| Azure AD ger [inbyggda roller](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) med sitt rollbaserade åtkomstkontrollsystem (RBAC), med begränsat stöd för att [skapa anpassade roller](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview) för att delegera privilegierad åtkomst till identitetssystemet, apparna och resurserna som styr.</br>Hantera roller kan förbättras med [Privilegierad identitetshantering (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) för att ge just-in-time, tidsbegränsad eller arbetsflödesbaserad åtkomst till privilegierade roller. |
| Hantering av autentiseringsuppgifter| Autentiseringsuppgifterna i Active Directory baseras på lösenord, certifikatautentisering och smartkortsautentisering. Lösenord hanteras med hjälp av lösenordsprinciper som baseras på lösenordslängd, förfallodatum och komplexitet.|Azure AD använder intelligent [lösenordsskydd](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) för molnet och lokalt. Skyddet omfattar smart utelåsning plus blockering av vanliga och anpassade lösenordsfraser och ersättningar. </br>Azure AD ökar avsevärt säkerheten [genom multifaktorautentisering](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) och [lösenordslös](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) teknik, till exempel FIDO2. </br>Azure AD minskar supportkostnaderna genom att ge användarna ett system [för återställning av lösenord med självbetjäning.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) |
| **Appar**|||
| Infrastrukturappar|Active Directory ligger till grund för många lokala infrastrukturkomponenter, till exempel DNS, DHCP, IPSec, WiFi, NPS och VPN-åtkomst|I en ny molnvärld, Azure AD, är det nya kontrollplanet för åtkomst till appar jämfört med att förlita sig på nätverkskontroller. När användare autentiserar kontrollerar[villkorlig åtkomst (CA)](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)vilka användare som ska ha åtkomst till vilka appar under erforderliga villkor.|
| Traditionella och äldre appar| De flesta lokala appar använder LDAP, Windows-integrerad autentisering (NTLM och Kerberos) eller Rubrikbaserad autentisering för att styra åtkomsten till användare.| Azure AD kan ge åtkomst till dessa typer av lokala appar med hjälp av [Azure AD-programproxyagenter](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) som körs lokalt. Med den här metoden kan Azure AD autentisera Active Directory-användare lokalt med Kerberos medan du migrerar eller behöver samexistera med äldre appar. |
| SaaS-appar|Active Directory stöder inte SaaS-appar internt och kräver federationssystem, till exempel AD FS.|SaaS-appar som stöder OAuth2-,\* SAML- och WS-autentisering kan integreras för att använda Azure AD för autentisering. |
| LOB-appar (Line of business) med modern autentisering|Organisationer kan använda AD FS med Active Directory för att stödja LOB-appar som kräver modern autentisering.| LOB-appar som kräver modern autentisering kan konfigureras för att använda Azure AD för autentisering. |
| Tjänster på mellannivå/daemon|Tjänster som körs i lokala miljöer använder normalt AD-tjänstkonton eller grupperade hanterade tjänstkonton (gMSA) för att köras. Dessa appar ärver sedan behörigheterna för tjänstkontot.| Azure AD tillhandahåller [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) för att köra andra arbetsbelastningar i molnet. Livscykeln för dessa identiteter hanteras av Azure AD och är knuten till resursleverantören kan inte användas för andra ändamål för att få bakdörrsåtkomst.|
| **Egenskaper**|||
| Mobil|Active Directory stöder inte mobila enheter utan lösningar från tredje part.| Microsofts lösning för hantering av mobila enheter, Microsoft Intune, är integrerad med Azure AD. Microsoft Intune tillhandahåller enhetstillståndsinformation till identitetssystemet för att utvärdera under autentiseringen. |
| Windows-datorer|Active Directory ger möjlighet att domän ansluta till Windows-enheter för att hantera dem med hjälp av Grupprincip, System Center Configuration Manager eller andra tredjepartslösningar.|Windows-enheter kan [anslutas till Azure AD](https://docs.microsoft.com/azure/active-directory/devices/). Villkorlig åtkomst kan kontrollera om en enhet är Azure AD-ansluten som en del av autentiseringsprocessen. Windows-enheter kan också hanteras med [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune). I det här fallet, villkorlig åtkomst, kommer att överväga om en enhet är klagomål (till exempel uppdaterade säkerhetskorrigeringar och virussignaturer) innan du tillåter åtkomst till apparna.|
| Windows-servrar| Active Directory ger starka hanteringsfunktioner för lokala Windows-servrar med grupprinciper eller andra hanteringslösningar.| Virtuella datorer för Windows-servrar i Azure kan hanteras med [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/). [Hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) kan användas när virtuella datorer behöver åtkomst till identitetssystemets katalog eller resurser.|
| Linux/Unix-arbetsbelastningar|Active Directory stöder inte inbyggt icke-Windows utan lösningar från tredje part, även om Linux-datorer kan konfigureras för att autentisera med Active Directory som Kerberos-sfär.|Virtuella linux/Unix-datorer kan använda [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) för att komma åt identitetssystemet eller resurser. Vissa organisationer migrerar dessa arbetsbelastningar till molnbehållaretekniker, som också kan använda hanterade identiteter.|

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [Jämföra självhanterade Active Directory Domain Services, Azure Active Directory och hanterade Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)
- [Vanliga frågor och svar om Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-faq)
- [Vad är nytt i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/whats-new)
