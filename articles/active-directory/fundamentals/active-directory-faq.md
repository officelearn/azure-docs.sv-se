---
title: Vanliga frågor (FAQ) – Azure Active Directory | Microsoft Docs
description: Vanliga frågor och svar om Azure och Azure Active Directory, lösenordshantering och programåtkomst.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f602b5f9809e5bbe0b1efb52cfa66877a31a2170
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249554"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Vanliga frågor och svar om Azure Active Directory
Azure Active Directory (Azure AD) är en omfattande IDaaS-lösning (Identity as a Service) som omfattar alla aspekter relaterade till identiteter, åtkomsthantering och säkerhet.

Mer information finns i [Vad är Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Kom åt Azure och Azure Active Directory
**F: Varför får jag ”inga prenumerationer hittades” när jag försöker komma åt Azure AD i Azure-portalen?**

**S:** Om du vill komma åt Azure-portalen måste ha varje användare behörighet med en Azure-prenumeration. Om du har en betald Office 365- eller Azure AD-prenumeration går du till [https://aka.ms/accessAAD](https://aka.ms/accessAAD) för att hitta stegen för engångsaktiveringen. Annars måste du aktivera ett kostnadsfritt [Azure-konto](https://azure.microsoft.com/pricing/free-trial/) eller en betald prenumeration.

Mer information finns i:

* [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- - -
**F: Vad är relationen mellan Azure AD, Office 365 och Azure?**

**S:** Azure AD tillhandahåller gemensamma identitets- och åtkomstfunktioner för alla webbtjänster. Oavsett om du använder Office 365, Microsoft Azure, Intune eller andra tjänster använder du redan Azure AD för hjälp med inloggnings- och åtkomsthantering för alla dessa tjänster.

Alla användare som har ställts in för att använda webbtjänster definieras som användarkonton i en eller flera Azure AD-instanser. Du kan ställa in dessa konton för kostnadsfria Azure AD-funktioner, till exempel programåtkomst i molnet.

Azure AD-betaltjänsterna som Enterprise Mobility + Security kompletterar andra webbtjänster som Office 365 och Microsoft Azure med heltäckande hanterings- och säkerhetslösningar i företagsklass.

- - -

**F:  Vilka är skillnaderna mellan ägare och Global administratör?**

**S:** Som standard tilldelas den person som registrerar sig för en Azure-prenumeration ägarrollen för Azure-resurser. Ägare kan använda antingen ett Microsoft-konto eller ett arbets- eller skolkonto från den katalog som Azure-prenumerationen är associerad med.  Den här rollen har behörighet att hantera tjänster på Azure-portalen.

Om andra behöver logga in och komma åt tjänster med hjälp av samma prenumeration kan du tilldela dem lämplig [inbyggd roll](../../role-based-access-control/built-in-roles.md). Mer information finns i [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md).

Som standard tilldelas den person som registrerar sig för en Azure-prenumeration rollen som Global administratör för katalogen. Globala administratörer har åtkomst till alla funktioner i Azure AD-katalog. Azure AD har en annan uppsättning administratörsroller för att hantera katalog- och identitetsrelaterade funktioner. Dessa administratörer har åtkomst till olika funktioner i Azure-portalen. Administratörens roll avgör vad de kan göra, som att skapa eller redigera användare, tilldela administrativa roller till andra, återställa användarlösenord, hantera användarlicenser eller hantera domäner.  Mer information om Azure AD-katalogadministratörer och deras roller finns i [tilldela en användare till administratörsroller i Azure Active Directory](active-directory-users-assign-role-azure-portal.md) och [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Dessutom kompletterar Azure AD-betaltjänster som Enterprise Mobility + Security andra webbtjänster, som Office 365 och Microsoft Azure, med heltäckande hanterings- och säkerhetslösningar i företagsklass.

- - -
**F: Finns det en rapport som visar när Mina Azure AD-användarlicenser upphör att gälla?**

**S:** Nej.  Det här är inte tillgängligt för närvarande.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Kom igång med en Azure AD-hybridlösning


**F: Hur lämnar jag en klient när jag har lagts till som medarbetare?**

**S:** När du har lagts till en annan organisations klient som medarbetare kan använda du ”klientväxlaren” i det övre högra hörnet för att växla mellan klienter.  För närvarande går det inte att lämna organisationen som bjuder in och Microsoft arbetar med att tillhandahålla den här funktionen.  Tills den funktionen är tillgänglig kan du be organisationen som bjuder in att ta bort dig från deras klient.
- - -
**F: Hur kan jag ansluta min lokala katalog till Azure AD?**

**S:** Du kan ansluta din lokala katalog till Azure AD med hjälp av Azure AD Connect.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

- - -
**F: Hur konfigurerar jag enkel inloggning mellan min lokala katalog och Mina molnprogram?**

**S:** Du behöver bara konfigurera enkel inloggning (SSO) mellan din lokala katalog och Azure AD. Så länge du kommer åt dina molnprogram via Azure AD ser tjänsten till att användarna autentiseras korrekt med deras lokala autentiseringsuppgifter.

Du kan enkelt implementera enkel inloggning (SSO) från det lokala systemet med federationslösningar som Active Directory Federation Services (AD FS) eller genom att konfigurera hash-synkronisering av lösenord. Du kan enkelt distribuera båda alternativen med hjälp av Azure AD Connect-konfigurationsguiden.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

- - -
**F: Tillhandahåller Azure AD en självbetjäningsportal för användare i min organisation?**

**S:** Ja, Azure AD tillhandahåller du med den [Azure AD-åtkomstpanelen](https://myapps.microsoft.com) för självbetjäning och programåtkomst. Om du är Office 365-kund kan du hitta många av dessa funktioner i den [Office 365-portalen](https://portal.office.com).

Mer information finns i [Introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

- - -
**F: Kan Azure AD hjälpa mig att hantera min lokala infrastruktur?**

**S:** Ja. Azure AD Premium-versionen tillhandahåller Azure AD Connect Health. Azure AD Connect Health hjälper dig att övervaka och få insyn i den lokala identitetsinfrastrukturen och synkroniseringstjänsterna.  

Mer information finns i [Övervaka den lokala identitetsinfrastrukturen och synkroniseringstjänster i molnet](../hybrid/whatis-hybrid-identity-health.md).  

- - -
## <a name="password-management"></a>Lösenordshantering
**F: Kan jag använda Azure AD tillbakaskrivningen av lösenord utan Lösenordssynkronisering? (Är det i det här scenariot möjligt att använda lösenordsåterställning via självbetjäning (SSPR) för Azure AD med tillbakaskrivning av lösenord och inte lagra lösenord i molnet?)**

**S:** Du behöver inte synkronisera dina Active Directory-lösenord till Azure AD för att använda tillbakaskrivning. I en federerad miljö använder enkel inloggning (SSO) i Azure AD den lokala katalogen för att autentisera användaren. I det här scenariot måste inte det lokala lösenordet spåras i Azure AD.

- - -
**F: Hur lång tid tar det för ett lösenord att skrivas tillbaka till Active Directory lokalt?**

**S:** Tillbakaskrivning av lösenord fungerar i realtid.

Mer information finns i [Komma igång med lösenordshantering](../authentication/quickstart-sspr.md).

- - -
**F: Kan jag använda tillbakaskrivning av lösenord med lösenord som hanteras av en administratör?**

**S:** Ja, om du har aktiverat tillbakaskrivning av lösenord, de lösenordsåtgärder som utförs av en administratör skrivs tillbaka till din lokala miljö.  

Fler svar på lösenordsrelaterade frågor finns i [Vanliga frågor och svar om lösenordshantering](../authentication/active-directory-passwords-faq.md).
- - -
**F:  Vad kan jag göra om jag inte kommer ihåg mitt befintliga Office 365-/ Azure AD-lösenord när du försöker ändra mitt lösenord?**

**S:** Det finns ett par alternativ för den här typen av situation.  Använd självbetjäning för återställning av lösenord (SSPR) om det är tillgängligt.  Huruvida SSPR fungerar eller ej beror på hur det är konfigurerat.  Mer information finns i avsnittet som beskriver [Hur portalen för lösenordsåterställning fungerar](../authentication/howto-sspr-deployment.md).

För Office 365-användare kan administratören återställa lösenordet med hjälp av stegen som beskrivs i [Återställa användares lösenord](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

För Azure AD-konton kan administratörer återställa lösenord med någon av följande metoder:

- [Återställa konton på Azure-portalen](active-directory-users-reset-password-azure-portal.md)
- [Använda PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Säkerhet
**F: Låses konton efter ett visst antal misslyckade försök eller finns det en mer avancerad strategi?**

Vi använder en mer avancerad strategi för att låsa konton.  Den är baserad på IP-adressen för begäran och det lösenord som anges. Låsningens varaktighet ökar också beroende på sannolikheten för att det rör sig om ett angrepp.  

**F:  Vissa (vanliga) lösenord avvisas med meddelandet ”det här lösenordet har använts för många gånger”, gäller detta lösenord som används i aktuell active directory?**

Det här gäller lösenord som är allmänt vanliga, till exempel varianter av "Lösenord" och "123456".

**F: En begäran från misstänkta källor (botnät, tor-slutpunkt) blockeras i en B2C-klient eller kräver detta att en Basic- eller Premium edition-klient?**

Vi har en gateway som filtrerar begäranden och som ger ett visst skydd mot botnät. Den används för alla B2C-klienter.

## <a name="application-access"></a>Programåtkomst

**F: Var hittar jag en lista över program som redan är integrerade med Azure AD och deras funktioner?**

**S:** Azure AD har mer än 2 600 redan integrerade program från Microsoft, programtjänstleverantörer och partner. Alla redan integrerade program stöder enkel inloggning (SSO). Med enkel inloggning kan du använda din organisations autentiseringsuppgifter för att komma åt dina appar. Vissa program stöder även automatisk etablering och avetablering.

En fullständig lista över redan integrerade program finns på [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**F: Vad händer om är det program som jag behöver inte i Azure AD-marketplace?**

**S:** Med Azure AD Premium kan du lägga till och konfigurera de program som du vill. Beroende på programmets funktioner och dina preferenser kan du konfigurera enkel inloggning (SSO) och automatisk etablering.  

Mer information finns i:

* [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](../manage-apps/use-scim-to-provision-users-and-groups.md)

- - -
**F: Hur användare loggar in till program med hjälp av Azure AD?**

**S:** Azure AD kan användarna visa och komma åt sina program, till exempel flera olika sätt:

* Azure AD-åtkomstpanelen
* Startprogrammet för Office 365
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Mer information finns i [slutanvändaren upplevelser för program](../manage-apps/end-user-experiences.md).

- - -
**F: Vilka är de olika sätten Azure AD stöder autentisering och enkel inloggning till program?**

**S:** Azure AD stöder många standardiserade protokoll för autentisering och auktorisering, till exempel SAML 2.0, OpenID Connect, OAuth 2.0 och WS-Federation. Azure AD stöder också lösenordsvalv och automatisk inloggning för appar som endast har stöd för formulärbaserad autentisering.  

Mer information finns i:

* [Autentiseringsscenarier för Azure AD](../develop/authentication-scenarios.md)
* [Active Directory-autentiseringsprotokoll](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Enkel inloggning för program i Azure AD](../manage-apps/what-is-single-sign-on.md)

- - -
**F: Kan jag lägga till program som jag kör lokalt?**

**S:** Azure AD Application Proxy ger enkel och säker åtkomst till lokala webbprogram som du väljer. Du kan komma åt dessa program på samma sätt som du kommer åt programvara som en tjänst (SaaS)-appar i Azure AD. Du behöver ingen VPN-anslutning och du behöver inte ändra din nätverksinfrastruktur.  

Mer information finns i [Ge säker fjärråtkomst till lokala program](../manage-apps/application-proxy.md).

- - -
**F: Hur kräver jag Multi-Factor authentication för användare som har åtkomst till ett visst program?**

**S:** Med Azure AD villkorlig åtkomst, kan du tilldela en unik åtkomstprincip för varje program. Du kan välja att alltid kräva Multi-Factor Authentication eller bara kräva det när användarna inte är anslutna till det lokala nätverket.  

Mer information finns i [Skydda åtkomsten till Office 365 och andra appar som är anslutna till Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

- - -
**F: Vad är automatisk användaretablering för SaaS-appar?**

**S:** Använd Azure AD för att automatisera den genereringen, underhållet och borttagningen av användaridentiteter i många populära SaaS molnappar.

Mer information finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

- - -
**F:  Kan jag konfigurera en säker LDAP-anslutning med Azure AD?**

**S:**  Nej. Azure AD stöder inte LDAP-protokollet. Du kan dock konfigurera säkert LDAP med Azure AD Domain Services.
