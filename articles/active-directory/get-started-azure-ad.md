---
title: Kom igång med Azure Active Directory | Microsoft Docs
description: Hämta licenser, lägga till domännamn, skapa anpassade inloggningssidan och lägga till Självbetjäning för återställning av lösenord i Azure Active Directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: bf7647d8489a102175c4e9de799793e6550495e7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714416"
---
# <a name="get-started-with-azure-ad"></a>Kom igång med Azure AD
Moderna Identitetshantering kräver skalbart och konsekvent reliablity att kontrollera tillgänglighet för program och tjänster till endast autentiserade användare. För att stödja identity management behoven hos användare, IT-behov rätt ett sätt att ge åtkomst till offentliga godkända program som en tjänst (SaaS)-appar, ett sätt att värden interna branschspecifika appar och även kan förbättra lokalt app-utveckling och användning. Alla dessa krav peka behovet av en molnbaserad identitetshanteringslösning.      

Azure Active Directory (AD Azure) är Microsofts flera innehavare, molnbaserade katalog och identity management-tjänsten. Azure AD kombinerar core katalogtjänster, avancerade identitet styrning och hantering av åtkomst. Flera innehavare, fördelade hög tillgänglighet utformning av Azure AD-innebär att du kan använda det för dina viktigaste affärsbehov.

Azure AD innehåller en fullständig uppsättning identitetshanteringsfunktionerna inklusive möjligheten att synkronisera lokala resursinformation, anpassa företagsinformation, enkla licenshantering och även självbetjäning lösenordshantering. Dessa enkelt att distribuera funktioner kan hjälpa dig att komma igång med Azure AD för att skydda molnbaserade program, effektivisera IT-processer, minska kostnaderna och säkerställer att företagsefterlevnad mål är uppfyllda.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

Resten av den här artikeln visar hur du kommer igång med Azure AD. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Registrera dig för Azure Active Directory Premium
Att [Kom igång med Azure Active Directory (AD Azure) Premium](active-directory-get-started-premium.md), du kan köpa licenser och koppla dem till din Azure-prenumeration. Om du skapar en ny Azure-prenumeration, måste du också aktivera licensieringsplan och åtkomst till Azure AD-tjänsten. 

Om du vill registrera dig för Active Directory Premium kan du välja mellan flera alternativ: 

- Använda din Azure- eller Office 365-prenumeration
- Använda en Enterprise Mobility + Security-licensieringsplan
- Använda en Microsoft-volymlicensieringsplan

### <a name="verification-step"></a>Verifieringssteg
Se till att du kan logga in till tjänsten efter aktivera prenumerationen.

## <a name="add-a-custom-domain-name"></a>Lägg till ett anpassat domännamn
Varje Azure AD-katalog som medföljer ett första domännamn i form av *domainname*. onmicrosoft.com. Det ursprungliga domännamnet inte kan ändras eller tas bort, men du kan också [lägga till företagets domännamn i Azure AD](add-custom-domain.md). Din organisation har till exempel förmodligen andra domännamn som används för att göra affärs- och användare som loggar in med företagets domännamn. Lägga till anpassade domännamn i Azure AD kan du tilldela användarnamn i katalogen som dina användare som ”alice@contoso.com”. i stället för 'alice@.onmicrosoft.com'. Processen är enkel:

1. Lägga till det anpassade domännamnet i din katalog
2. Lägga till en DNS-post för domännamnet hos domännamnsregistratorn
3. Verifiera det anpassade domännamnet i Azure AD

### <a name="verification-step"></a>Verifieringssteg
När du lägger till en anpassad domän måste du kontrollera att den innehåller den **verifierad** status visas på den **anpassade domännamn** bladet för Azure AD-portalen.

## <a name="add-company-branding-to-your-sign-in-page"></a>Lägga till företagsanpassning till din inloggningssida 
För att undvika förvirring vill många företag använda ett enhetligt utseende på alla webbplatser och tjänster som de hanterar. Azure Active Directory (AD Azure) tillhandahåller den här funktionen genom att låta dig [anpassa utseendet på inloggningssidan med företagets logotyp och egna färgscheman](customize-branding.md). På inloggningssidan är den sida som visas när du loggar in på Office 365 eller andra webbaserade program som använder Azure AD som deras identitetsleverantör. Du kan interagera med den här sidan om du vill ange dina autentiseringsuppgifter.

### <a name="verification-step"></a>Verifieringssteg
Logga in på Azure-portalen och kontrollera att dina anpassade inloggningssidan och eventuella ytterligare språk anpassningar har konfigurerats korrekt. 

## <a name="add-new-users"></a>Lägg till nya användare
Du kan [lägga till nya användare till din organisations Azure AD](add-users-azure-active-directory.md) en i taget med Azure-portalen eller genom att synkronisera dina lokala Windows Server AD-resursdata. Du kan lägga till molnbaserade användare direkt från Azure AD-portalen eller synkroniserar lokala användarinformation.

För att aktivera lokal identitetssynkronisering till Azure AD måste du installera och konfigurera [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) på en server i din organisation. Det här programmet hanterar synkronisering av användare och grupper från din befintliga identitetskälla till Azure AD-klienten.

### <a name="verification-step"></a>Verifieringssteg
När du skapar eller synkronisera nya användare och kontrollera att de visas i Azure AD.

## <a name="assign-licenses"></a>Tilldela licenser
Även om hur du skaffar en prenumeration är allt du behöver konfigurera betalda funktioner, måste du fortfarande [tilldela användarlicenser](license-users-groups.md) för Azure AD Premium betalda funktioner. Alla användare som ska ha åtkomst till eller som hanteras via en Azure AD betald funktionen måste tilldelas en licens. Licenstilldelning finns en mappning mellan en användare och en köpta tjänst, till exempel Azure AD Premium, Basic eller Enterprise Mobility + Security.

Du kan använda gruppbaserade licenstilldelning ställa in regler som i följande exempel:

- Alla användare i din katalog få automatiskt en licens
- Alla med lämpliga befattning hämtar en licens
- Du kan delegera beslutet till andra chefer i organisationen (via självbetjäning grupper)

### <a name="verification-step"></a>Verifieringssteg
Granska tilldelade och tillgängliga licenser under **Azure Active Directory** > **licenser** > **alla produkter**.

## <a name="configure-self-service-password-reset"></a>Konfigurera självbetjäning vid återställning av lösenord
[Självbetjäning för lösenordsåterställning (SSPR)](authentication/quickstart-sspr.md) erbjuder ett enkelt sätt för IT-administratörer så att användarna kan återställa eller låsa upp sitt lösenord eller konton. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk.

### <a name="verification-step"></a>Verifieringssteg
Granska aktiverat SSPR egenskaper under **Azure Active Directory** > **lösenordsåterställning** att säkerställa rätt användar- och grupptilldelningar har gjorts. 


## <a name="next-steps"></a>Nästa steg
[Azure Active Directory service-sidan](https://azure.microsoft.com/services/active-directory/)

[Azure Active Directory information prissättningssidan](https://azure.microsoft.com/pricing/details/active-directory/)