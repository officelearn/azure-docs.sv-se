---
title: Bygg återhämtning vid extern användarautentisering med Azure Active Directory
description: En guide för IT-administratörer och arkitekter för att skapa en elastisk autentisering för externa användare
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920068"
---
# <a name="build-resilience-in-external-user-authentication"></a>Bygg återhämtning vid extern användarautentisering

[Azure Active Directory B2B-samarbete](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (Azure AD B2B) är en funktion i [externa identiteter](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) som gör det möjligt att samar beta med andra organisationer och individer. Det möjliggör säker onboarding av gäst användare i din Azure AD-klient utan att behöva hantera sina autentiseringsuppgifter. Externa användare får sin identitet och sina autentiseringsuppgifter från en extern identitetsprovider (IdP) så att de inte behöver komma ihåg en ny autentiseringsuppgift. 

## <a name="ways-to-authenticate-external-users"></a>Sätt att autentisera externa användare

Du kan välja metoder för extern användarautentisering till din katalog. Du kan använda Microsoft IDP: er eller andra IDP: er.

Med varje extern IdP, tar du ett beroende på tillgängligheten för den IdP. Med vissa metoder för att ansluta till IDP: er finns det saker du kan göra för att öka din återhämtning.

> [!NOTE] 
> Azure AD B2B har inbyggd möjlighet att autentisera alla användare från en [Azure Active Directory](https://docs.microsoft.com/azure/active-directory) -klient eller med ett personligt [Microsoft-konto](https://account.microsoft.com/account). Du behöver inte göra någon konfiguration med de här inbyggda alternativen.

### <a name="considerations-for-resilience-with-other-idps"></a>Överväganden för återhämtning med andra IDP: er

När du använder externa IDP: er för gäst användarautentisering, finns det vissa konfigurationer som du måste se till att du upprätthåller för att förhindra störningar.

| Autentiseringsmetod| Överväganden vid återhämtning |
| - | - |
| Federation med sociala IDP: er som [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) eller [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation).| Du måste underhålla ditt konto med IdP och konfigurera ditt klient-ID och klient hemlighet. |
| [Direkt Federation med SAML och WS-Federation identitets leverantörer](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| Du måste samar beta med IdP-ägaren för åtkomst till deras slut punkter, som du är beroende av. <br>Du måste underhålla de metadata som innehåller certifikaten och slut punkterna. |
| [E-post med eng ång slö sen ord](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| Med den här metoden är du beroende av Microsofts e-postsystem, användarens e-postsystem och användarens e-postklient. |


 

## <a name="self-service-sign-up-preview"></a>Självbetjänings registrering (för hands version)

Som ett alternativ till att skicka inbjudningar eller länkar kan du aktivera [självbetjänings registrering](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview).  Detta gör att externa användare kan begära åtkomst till ett program. Du måste skapa en [API-anslutning](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) och koppla den till ett användar flöde. Du kopplar användar flöden som definierar användar upplevelsen med ett eller flera program. 

Det går att använda [API-kopplingar](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) för att integrera ditt självbetjänings registrerings användar flöde med externa system-API: er. Denna API-integrering kan användas för [anpassade godkännande arbets flöden](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals), [utföra identitets verifiering](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up)och andra uppgifter som att skriva över användarattribut. Om du använder API: er måste du hantera följande beroenden.

* **API-koppling-autentisering**: om du konfigurerar en anslutning krävs en slut punkts-URL, ett användar namn och ett lösen ord. Konfigurera en process som de här autentiseringsuppgifterna upprätthålls av och arbeta med API-ägaren för att se till att du känner till eventuella förfallo scheman.

* **API-kopplingens svar**: utforma API-anslutningar i registrerings flödet för att inte fungera korrekt om API: t inte är tillgängligt. Undersök och tillhandahålla API-utvecklare dessa API- [exempel svar](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) och [bästa praxis för fel sökning](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector). Arbeta med API Development-teamet för att testa alla möjliga svars scenarier, inklusive fortsatta, validerings fel och blockera svar. 

## <a name="next-steps"></a>Nästa steg
Återhämtnings resurser för administratörer och arkitekter
 
* [Bygg återhämtning med hantering av autentiseringsuppgifter](resilience-in-credentials.md)

* [Bygg återhämtning med enhets tillstånd](resilience-with-device-states.md)

* [Bygg återhämtning med hjälp av den kontinuerliga åtkomst utvärderingen (CAE)](resilience-with-continuous-access-evaluation.md)

* [Bygg återhämtning i din hybrid autentisering](resilience-in-hybrid.md)

* [Bygg återhämtning i program åtkomst med Application Proxy](resilience-on-premises-access.md)

Återhämtnings resurser för utvecklare

* [Bygg IAM-återhämtning i dina program](resilience-app-development-overview.md)

* [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)
 
