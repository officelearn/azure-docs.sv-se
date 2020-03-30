---
title: Översikt över Azure Active Directory-autentisering
description: Lär dig mer om de olika autentiseringsmetoderna och säkerhetsfunktionerna för användarloggningar med Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c2fa8488490561e8f11746e8e737718ee9f37
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76261270"
---
# <a name="what-is-azure-active-directory-authentication"></a>Vad är Azure Active Directory-autentisering?

En av huvudfunktionerna i en identitetsplattform är att verifiera eller *autentisera*autentiseringsuppgifter när en användare loggar in på en enhet, ett program eller en tjänst. I Azure Active Directory (Azure AD) innebär autentisering mer än bara verifiering av ett användarnamn och lösenord. Azure AD-autentisering innehåller följande komponenter för att förbättra säkerheten och minska behovet av hjälp med helpdesk:

* Återställning av lösenord för självbetjäning
* Azure Multi-Factor Authentication
* Hybridintegrering för att skriva lösenordsändringar tillbaka till lokal miljö
* Hybridintegrering för att tillämpa principer för lösenordsskydd för en lokal miljö
* Lösenordsfri autentisering

## <a name="improve-the-end-user-experience"></a>Förbättra slutanvändarens upplevelse

Azure AD hjälper till att skydda en användares identitet och förenkla deras inloggningsupplevelse. Funktioner som återställning av lösenord för självbetjäning gör att användarna kan uppdatera eller ändra sina lösenord med hjälp av en webbläsare från vilken enhet som helst. Den här funktionen är särskilt användbar när användaren har glömt sitt lösenord eller deras konto är låst. Utan att vänta på att en helpdesk eller administratör ska ge support kan en användare häva blockeringen och fortsätta att arbeta.

Med Azure Multi Factor Authentication kan användare välja ytterligare en form av autentisering under inloggning, till exempel ett telefonsamtal eller ett meddelande om mobilappar. Den här möjligheten minskar kravet på en enda, fast form av sekundär autentisering som en maskinvarutoken. Om användaren för närvarande inte har någon form av ytterligare autentisering kan de välja en annan metod och fortsätta att arbeta.

![Autentiseringsmetoder som används på inloggningsskärmen](media/concept-authentication-methods/overview-login.png)

Lösenordslös autentisering tar bort användarens behov av att skapa och komma ihåg ett säkert lösenord alls. Med funktioner som säkerhetsnycklarna Windows Hello för företag eller FIDO2 kan användare logga in på en enhet eller ett program utan lösenord. Den här möjligheten kan minska komplexiteten i att hantera lösenord i olika miljöer.

## <a name="self-service-password-reset"></a>Återställning av lösenord för självbetjäning

Återställning av lösenord med självbetjäning ger användarna möjlighet att ändra eller återställa sitt lösenord, utan administratör eller helpdesk inblandning. Om en användares konto är låst eller om de glömmer sitt lösenord kan de följa uppmaningar om att häva blockeringen och återgå till arbetet. Den här möjligheten minskar antalet supportsamtal och produktivitetsförluster när en användare inte kan logga in på sin enhet eller ett program.

Återställning av lösenord med självbetjäning fungerar i följande scenarier:

* **Lösenordsändring -** när en användare vet sitt lösenord men vill ändra det till något nytt.
* **Återställning av lösenord –** när en användare inte kan logga in, till exempel när de har glömt lösenord, och vill återställa sitt lösenord.
* **Kontoupplåsning -** när en användare inte kan logga in eftersom deras konto är utelåst och vill låsa upp sitt konto.

När en användare uppdaterar eller återställer sitt lösenord med självbetjäningslösenordsåterställning kan lösenordet också skrivas tillbaka till en lokal Active Directory-miljö. Återställning av lösenord säkerställer att en användare omedelbart kan använda sina uppdaterade autentiseringsuppgifter med lokala enheter och program.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Multifaktorautentisering är en process där en användare uppmanas under inloggningsprocessen för ytterligare en form av identifiering, till exempel för att ange en kod på sin mobiltelefon eller för att tillhandahålla en fingeravtrycksskanning.

Om du bara använder ett lösenord för att autentisera en användare, lämnar det en osäker vektor för angrepp. Om lösenordet är svagt eller har exponerats någon annanstans, är det verkligen användaren loggar in med användarnamn och lösenord, eller är det en angripare? När du behöver en andra form av autentisering ökar säkerheten eftersom den här ytterligare faktorn inte är något som är lätt för en angripare att hämta eller duplicera.

![Konceptuell bild av de olika formerna av multifaktorautentisering](./media/concept-mfa-howitworks/methods.png)

Azure Multi Factor Authentication fungerar genom att kräva två eller flera av följande autentiseringsmetoder:

* Något du vet, vanligtvis ett lösenord.
* Något du har, till exempel en betrodd enhet som inte är lätt att duplicera, som en telefon eller maskinvarunyckel.
* Något du är - biometri som ett fingeravtryck eller ansikte scan.

Användare kan registrera sig för både återställning av lösenord för självbetjäning och Azure Multi-Factor Authentication i ett steg för att förenkla upplevelsen av ombordstigning. Administratörer kan definiera vilka former av sekundär autentisering som kan användas. Azure Multi-Factor Autentisering kan också krävas när användare utför en självbetjäning lösenordsåterställning för att ytterligare säkra den processen.

## <a name="password-protection"></a>Lösenordsskydd

Som standard blockerar Azure AD svaga lösenord som *Lösenord1*. En global lista över förbjudna lösenord uppdateras automatiskt och tillämpas som innehåller kända svaga lösenord. Om en Azure AD-användare försöker ange sitt lösenord till ett av dessa svaga lösenord får de ett meddelande om att välja ett säkrare lösenord.

Om du vill öka säkerheten kan du definiera anpassade principer för lösenordsskydd. Dessa principer kan använda filter för att blockera alla varianter av ett lösenord som innehåller ett namn som *Contoso* eller en plats som *London*, till exempel.

För hybridsäkerhet kan du integrera Azure AD-lösenordsskydd med en lokal Active Directory-miljö. En komponent som är installerad i prem-miljön får den globala listan över förbjudna lösenord och anpassade principer för lösenordsskydd från Azure AD, och domänkontrollanter använder dem för att bearbeta lösenordsändringshändelser. Den här hybridmetoden ser till att oavsett hur eller var en användare ändrar sina autentiseringsuppgifter, så tillämpar du användningen av starka lösenord.

## <a name="passwordless-authentication"></a>Lösenordsfri autentisering

Slutmålet för många miljöer är att ta bort användningen av lösenord som en del av inloggningshändelser. Funktioner som Azure lösenordsskydd eller Azure Multi-Factor Authentication hjälper till att förbättra säkerheten, men ett användarnamn och lösenord är fortfarande en svag form av autentisering som kan exponeras eller brute-force attackeras.

![Säkerhet kontra bekvämlighet med autentiseringsprocessen som leder till lösenordslös](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

När du loggar in med en lösenordslös metod tillhandahålls autentiseringsuppgifter med hjälp av metoder som biometri med Windows Hello för företag eller en FIDO2-säkerhetsnyckel. Dessa autentiseringsmetoder kan inte enkelt dupliceras av en angripare.

Azure AD innehåller sätt att autentisera med lösenordslösa metoder för att förenkla inloggningsupplevelsen för användare och minska risken för attacker.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [snabbstarten för återställning av lösenord för självbetjäning][quickstart-sspr] och [azure multifaktorautentisering][tutorial-mfa-applications].

Mer information om begrepp för återställning av lösenord med självbetjäning finns i [Hur Azure AD-självbetjäningslösenordsåterställning fungerar][concept-sspr].

Mer information om flerfaktorsautentiseringsbegrepp finns i [Hur Azure Multi-Factor Authentication fungerar][concept-mfa].

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
