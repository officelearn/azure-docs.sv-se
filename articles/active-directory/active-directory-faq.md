---
title: "Vanliga frågor och svar om Azure Active Directory | Microsoft Docs"
description: "I det här avsnittet med vanliga frågor och svar om Azure Active Directory får du svar på frågor om hur du får åtkomst till Azure och Azure Active Directory, om lösenordshantering samt om åtkomsten till program."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/08/2017
ms.author: markvi
ms.openlocfilehash: 5ec11f1a125cc4c6e7f5165091411dea7daca193
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-faq"></a>Vanliga frågor och svar om Azure Active Directory
Azure Active Directory (Azure AD) är en omfattande IDaaS-lösning (Identity as a Service) som omfattar alla aspekter relaterade till identiteter, åtkomsthantering och säkerhet.

Mer information finns i [Vad är Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Kom åt Azure och Azure Active Directory
**F: Varför visas ett meddelande om att inga prenumerationer hittades när jag försöker komma åt Azure AD på den klassiska Azure-portalen?**

**S:** För att komma åt den klassiska Azure-portalen behöver varje användare ha behörighet med en Azure-prenumeration. Om du har en betald Office 365- eller Azure AD-prenumeration går du till [http://aka.ms/accessAAD](http://aka.ms/accessAAD) och följer steget för engångsaktiveringen. Annars måste du aktivera ett kostnadsfritt [Azure-konto](https://azure.microsoft.com/pricing/free-trial/) eller en betald prenumeration.

Mer information finns i:

* [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera katalogen för din Office 365-prenumeration i Azure](active-directory-manage-o365-subscription.md)

- - -
**F: Hur är Azure AD, Office 365 och Azure kopplade till varandra?**

**S:** Azure AD tillhandahåller gemensamma identitets- och åtkomstfunktioner för alla webbtjänster. Oavsett om du använder Office 365, Microsoft Azure, Intune eller andra tjänster använder du redan Azure AD för hjälp med inloggnings- och åtkomsthantering för alla dessa tjänster.

Alla användare som har ställts in för att använda webbtjänster definieras som användarkonton i en eller flera Azure AD-instanser. Du kan ställa in dessa konton för kostnadsfria Azure AD-funktioner, till exempel programåtkomst i molnet.

Azure AD-betaltjänsterna som Enterprise Mobility + Security kompletterar andra webbtjänster som Office 365 och Microsoft Azure med heltäckande hanterings- och säkerhetslösningar i företagsklass.
- - -
**F: Varför kan jag logga in i Azure Portal, men inte den klassiska Azure-portalen?**

**S:** Azure Portal kräver ingen giltig prenumeration, men den klassiska portalen kräver att du har en giltig prenumeration.  Om du inte har någon prenumeration kan du inte logga in på den klassiska portalen.
- - -
**F: Vad är skillnaden mellan prenumerationsadministratör och katalogadministratör?**

**S:** Som standard tilldelas du rollen som prenumerationsadministratör när du registrerar dig för Azure. En prenumerationsadministratör kan använda antingen ett Microsoft-konto eller ett arbets- eller skolkonto från den katalog som Azure-prenumerationen är associerad med.  Den här rollen har behörighet att hantera tjänster på Azure-portalen.

Om andra behöver logga in och komma åt tjänster med samma prenumeration kan du lägga till dem som medadministratörer. Den här rollen har samma åtkomstbehörigheter som tjänstadministratören, men kan inte ändra associationen mellan prenumerationer och Azure-kataloger.  Ytterligare information om prenumerationsadministratörer finns i [How to add or change Azure administrator roles](../billing-add-change-azure-subscription-administrator.md) (Så här lägger du till eller ändrar Azure-administratörsroller) och [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).


Azure AD har en annan uppsättning administratörsroller för att hantera katalog- och identitetsrelaterade funktioner.  De här administratörerna har åtkomst till olika funktioner i Azure Portal eller den klassiska Azure-portalen. Administratörens roll avgör vad de kan göra, som att skapa eller redigera användare, tilldela administrativa roller till andra, återställa användarlösenord, hantera användarlicenser eller hantera domäner.  Mer information om Azure AD-katalogadministratörer och deras roller finns i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Dessutom kompletterar Azure AD-betaltjänster som Enterprise Mobility + Security andra webbtjänster, som Office 365 och Microsoft Azure, med heltäckande hanterings- och säkerhetslösningar i företagsklass.

- - -
**F: Finns det någon rapport som visar när mina Azure AD-användarlicenser upphör att gälla?**

**S:** Nej.  Det här är inte tillgängligt för närvarande.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Kom igång med en Azure AD-hybridlösning


**F: Hur lämnar jag en klient när jag har lagts till som medarbetare?**

**A:** När du har lagts till som medarbetare till en annan organisations klient kan du använda "klientväxlaren" i det övre högra hörnet för att växla mellan klienter.  För närvarande går det inte att lämna organisationen som bjuder in och Microsoft arbetar med att tillhandahålla den här funktionen.  Tills den funktionen är tillgänglig kan du be organisationen som bjuder in att ta bort dig från deras klient.
- - -
**F: Hur kan jag ansluta min lokala katalog till Azure AD?**

**S:** Du kan ansluta din lokala katalog till Azure AD med hjälp av Azure AD Connect.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

- - -
**F: Hur konfigurerar jag enkel inloggning (SSO) mellan min lokala katalog och mina molnprogram?**

**S:** Du behöver bara konfigurera enkel inloggning (SSO) mellan din lokala katalog och AD Azure. Så länge du kommer åt dina molnprogram via Azure AD ser tjänsten till att användarna autentiseras korrekt med deras lokala autentiseringsuppgifter.

Du kan enkelt implementera enkel inloggning (SSO) från det lokala systemet med federationslösningar som Active Directory Federation Services (AD FS) eller genom att konfigurera hash-synkronisering av lösenord. Du kan enkelt distribuera båda alternativen med hjälp av Azure AD Connect-konfigurationsguiden.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

- - -
**F: Tillhandahåller Azure AD en självbetjäningsportal för användare i organisationen?**

**S:** Ja, Azure AD ger tillgång till [Azure AD-åtkomstpanelen](http://myapps.microsoft.com) för självbetjäning och programåtkomst. Om du är en Office 365-kund kan du hitta många av dessa funktioner på Office 365-portalen.

Mer information finns i [Introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

- - -
**F: Kan Azure AD hjälpa mig att hantera min lokala infrastruktur?**

**S:** Ja. Azure AD Premium-versionen tillhandahåller Azure AD Connect Health. Azure AD Connect Health hjälper dig att övervaka och få insyn i den lokala identitetsinfrastrukturen och synkroniseringstjänsterna.  

Mer information finns i [Övervaka den lokala identitetsinfrastrukturen och synkroniseringstjänster i molnet](active-directory-aadconnect-health.md).  

- - -
## <a name="password-management"></a>Lösenordshantering
**F: Kan jag använda tillbakaskrivning av lösenord i Azure AD utan lösenordssynkronisering? (Är det i det här scenariot möjligt att använda lösenordsåterställning via självbetjäning (SSPR) för Azure AD med tillbakaskrivning av lösenord och inte lagra lösenord i molnet?)**

**S:** Du behöver inte synkronisera dina Active Directory-lösenord till Azure AD för att använda tillbakaskrivning. I en federerad miljö använder enkel inloggning (SSO) i Azure AD den lokala katalogen för att autentisera användaren. I det här scenariot måste inte det lokala lösenordet spåras i Azure AD.

- - -
**F: Hur lång tid tar det för ett lösenord att skrivas tillbaka till Active Directory lokalt?**

**S:** Tillbakaskrivningen av lösenord fungerar i realtid.

Mer information finns i [Komma igång med lösenordshantering](active-directory-passwords-getting-started.md).

- - -
**F: Kan jag använda tillbakaskrivning av lösenord med lösenord som hanteras av en administratör?**

**S:** Ja, om du har aktiverat tillbakaskrivning av lösenord skrivs lösenordsåtgärder som utförs av en administratör tillbaka till din lokala miljö.  

Fler svar på lösenordsrelaterade frågor finns i [Vanliga frågor och svar om lösenordshantering](active-directory-passwords-faq.md).
- - -
**F: Vad gör jag om jag inte kommer ihåg mitt befintliga Office 365-/Azure AD-lösenord när jag försöker ändra mitt lösenord?**

**S:** För den här typen av situation finns det ett par alternativ.  Använd självbetjäning för återställning av lösenord (SSPR) om det är tillgängligt.  Huruvida SSPR fungerar eller ej beror på hur det är konfigurerat.  Mer information finns i avsnittet som beskriver [Hur portalen för lösenordsåterställning fungerar](active-directory-passwords-best-practices.md).

För Office 365-användare kan administratören återställa lösenordet med hjälp av stegen som beskrivs i [Återställa användares lösenord](https://support.office.com/en-us/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

För Azure AD-konton kan administratörer återställa lösenord med någon av följande metoder:

- [Återställa konton på Azure-portalen](active-directory-users-reset-password-azure-portal.md)
- [Använda PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Säkerhet
**Fråga: Låses konton efter ett visst antal misslyckade försök eller finns det en mer avancerad strategi?**</br>
Vi använder en mer avancerad strategi för att låsa konton.  Den är baserad på IP-adressen för begäran och det lösenord som anges. Låsningens varaktighet ökar också beroende på sannolikheten för att det rör sig om ett angrepp.  

**Fråga: Vissa (vanliga) lösenord avvisas med meddelandet ”lösenordet har använts för många gånger”. Gäller detta lösenord som används i aktuell Active Directory?**</br>
Det här gäller lösenord som är allmänt vanliga, till exempel varianter av "Lösenord" och "123456".

**Fråga: Blockeras inloggningsbegäranden från misstänkta källor (botnät, Tor-slutpunkt) på en B2C-klient eller kräver detta att klienten har en Basic- eller Premium-utgåva?**</br>
Vi har en gateway som filtrerar begäranden och som ger ett visst skydd mot botnät. Den används för alla B2C-klienter.

## <a name="application-access"></a>Programåtkomst
**F: Var kan jag hitta en lista över program som redan är integrerade i Azure AD och deras funktioner?**

**S:** Azure AD har mer än 2 600 redan integrerade program från Microsoft, programtjänstleverantörer och partner. Alla redan integrerade program stöder enkel inloggning (SSO). Med enkel inloggning kan du använda din organisations autentiseringsuppgifter för att komma åt dina appar. Vissa program stöder även automatisk etablering och avetablering.

En fullständig lista över redan integrerade program finns på [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**F: Vad gör jag om jag inte hittar det program som jag behöver på Azure AD-Marketplace?**

**S:** Med Azure AD Premium kan du lägga till och konfigurera de program du vill. Beroende på programmets funktioner och dina preferenser kan du konfigurera enkel inloggning (SSO) och automatisk etablering.  

Mer information finns i:

* [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md)

- - -
**F: Hur loggar användarna in i program med Azure AD?**

**S:** Med Azure AD kan användarna visa och komma åt sina program på flera olika sätt, t.ex.:

* Azure AD-åtkomstpanelen
* Startprogrammet för Office 365
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Mer information finns i [Distribuera Azure AD-integrerade program till användare](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**F: På vilka sätt stöder Azure AD autentisering och enkel inloggning (SSO) i program?**

**S:** Azure AD har stöd för många standardiserade protokoll för autentisering och auktorisering, till exempel SAML 2.0, OpenID Connect, OAuth 2.0 och WS-Federation. Azure AD stöder också lösenordsvalv och automatisk inloggning för appar som endast har stöd för formulärbaserad autentisering.  

Mer information finns i:

* [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md)
* [Active Directory-autentiseringsprotokoll](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Hur fungerar enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**F: Kan jag lägga till program som jag kör lokalt?**

**S:** Azure AD Application Proxy ger enkel och säker åtkomst till lokala webbprogram som du väljer. Du kan komma åt dessa program på samma sätt som du kommer åt programvara som en tjänst (SaaS)-appar i Azure AD. Du behöver ingen VPN-anslutning och du behöver inte ändra din nätverksinfrastruktur.  

Mer information finns i [Ge säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md).

- - -
**F: Hur kräver jag Multi-Factor Authentication för användare som kommer åt ett visst program?**

**S:** Med villkorlig åtkomst i Azure AD kan du tilldela en unik åtkomstprincip för varje program. Du kan välja att alltid kräva Multi-Factor Authentication eller bara kräva det när användarna inte är anslutna till det lokala nätverket.  

Mer information finns i [Skydda åtkomsten till Office 365 och andra appar som är anslutna till Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- - -
**F: Vad är automatisk användaretablering för SaaS-appar?**

**S:** Med Azure AD kan du automatisera genereringen, underhållet och borttagningen av användaridentiteter i många populära SaaS-appar i molnet.

Mer information finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](active-directory-saas-app-provisioning.md).

- - -
**F: Kan jag skapa en säker LDAP-anslutning med Azure AD?**

**S:** Nej. Azure AD stöder inte LDAP-protokollet.
