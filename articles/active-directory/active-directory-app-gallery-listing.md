<properties
   pageTitle="Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten"
   description="Auflisten einer Anwendung, die einmaliges Anmelden unterstützt, im Azure Active Directory-Katalog | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/29/2015"
   ms.author="mbaldwin"/>


# Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten

Um eine Anwendung aufzulisten, die einmaliges Anmelden mit Azure Active Directory unterstützt die [Azure AD-Katalog](http://azure.microsoft.com/marketplace/active-directory/all/), muss die Anwendung zunächst einen der folgenden Modi Integration zu implementieren:

* **OpenID Connect** -die direkte Integration in Azure AD für Authentifizierung und Azure AD-API-Konfiguration zustimmen mit OpenID Connect. Wenn Sie gerade erst mit der Integration beginnen und Ihre Anwendung SAML nicht unterstützt, ist dies der empfohlene Modus.

* **SAML** – die Anwendung bereits verfügt, können Drittanbieter-Identitätsanbieter mithilfe des SAML-Protokolls konfiguriert.

Für die Auflistung bestehen je nach Modus folgende Anforderungen:

##Integration mithilfe von OpenID Connect

Zur Integration Ihrer Anwendung in Azure AD, die nach dem [Developer Anweisungen](active-directory-authentication-scenarios.md). Schließen Sie die folgenden Fragen und an waadpartners@microsoft.com senden.

* Stellen Sie Anmeldeinformationen für einen Testmandanten oder ein Testkonto mit Ihrer Anwendung bereit, die vom Azure AD-Team zum Testen der Integration verwendet werden können.  

* Erhalten Sie Anweisungen wie die Azure AD-Team kann sich anmelden und Verbinden einer Instanz von Azure AD mit Ihrer Anwendung mithilfe der [Azure AD Consent Frameworks](https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/#overview-of-the-consent-framework). 

* Stellen Sie alle weiteren notwendigen Anweisungen bereit, damit das Azure AD-Team das einmalige Anmelden mit Ihrer Anwendung testen kann. 

* Stellen Sie folgende Informationen bereit:

> Name des Unternehmens:
> 
> Website des Unternehmens:
> 
> Anwendungsname:
> 
> Anwendungsbeschreibung (max. 256 Zeichen):
> 
> Website der Anwendung (informativ):
> 
> Technische Support-Website für die Anwendung oder Kontaktinformationen:
> 
> Client-ID der Anwendung, wie in den Anwendungsdetails auf https://manage.windowsazure.com gezeigt:
> 
> Anmelde-URL, unter der sich die Kunden anmelden und/oder die Anwendung erwerben:
> 
> Wählen Sie bis zu drei Kategorien aus, unter denen Ihre Anwendung aufgelistet wird (verfügbare Kategorien finden Sie im Azure Active Directory Marketplace):
> 
> Kleines Anwendungssymbol einfügen (PNG-Datei, 45 px mal 45 px,  Volltonfarbe für den Hintergrund):
> 
> Großes Anwendungssymbol einfügen (PNG-Datei, 215 px mal 215 px,  Volltonfarbe für den Hintergrund):
> 
> Logo einfügen (PNG-Datei, 150 px mal 122 px, transparente Hintergrundfarbe):

##Integration mithilfe von SAML

Jede Anwendung, die SAML 2.0 unterstützt direkt in Azure AD-Mandant mit integriert werden kann [diesen Anweisungen zum Hinzufügen einer benutzerdefinierten Anwendung](active-directory-saas-custom-apps.md). Nachdem Sie getestet haben, dass Ihre Anwendungsintegration mit Azure AD funktioniert, senden Sie die folgende Informationen, um <waadpartners@microsoft.com>.

* Stellen Sie Anmeldeinformationen für einen Testmandanten oder ein Testkonto mit Ihrer Anwendung bereit, die vom Azure AD-Team zum Testen der Integration verwendet werden können.  

* SAML Anmelde-URL, Aussteller-URL (Entitäts-ID) und Antwort-URL (Assertion Consumer Service)-Werte für Ihre Anwendung bereitstellen, wie beschrieben [hier](active-directory-saas-custom-apps.md). Wenn Sie diese Werte in der Regel als Teil einer SAML-Metadatendatei zur Verfügung stellen, senden Sie uns diese ebenfalls.

* Beschreiben Sie kurz, wie Azure AD als Identitätsanbieter in ihrer Anwendung mithilfe von SAML 2.0 konfiguriert wird. Wenn Ihre Anwendung die Konfiguration von Azure AD als Identitätsanbieter über ein Self-Service-Verwaltungsportal unterstützt, stellen Sie sicher, dass dies mit den oben angegebenen Anmeldeinformation eingerichtet werden kann.

* Stellen Sie folgende Informationen bereit:

> Name des Unternehmens:
> 
> Website des Unternehmens:
> 
> Anwendungsname:
> 
> Anwendungsbeschreibung (max. 256 Zeichen):
> 
> Website der Anwendung (informativ):
> 
> Technische Support-Website für die Anwendung oder Kontaktinformationen:
> 
> Anmelde-URL, unter der sich die Kunden anmelden und/oder die Anwendung erwerben:
> 
> Wählen Sie bis zu drei Kategorien für Ihre Anwendung unter aufgelistet werden (Verfügbare Kategorien finden Sie die [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Kleines Anwendungssymbol einfügen (PNG-Datei, 45 px mal 45 px,  Volltonfarbe für den Hintergrund):
> 
> Großes Anwendungssymbol einfügen (PNG-Datei, 215 px mal 215 px,  Volltonfarbe für den Hintergrund):
> 
> Logo einfügen (PNG-Datei, 150 px mal 122 px, transparente Hintergrundfarbe):


