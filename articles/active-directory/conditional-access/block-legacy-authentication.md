---
title: Blockera äldre autentisering till Azure Active Directory (Azure AD) med villkorlig åtkomst | Microsoft Docs
description: Lär dig hur du konfigurerar en princip för villkorlig åtkomst i Azure Active Directory (Azure AD) för åtkomstförsök från ej betrodda nätverk.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ddfea3ec7380a36f937052a6a994504ca081f187
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53020181"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Så här: blockera äldre autentisering till Azure AD med villkorlig åtkomst   

Om du vill ge dina användare enkel åtkomst till dina appar i molnet, stöder Azure Active Directory (Azure AD) ett stort antal autentiseringsprotokoll, inklusive äldre autentisering. Äldre protokoll stöder dock inte multifaktorautentisering (MFA). MFA är ett vanligt krav adress identitetsstöld i många miljöer. 

Om din miljö är redo att blockera äldre autentisering för att förbättra din klient protection kan göra du det här målet med villkorlig åtkomst. Den här artikeln förklarar hur du kan konfigurera principer för villkorlig åtkomst att blockera äldre autentisering för din klient.



## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med: 

- Den [grundbegreppen](overview.md) av Azure AD villkorsstyrd åtkomst 
- Den [bästa praxis](best-practices.md) för att konfigurera principer för villkorlig åtkomst i Azure portal



## <a name="scenario-description"></a>Scenariobeskrivning

Azure AD stöder flera av de mest använda protokoll för autentisering och auktorisering, inklusive äldre autentisering. Äldre refererar till protokoll som använder grundläggande autentisering. Normalt kan inte dessa protokoll tvinga alla typer av tvåfaktorsautentisering. Exempel för appar som baseras på äldre autentisering är:

- Äldre Microsoft Office-program

- Appar med hjälp av e-postprotokoll som POP, IMAP och SMTP

En faktor autentisering (till exempel användarnamn och lösenord) är inte tillräckligt med dessa dagar. Lösenord är felaktigt eftersom de är lätta att gissa och vi (mänsklig) är felaktig på välja bra lösenord. Lösenord är också sårbara för attacker som nätfiske och lösenord besprutningsmedel olika. En av de enklaste saker som du kan göra för att skydda mot hot för lösenord är att implementera MFA. Med MFA, även om en angripare får tillgång till en användares lösenord räcker lösenordet enbart inte att autentisera och komma åt data.

Hur kan du förhindra att appar som använder äldre autentisering från att komma åt resurser i din klient Rekommendationen är att bara blockera dem med en princip för villkorlig åtkomst. Om det behövs kan tillåta du enbart vissa användare och specifika nätverksplatser du använder appar som baseras på äldre autentisering.




## <a name="implementation"></a>Implementering

Det här avsnittet beskrivs hur du konfigurerar principer för villkorlig åtkomst blockera äldre autentisering. 

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering 

Du kan ange ett villkor som är kopplad till klientappar som används för att komma åt resurser i en princip för villkorlig åtkomst. Klient apps villkor kan du begränsa omfånget till appar som använder äldre autentisering genom att välja **andra klienter** för **mobilappar och skrivbordsklienter**.

![Övriga klienter](./media/block-legacy-authentication/01.png)

Om du vill blockera åtkomst för dessa appar måste du markera **blockera åtkomst**.

![Blockera åtkomst](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>Välj användare och appar i molnet

Om du vill blockera äldre autentisering för din organisation kan tror du förmodligen att du kan göra detta genom att välja:

- Alla användare

- Alla molnappar

- Blockera åtkomst
 

![Tilldelningar](./media/block-legacy-authentication/03.png)



Azure har en säkerhetsfunktion som hindrar dig från att skapa en princip så här eftersom den här konfigurationen bryter mot den [bästa praxis](best-practices.md) för principer för villkorlig åtkomst.
 
![Principkonfigurationen stöds inte](./media/block-legacy-authentication/04.png)


Funktionen säkerhet är nödvändigt eftersom *blockerar alla användare och alla molnappar* finns risken för att blockera hela din organisation från att logga in på din klient. Du inkludera inte minst en användare för att uppfylla kravet på minimal bästa praxis. Du kan också 

![Principkonfigurationen stöds inte](./media/block-legacy-authentication/05.png)


Du kan uppfylla det här säkerhetsfunktion genom att exkludera en användare från din princip. Vi rekommenderar att du bör definiera några [nödfall-åtkomst till administratörskonton i Azure AD](../users-groups-roles/directory-emergency-access.md) och exkluderar dem från din princip.
 

## <a name="policy-deployment"></a>Distribution av princip

Innan du placerar principen till produktion måste ta hand om:
 
- **Tjänstkonton** – identifiera användarkonton som används som tjänstkonton eller av enheter, till exempel konferens rummet telefoner. Kontrollera att dessa konton har starka lösenord och lägga till dem i en undantagen grupp.
 
- **Logga in rapporter** – granska inloggning rapporten och leta efter **andra klienter** trafik. Identifiera övre användning och undersöka varför den inte används. Trafiken genereras vanligtvis av äldre Office-klienter som inte använder modern autentisering eller vissa tredjeparts-e-postappar. Gör en plan för att flytta användning från de här apparna, eller om effekten är låg, meddela användarna att de inte kan använda de här apparna längre.
 
Mer information finns i [hur ska du distribuera en ny princip?](best-practices.md#how-should-you-deploy-a-new-policy).



## <a name="what-you-should-know"></a>Det här bör du känna till

Konfigurera en princip för **andra klienter** blockerar för hela organisationen från vissa klienter som SPConnect. Det här blocket inträffar eftersom äldre klienter autentiseras på oväntade sätt. Problemet gäller inte för viktiga Office-program som de äldre Office-klienterna.

Det kan ta upp till 24 timmar innan principen ska träda i kraft.

Du kan välja alla tillgängliga bevilja kontroller för det andra villkoret för klienter; slutanvändarens upplevelse är dock alltid samma - blockerat åtkomsten.

Du kan konfigurera alla andra villkor bredvid det andra villkoret för klienter.




## <a name="next-steps"></a>Nästa steg

Om du inte är bekant med att konfigurera principer för villkorlig åtkomst än [kräva MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md) ett exempel.
