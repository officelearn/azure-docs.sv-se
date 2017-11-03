---
title: 'Azure AD Connect: Om du redan har Azure AD | Microsoft Docs'
description: "Det här avsnittet beskriver hur du använder Anslut när du har en befintlig Azure AD-klient."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: a62a3954d10e718f5d180ddb725c6a9c7cda56c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: När du har en befintliga klient
De flesta avsnitt för hur du använder Azure AD Connect förutsätter att du börjar med en ny Azure AD-klient och att det finns inga användare eller det andra objekt. Men om du har börjat med Azure AD-klient fylls med användare och andra objekt, och nu vill använda Connect, sedan det här avsnittet är för dig.

## <a name="the-basics"></a>Grunderna
Ett objekt i Azure AD antingen lärt dig i molnet (Azure AD) eller lokalt. För ett enskilt objekt kan du hantera vissa attribut för lokala och vissa andra attribut i Azure AD. Varje objekt har en flagga som anger om objektet hanteras.

Du kan hantera vissa användare på lokalt och andra i molnet. Ett vanligt scenario för den här konfigurationen är en organisation med en blandning av redovisning arbetare och försäljning personer. Redovisning arbetare har en lokal AD-kontot, men de försäljning Worker inte, har ett konto i Azure AD. Du kan hantera vissa användare lokalt och vissa i Azure AD.

Om du startade att hantera användare i Azure AD som finns också i lokala AD och senare vill använda Connect, och det finns några ytterligare frågor som du behöver tänka på.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synkronisera med befintliga användare i Azure AD
När du installerar Azure AD Connect och du startar synkroniseringen, Azure AD sync-tjänsten (i Azure AD) har en kontroll av alla nya objekt och försök att hitta ett befintligt objekt som matchar. Det finns tre attribut som används för den här processen: **userPrincipalName**, **proxyAddresses**, och **sourceAnchor**/**immutableID** . En matchning på **userPrincipalName** och **proxyAddresses** kallas en **mjuka matchar**. En matchning på **sourceAnchor** kallas **hårda matchar**. För den **proxyAddresses** attributet värdet med **SMTP:**, som den primära e-postadressen används för utvärdering.

Matchningen utvärderas bara för nya objekt som kommer från Connect. Om du ändrar en befintlig objekt så att den matchar någon av dessa attribut kan se du ett fel i stället.

Om Azure AD hittar ett objekt där attributvärden är desamma för ett objekt som kommer från Connect och att det finns redan i Azure AD, tas objektet i Azure AD över av Connect. Tidigare molnhanterade objekt flaggas som lokalt hanteras. Alla attribut i Azure AD med ett värde i lokala AD skrivs över med lokalt värde. Undantaget är när ett attribut har en **NULL** värdet lokalt. I det här fallet kan värdet i Azure AD finns kvar, men du fortfarande endast ändra den lokala till något annat.

> [!WARNING]
> Eftersom alla attribut i Azure AD ska skrivas över av värdet lokalt måste du kontrollera att du har bra data lokalt. Till exempel om du bara har hanterat e-postadress i Office 365 och inte kvar den uppdateras i lokal AD DS, och du förlorar alla värden i Azure AD/Office 365 finns inte i AD DS.

> [!IMPORTANT]
> Om du använder Lösenordssynkronisering används alltid av standardinställningar, och sedan lösenordet i Azure AD skrivs över med lösenordet i lokala AD. Om användarna används för att hantera olika lösenord, måste du informera dem om att de ska använda det lokala lösenordet när du har installerat Connect.

Föregående avsnitt och varning måste beaktas i planeringen. Om du har gjort många ändringar i Azure AD inte återspeglas i lokala AD DS, måste du planera att fylla i AD DS med de uppdaterade värdena innan du synkroniserar dina objekt med Azure AD Connect.

Om du matchar din objekt med en mjuk matchning sedan **sourceAnchor** läggs till objektet i Azure AD så hårda matchar senare kan användas.

### <a name="hard-match-vs-soft-match"></a>Hård-match eller Soft-matchning
För en ny installation av Connect finns inga praktiska skillnaden mellan soft- och en hård matchning. Skillnaden är i en situation med disaster recovery. Om du har förlorat din server med Azure AD Connect kan installera du en ny instans utan att förlora data. Ett objekt med en sourceAnchor skickas till Connect under första installationen. Matchningen kan sedan utvärderas av klienten (Azure AD Connect), vilket är mycket snabbare än att göra samma sak i Azure AD. En hård matchning ska utvärderas efter Connect och av Azure AD. En mjuk matchning utvärderas bara av Azure AD.

### <a name="other-objects-than-users"></a>Andra objekt än användare
För e-postaktiverade grupper och kontakter, du kan soft-matcha utifrån proxyAddresses. Hård-match kan inte användas eftersom du kan bara uppdatera de sourceAnchor/immutableID (med PowerShell) på användare bara. För grupper som inte är e-postaktiverade, finns det inget stöd för mjuk- eller hårddiskar matchning.

## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Skapa en ny Active Directory för lokala data i Azure AD
Vissa kunder börja med en molnbaserad lösning med Azure AD och de inte har en lokal AD. De vill använda lokala resurser och vill skapa en lokal AD-baserad på data i Azure AD. Azure AD Connect kan hjälpa dig för det här scenariot. Den skapar inte användare lokala och har inte någon möjlighet att ange lösenord för lokal till samma som i Azure AD.

Om det enda skäl varför du vill lägga till lokala AD är att stödja LOB (Line-of-Business-program), kanske du bör överväga för att använda [Azure AD domain services](../../active-directory-domain-services/index.md) i stället.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
