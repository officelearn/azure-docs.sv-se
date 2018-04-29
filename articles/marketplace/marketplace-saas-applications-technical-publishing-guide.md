---
title: Azure Marketplace SaaS-program Technical publicering Guide
description: Stegvisa instruktioner och publishing checklistor för publicering av SaaS-program på Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: eb6db45ca0fcb6879aeaeaaf70715691cac438b0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS-program tekniska publiceringsguide

Välkommen till guiden Publicera Azure Marketplace SaaS-program Technical. Den här guiden är avsedd att hjälpa kandidat och befintliga utgivare för att visa en lista över sina program och tjänster i Azure Marketplace med SaaS-program som erbjuder.  
Du vill använda erbjudandet för SaaS-program när lösningen ska distribueras i din egen Azure-prenumeration och kunder kommer att logga in via ett användargränssnitt som du kan skapa och hantera att testa programmet. Detta åstadkoms med hjälp av [Azure Active Directory (AD Azure)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) utnyttjar den befintliga miljön som utvärderingsversion. Med andra ord är det en kund ledde, värdtjänster kostnadsfri utvärderingsversion. Det är viktigt att exponera din lösning på ett sätt som ger möjlighet att utforska din lösning separat utan avgift eller avgift för centraliserad i molnet och så att den här erbjudandetypen tillhandahåller en utvärderingsversion som matchar hur kunder söka efter molnlösningar.  

En översikt över alla andra Marketplace-erbjudanden, referera till den [Marketplace Publisher Guide](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>Teknisk vägledning för SaaS-program
Tekniska krav för SaaS-program är enkelt. Utgivare krävs endast att integreras med Azure AD som ska publiceras.  Azure AD-integrering med program är väl dokumenterat och Microsoft tillhandahåller flera SDK-verktyg och resurser för att åstadkomma detta.  

Om du vill starta, rekommenderar vi att du har en prenumeration som är dedikerad för din Azure Marketplace-publicering, så att du kan isolera arbete från andra initiativ. Dessutom, om de inte redan installerats, rekommenderar vi att du har följande verktyg som en del av din utvecklingsmiljö: 
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure powerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0)  
- [Azure Developer Tools (se vad som är tillgängligt)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>Resurser
Följande tabell innehåller länkar till de bästa Azure AD-resurserna för att komma igång: 

**Dokumentation**

- [Azure Active Directory-Guide för utvecklare](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

- [Integrera med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

- [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

- [Plan för Azure - säkerhet och identitet](https://azure.microsoft.com/roadmap/?category=security-identity)

**Videoklipp**

- [Azure Active Directory-autentisering med Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Azure Active Directory-identitet tekniska genomgång - del 1 av 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Azure Active Directory-identitet tekniska genomgång - del 2 av 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Skapa appar med Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [Microsoft Azure-videor fokuserar på Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Utbildning**  
- [Microsoft Azure för IT-proffs innehåll serien: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Tjänsten för Azure Active Directory-uppdateringar**  
- [Tjänsten för Azure AD-uppdateringar](https://azure.microsoft.com/updates/?product=active-directory)

Du kan använda följande resurser för support:
- [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Azure Active Directory-galleriet
Du kan också låta programmet visas i Azure AD application gallery som ingår i Azure Marketplace AppStore förutom med programmet visas i Azure Marketplace/AppSource. Kunder som använder Azure AD som en identitetsleverantör kan hitta annan SaaS-program kopplingar publicerade här. IT-administratörer kan lägga till kopplingar från galleriet appen och sedan konfigurera och använda kopplingar för enkel inloggning (SSO) och etablering. Azure AD stöder alla större federation-protokoll för enkel inloggning, inklusive SAML 2.0, OpenID Connect, OAuth och WS-Fed.  

När du har testat att integrera dina appar fungerar med Azure AD, skicka din begäran om åtkomst på vår Portal för program-nätverk. Om du har en Office 365-konto kan du använda som för att logga in på portalen. Du kan använda ditt Microsoft-konto (till exempel Outlook eller Hotmail) för att logga in om du inte har något Office 365-konto.

## <a name="program-benefits"></a>Programförmåner
- Ger den bästa möjliga enkel inloggning för kunder
- Enkel och minimal konfiguration av programmet
- Kunder kan söka efter programmet och Sök i galleriet
- Kunder kan använda den här integreringen oavsett vilka Azure AD-SKU de använder (ledigt, Basic eller Premium)
- Steg för steg-konfiguration självstudier för våra ömsesidiga kunder
- Gör att användaren etableringen för samma app om du använder SCIM

## <a name="prerequisites"></a>Förutsättningar
Om du vill visa ett program i Azure AD-galleriet måste först programmet implementera ett av federation-protokoll som stöds av Azure AD. Läs villkoren för Azure AD-programgalleriet på [juridisk Information för Microsoft Azure](https://azure.microsoft.com/support/legal/).  

Nedan beskrivs ytterligare information om nödvändiga komponenter beroende på vilket protokoll du använder:

**OpenID Connect** – skapa flera innehavare program i Azure AD och implementera medgivande ramverk för programmet. Skicka inloggningsbegäran om till vanliga slutpunkten så att en kund kan ge medgivande till programmet. Du kan styra kunden användaråtkomst baserat på klient-ID och användarens UPN togs emot i token.  
**SAML 2.0 eller WS-Fed** -programmet måste ha en möjlighet att göra SAML eller WS-Fed SSO-integrering i SP eller IdP-läge.
