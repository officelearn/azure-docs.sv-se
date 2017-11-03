---
title: "Visar en lista över ditt program i Azure Active Directory-programgalleriet"
description: "Visa en lista över ett program som stöder enkel inloggning i Azure Active Directory-galleriet | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 1d315cf63bcbf37b6b03b5a965ac615282526682
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Visar en lista över ditt program i Azure Active Directory-programgalleriet
Att visa ett program som stöder enkel inloggning med Azure Active Directory i den [Azure AD-galleriet](https://azure.microsoft.com/marketplace/active-directory/all/), programmet först måste implementera ett av följande integration lägen:

* **OpenID Connect** -Direktintegrering med Azure AD med OpenID Connect för autentisering och Azure AD medgivande API för konfigurationen. Om du bara startar en integration och programmet har inte stöd för SAML är rekommenderar-läge.
* **SAML** – ditt program redan har möjlighet att konfigurera från tredje part identitetsleverantörer med SAML-protokoll.

Lista över kraven för varje läge är nedan.

## <a name="openid-connect-integration"></a>OpenID Connect integrering
Integrera programmet med Azure AD, efter den [developer instruktioner](active-directory-authentication-scenarios.md). Sedan slutföra frågorna nedan och skicka till waadpartners@microsoft.com.

* Ange autentiseringsuppgifter för en Testklient eller ett konto med ditt program som kan användas av Azure AD-teamet för att testa integreringen.  
* Ger instruktioner för hur Azure AD-teamet kan logga in och ansluta en instans av Azure AD till appen med den [Azure AD medgivande framework](active-directory-integrating-applications.md#overview-of-the-consent-framework). 
* Ange eventuella ytterligare instruktioner som krävs för Azure AD-teamet och testa enkel inloggning med ditt program. 
* Ange informationen nedan:

> Företagsnamn:
> 
> Företagets webbplats:
> 
> Programnamn:
> 
> Programbeskrivningen (200 tecken):
> 
> Programmet webbplats (information):
> 
> Programmet tekniska avdelningen eller kontaktinformation:
> 
> Program-ID för programmet, enligt programinformation på https://portal.azure.com:
> 
> URL som programmet var för kunder för att registrera sig för och/eller köpa programmet:
> 
> Välj upp till tre kategorier för ditt program ska visas (för tillgängliga kategorier Se Azure Active Directory Marketplace):
> 
> Koppla liten ikon (PNG-fil, 45px av 45px bakgrundsfärg):
> 
> Koppla stora programikonen (PNG-fil, 215px av 215px bakgrundsfärg):
> 
> Koppla programmet logotyp (PNG-fil, 150px av 122px transparent bakgrundsfärg):
> 
> 

## <a name="saml-integration"></a>SAML-integrering
Alla appar som stöder SAML 2.0 kan integreras direkt med en Azure AD-klient med hjälp av [dessa instruktioner för att lägga till ett anpassat program](../application-config-sso-how-to-configure-federated-sso-non-gallery.md). När du har testat att integrera dina appar fungerar med Azure AD kan skicka följande information till < mailto:waadpartners@microsoft.com >.

* Ange autentiseringsuppgifter för en Testklient eller ett konto med ditt program som kan användas av Azure AD-teamet för att testa integreringen.  
* Ange SAML inloggnings-URL, utfärdar-URL (enhets-ID) och Reply URL (assertion konsumenten service) värden för ditt program enligt [här](../application-config-sso-how-to-configure-federated-sso-non-gallery.md). Om du vanligtvis ange dessa värden som en del av en SAML-metadatafil, sedan skicka som också.
* Ange en kort beskrivning av hur du konfigurerar Azure AD som en identitetsleverantör i ditt program använder SAML 2.0. Om ditt program har stöd för att konfigurera Azure AD som en identitetsleverantör via en administrativa självbetjäningsportalen kan sedan kontrollera att de autentiseringsuppgifter som anges ovan omfattar möjligheten att ställa in.
* Ange informationen nedan:

> Företagsnamn:
> 
> Företagets webbplats:
> 
> Programnamn:
> 
> Programbeskrivningen (200 tecken):
> 
> Programmet webbplats (information):
> 
> Programmet tekniska avdelningen eller kontaktinformation:
> 
> URL som programmet var för kunder för att registrera sig för och/eller köpa programmet:
> 
> Välj upp till tre kategorier för ditt program ska visas (tillgängliga kategorier finns i [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Koppla liten ikon (PNG-fil, 45px av 45px bakgrundsfärg):
> 
> Koppla stora programikonen (PNG-fil, 215px av 215px bakgrundsfärg):
> 
> Koppla programmet logotyp (PNG-fil, 150px av 122px transparent bakgrundsfärg):
> 
> 

