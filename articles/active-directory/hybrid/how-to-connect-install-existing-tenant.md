---
title: 'Azure AD Connect: När du har redan Azure AD | Microsoft Docs'
description: Det här avsnittet beskriver hur du använder Connect när du har en befintlig Azure AD-klient.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1495c14ae4c588661452aa3696019da00be47548
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571373"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: När du har en befintlig klient
De flesta av avsnitten om hur du använder Azure AD Connect förutsätter att du börjar med en ny Azure AD-klient och att det finns inga användare eller det andra objekt. Men om du har börjat med Azure AD-klient fyllt det med användare och andra objekt, och nu vill använda Connect, och det här avsnittet är för dig.

## <a name="the-basics"></a>Grunderna
Ett objekt i Azure AD hanteras antingen i molnet (Azure AD) eller lokalt. För ett enda objekt, kan du hantera vissa attribut lokalt och vissa andra attribut i Azure AD. Varje objekt har en flagga som visar där objektet hanteras.

Du kan hantera vissa användare både lokalt och andra i molnet. Ett vanligt scenario för den här konfigurationen är en organisation med en blandning av redovisning arbetstagare och försäljning. Redovisning arbetare har en lokal AD-konto, men försäljning arbetare inte betyder att det finns ett konto i Azure AD. Du skulle hantera vissa användare lokalt och vissa i Azure AD.

Om du startade att hantera användare i Azure AD som finns också i lokala AD och senare vill använda Connect, och det finns några ytterligare frågor som du behöver tänka på.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synkronisera med befintliga användare i Azure AD
När du installerar Azure AD Connect och du startar synkroniseringen, Azure AD-synkroniseringstjänsten (i Azure AD) har en kontroll på alla nya objekt och försök att hitta ett befintligt objekt så att de matchar. Det finns tre attribut som används för den här processen: **userPrincipalName**, **proxyAddresses**, och **sourceAnchor**/**immutableID** . En matchning på **userPrincipalName** och **proxyAddresses** kallas en **mjuk matchning**. En matchning på **sourceAnchor** kallas **hårda matchning**. För den **proxyAddresses** attributet endast värdet med **SMTP:**, det vill säga primära e-postadressen används för utvärdering.

Matchningen utvärderas bara för nya objekt som kommer från Connect. Om du ändrar ett befintligt objekt så att den matchar någon av dessa attribut kan se du ett fel i stället.

Om Azure AD hittar ett objekt där attributvärdena är desamma för ett objekt som kommer från Connect och att det finns redan i Azure AD, tas objektet i Azure AD över av Connect. Det tidigare molnhanterad objektet flaggas som lokala hanteras. Alla attribut i Azure AD med ett värde i den lokala AD skrivs över med lokala värdet. Undantaget är när ett attribut har en **NULL** värde på plats. I det här fallet kan värdet i Azure AD finns kvar, men du endast ändra det lokala till något annat.

> [!WARNING]
> Eftersom alla attribut i Azure AD ska skrivas över av värdet på plats måste du kontrollera att du har bra data lagras lokalt. Till exempel om du har endast hanterade e-postadress i Office 365 och inte kvar den uppdateras i lokala AD DS, och du förlorar alla värden i Azure AD/Office 365 finns inte i AD DS.

> [!IMPORTANT]
> Om du använder lösenord sync, vilket används alltid av standardinställningar, och sedan lösenordet i Azure AD skrivs över med lösenordet i den lokala AD. Om användarna är vana vid att hantera olika lösenord, måste du informera dem om att de ska använda det lokala lösenordet när du har installerat Connect.

Föregående avsnitt och varningsmeddelandena måste beaktas i planeringen. Om du har gjort många ändringar i Azure AD inte återspeglas i lokala AD DS, måste du planera att fylla i AD DS med de uppdaterade värdena innan du synkroniserar dina objekt med Azure AD Connect.

Om du matchade din objekt med en ungefärlig matchning kommer **sourceAnchor** läggs till objektet i Azure AD så att en hård matchning kan användas senare.

>[!IMPORTANT]
> Microsoft rekommenderar mot att synkronisera lokala konton med befintliga administrativa konton i Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Hård-match vs ungefärlig matchning
Det finns inga praktiska skillnaden mellan mjuk- och en hård matchning för en ny installation av Connect. Skillnaden är i en disaster recovery-situation. Om du har tappat bort din server med Azure AD Connect, kan du installera om en ny instans utan att förlora några data. Ett objekt med en sourceAnchor skickas till Connect under den inledande installationen. Matchningen kan sedan utvärderas av klienten (Azure AD Connect), vilket är mycket snabbare än gör sedan samma sak i Azure AD. En hård matchning utvärderas både av Connect och av Azure AD. En mjuk matchning utvärderas bara av Azure AD.

### <a name="other-objects-than-users"></a>Andra objekt än användare
För e-postaktiverade grupper och kontakter, du kan ungefärlig matchning utifrån proxyAddresses. Hård-match kan inte användas eftersom du bara uppdatera sourceAnchor/immutableID (med PowerShell) för användare endast. För grupper som inte är e-postaktiverade: det finns för närvarande inget stöd för mjuk- eller hårda matchning.

### <a name="admin-role-considerations"></a>Överväganden för Admin-roll
För att förhindra att obetrodda lokala användare matchar med en cloud-användare som har alla administratörsroll matchar Azure AD Connect inte den lokala användarobjekt med objekt som har en administratörsroll. Detta är som standard. Lösa problemet kan du göra följande:

1.  Ta bort katalogrollerna från molnbaserad användarobjektet
2.  Utlös en synkronisering
3.  Du kan också lägga till katalogrollerna användarobjektet i molnet när den matchande har uppstått.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Skapa en ny Active Directory för den lokala data i Azure AD
Vissa kunder börjar med en molnbaserad lösning med Azure AD och de inte har en lokal AD. De vill använda lokala resurser och vill skapa en lokal AD-baserad på Azure AD-data. Azure AD Connect kan hjälpa dig att det här scenariot. Den skapar inte användare både lokalt och den har inte någon möjlighet att ange lösenord för lokala platser till samma som i Azure AD.

Om det enda skälet varför du vill lägga till en lokal AD är att stödja Onlineindexgenerering (Line-of-Business-appar), och sedan kanske du bör överväga för att använda [Azure AD domain services](../../active-directory-domain-services/index.yml) i stället.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
