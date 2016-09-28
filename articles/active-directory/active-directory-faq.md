<properties
    pageTitle="Vanliga frågor och svar om Azure Active Directory | Microsoft Azure"
    description="I det här avsnittet med vanliga frågor och svar om Azure Active Directory får du svar på frågor om åtkomsten till Azure och Azure Active Directory, om lösenordshantering samt om åtkomsten till program."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>


# Vanliga frågor och svar om Azure Active Directory

Azure Active Directory är en omfattande IDaaS-lösning (Identity as a Service) som omfattar alla aspekter relaterade till identiteter, åtkomsthantering och säkerhet.


Mer information finns i [Vad är Azure Active Directory?](active-directory-whatis.md).



## Komma åt Azure och Azure Active Directory


**F: Varför visas ett meddelande om att inga prenumerationer hittades när jag försöker komma åt Azure AD på den klassiska Azure-portalen (https://manage.windowsazure.com)?**

**S:** För att komma åt den klassiska Azure-portalen måste varje användare ha behörighet för en Azure-prenumeration. Om du har en betald Office 365- eller Azure AD-prenumeration går du till  [http://aka.ms/accessAAD](http://aka.ms/accessAAD) och följer steget för engångsaktiveringen. Annars måste du aktivera en fullständig [utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/) eller en betald prenumeration. 

Mer information finns i:

- [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Hantera katalogen för din Office 365-prenumeration i Azure](active-directory-manage-o365-subscription.md)

---

**F: Hur är Azure AD, Office 365 och Azure kopplade till varandra?**

**S:** Azure Active Directory tillhandahåller gemensamma identitets- och åtkomstfunktioner för alla Microsoft-onlinetjänster. Om du använder Office 365, Microsoft Azure, Intune eller andra tjänster använder du redan Azure AD för inloggnings- och åtkomsthantering för alla dessa tjänster. 

Alla användare som du har aktiverat för Microsofts onlinetjänster definieras i själva verket som användarkonton i en eller flera Azure AD-instanser. Du kan aktivera dessa konton för kostnadsfria Azure AD-funktioner, till exempel programåtkomst i molnet.
 
Dessutom kompletterar Azure AD-betaltjänsterna (t.ex. Azure AD Basic, Premium, EMS osv.) andra onlinetjänster som Office 365 och Microsoft Azure med heltäckande hanterings- och säkerhetslösningar i företagsklass.


---



## Komma igång med en Azure AD-hybridlösning


**F: Hur kan jag ansluta min lokala katalog till Azure AD?**

**S:** Du kan ansluta din lokala katalog till Azure AD med hjälp av **Azure AD Connect**. 

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).


---

**F: Hur konfigurerar jag enkel inloggning (SSO) mellan min lokala katalog och mina molnprogram?**

**S:** Du behöver bara konfigurera enkel inloggning mellan din lokala katalog och AD Azure. Så länge du kommer åt dina molnprogram via Azure AD ser tjänsten till att användarna autentiseras korrekt med deras lokala autentiseringsuppgifter.

Du kan enkelt implementera enkel inloggning (SSO) från det lokala systemet med federationslösningar som AD FS eller genom att konfigurera hash-synkronisering av lösenord. Du kan enkelt distribuera båda alternativen med hjälp av Azure AD Connect-konfigurationsguiden.
  

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
  

---

**F: Tillhandahåller Azure Active Directory en självbetjäningsportal för användare i organisationen?**

**S:** Ja, Azure Active Directory ger tillgång till [Azure AD-åtkomstpanelen](http://myapps.microsoft.com) för självbetjäning och programåtkomst. Om du är en Office 365-kund kan du hitta många av dessa funktioner på Office 365-portalen. 

Mer information finns i [Introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 



---

**F: Kan Azure AD hjälpa mig att hantera min lokala infrastruktur?**

**S:** Ja. Azure AD Premium-versionen tillhandahåller **Connect Health**. Azure AD Connect Health hjälper dig att övervaka och få insyn i den lokala identitetsinfrastrukturen och synkroniseringstjänsterna.  

Mer information finns i [Övervaka den lokala identitetsinfrastrukturen och synkroniseringstjänster i molnet](active-directory-aadconnect-health.md).  

---

## Lösenordshantering

**F: Kan jag använda tillbakaskrivning av lösenord i Azure AD utan lösenordssynkronisering? (Jag skulle vilja använda Azure AD SSPR med tillbakaskrivning av lösenord men jag vill inte att mina lösenord ska lagras i molnet.)**

**S:** Du behöver inte synkronisera dina AD-lösenord till Azure AD för att använda tillbakaskrivning. I en federerad miljö använder SSO (enkel inloggning) i Azure AD den lokala katalogen för att autentisera användaren. I det här scenariot måste inte det lokala lösenordet spåras i Azure AD.

---

**F: Hur lång tid tar det för ett lösenord att skrivas tillbaka till AD lokalt?**

**S:** Tillbakaskrivningen av lösenord fungerar i realtid. 

Mer information finns i [Komma igång med lösenordshantering](active-directory-passwords-getting-started.md) 


---

**F: Kan jag använda tillbakaskrivning av lösenord med lösenord som hanteras av en administratör?**

**S:** Ja, om du har aktiverat tillbakaskrivning av lösenord skrivs lösenordsåtgärder som utförs av en administratör tillbaka till din lokala miljö.  

Fler svar på lösenordsrelaterade frågor finns i [Vanliga frågor och svar om lösenordshantering](active-directory-passwords-faq.md).

---

## Programåtkomst


**F: Var kan jag hitta en lista över program som redan är integrerade i Azure AD och deras funktioner?**

**S:** Azure AD har över 2 600 redan integrerade program från Microsoft, programtjänstproviders och partner. Alla redan integrerade program stöder enkel inloggning (SSO). Med enkel inloggning kan du använda din organisations autentiseringsuppgifter för att komma åt dina appar. Vissa program stöder även automatisk etablering och avetablering

En fullständig lista över redan integrerade program finns på [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**F: Vad gör jag om jag inte hittar det program som jag behöver på Azure AD-Marketplace?**

**S:** Med Azure AD Premium kan du lägga till och konfigurera de program du vill. Beroende på programmets funktioner och dina preferenser kan du konfigurera enkel inloggning (SSO) och automatisk etablering.  

Mer information finns i:

- [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](active-directory-saas-custom-apps.md)
- [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md) 


---

**F: Hur loggar användarna in i program med Azure Active Directory?**
 
**S:** Med Azure Active Directory kan användarna visa och komma åt sina program på flera olika sätt, t.ex.:

- Azure AD-åtkomstpanelen

- Startprogrammet för Office 365

- Direkt inloggning till federerade appar

- Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Mer information finns i [Distribuera Azure AD-integrerade program till användare](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**F: På vilka sätt stöder Azure Active Directory autentisering och enkel inloggning (SSO) i program?**
 
**S:** Azure Active Directory har stöd för många standardiserade protokoll för autentisering och auktorisering, till exempel SAML 2.0, OpenID Connect, OAuth 2.0 och WS-Federation. Azure AD stöder också lösenordsvalv och automatisk inloggning för appar som endast har stöd för formulärbaserad autentisering.  

Mer information finns i:

- [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md)

- [Active Directory-autentiseringsprotokoll](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Hur fungerar enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**F: Kan jag lägga till program som jag kör lokalt?**

**S:** Azure AD Application Proxy ger enkel och säker åtkomst till lokala webbprogram som du väljer. Du kan komma åt dessa program på samma sätt som du kommer åt SaaS-appar i Azure Active Directory. Du behöver ingen VPN-anslutning och du behöver inte ändra din nätverksinfrastruktur.  

Mer information finns i [Ge säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md).


--- 

**F: Hur kräver jag MFA för användare som kommer åt ett visst program?**

**S:** Med villkorlig åtkomst i Azure AD kan du tilldela en unik åtkomstprincip för varje program. Du kan välja att alltid kräva MFA eller bara kräva det när användarna inte är anslutna till det lokala nätverket.  

Mer information finns i [Skydda åtkomsten till Office 365 och andra appar som är anslutna till Azure Active Directory](active-directory-conditional-access.md).


---

**F: Vad är automatisk användaretablering för SaaS-appar?**

**S:** Med Azure Active Directory kan du automatisera genereringen, underhållet och borttagningen av användaridentiteter i många populära molnprogram (SaaS). 

Mer information finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](active-directory-saas-app-provisioning.md)

---






<!--HONumber=Sep16_HO3-->


