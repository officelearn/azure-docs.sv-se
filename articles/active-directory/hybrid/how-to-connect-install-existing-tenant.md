---
title: 'Azure AD Connect: När du redan har Azure AD | Microsoft-dokument'
description: I det här avsnittet beskrivs hur du använder Connect när du har en befintlig Azure AD-klientorganisation.
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
ms.openlocfilehash: 3636b88b14cf7e76e4fb023434316e7ee31ded04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71336811"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: När du har en befintlig klientorganisation
De flesta av ämnena för hur du använder Azure AD Connect förutsätter att du börjar med en ny Azure AD-klientorganisation och att det inte finns några användare eller andra objekt där. Men om du har börjat med en Azure AD-klientorganisation, fyllt den med användare och andra objekt och nu vill använda Connect, är det här avsnittet för dig.

## <a name="the-basics"></a>Grunderna
Ett objekt i Azure AD är antingen bemästrat i molnet (Azure AD) eller lokalt. För ett enda objekt kan du inte hantera vissa attribut lokalt och vissa andra attribut i Azure AD. Varje objekt har en flagga som anger var objektet hanteras.

Du kan hantera vissa användare lokalt och andra i molnet. Ett vanligt scenario för den här konfigurationen är en organisation med en blandning av redovisningsarbetare och säljare. Redovisningsarbetarna har ett lokalt AD-konto, men försäljningsarbetarna har inte det, de har ett konto i Azure AD. Du skulle hantera vissa användare lokalt och vissa i Azure AD.

Om du började hantera användare i Azure AD som också finns lokalt AD och senare vill använda Connect, finns det några ytterligare problem som du måste tänka på.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synkronisera med befintliga användare i Azure AD
När du installerar Azure AD Connect och du börjar synkronisera, gör Azure AD sync-tjänsten (i Azure AD) en kontroll av alla nya objekt och försöker hitta ett befintligt objekt som matchar. Det finns tre attribut som används för den här processen: **userPrincipalName**, **proxyAddresses**och **sourceAnchor**/**immutableID**. En matchning på **userPrincipalName** och **proxyAddresses** kallas en **mjuk matchning**. En matchning på **sourceAnchor** kallas **hård matchning**. För **proxyAddresses tillskrivs** endast värdet med **SMTP:**, som är den primära e-postadressen, används för utvärderingen.

Matchningen utvärderas endast för nya objekt som kommer från Connect. Om du ändrar ett befintligt objekt så att det matchar något av dessa attribut visas ett fel i stället.

Om Azure AD hittar ett objekt där attributvärdena är desamma för ett objekt som kommer från Connect och att det redan finns i Azure AD, tas objektet i Azure AD över av Connect. Det tidigare molnhanterade objektet flaggas som lokalt hanterat. Alla attribut i Azure AD med ett värde i lokalt AD skrivs över med det lokala värdet. Undantaget är när ett attribut har ett **NULL-värde** lokalt. I det här fallet kvarstår värdet i Azure AD, men du kan fortfarande bara ändra det lokalt till något annat.

> [!WARNING]
> Eftersom alla attribut i Azure AD kommer att skrivas över av det lokala värdet, se till att du har bra data lokalt. Om du till exempel bara har hanterad e-postadress i Office 365 och inte har den uppdaterad i lokala AD DS, förlorar du alla värden i Azure AD/Office 365 som inte finns i AD DS.

> [!IMPORTANT]
> Om du använder lösenordssynkronisering, som alltid används av expressinställningar, skrivs lösenordet i Azure AD över med lösenordet i lokala AD. Om användarna används för att hantera olika lösenord måste du informera dem om att de ska använda det lokala lösenordet när du har installerat Connect.

Föregående avsnitt och varning måste beaktas i din planering. Om du har gjort många ändringar i Azure AD som inte återspeglas i lokala AD DS måste du planera för hur du fyller i AD DS med de uppdaterade värdena innan du synkroniserar dina objekt med Azure AD Connect.

Om du matchade dina objekt med en mjuk matchning läggs **sourceAnchor** till i objektet i Azure AD så att en hård matchning kan användas senare.

>[!IMPORTANT]
> Microsoft rekommenderar starkt att du synkroniserar lokala konton med befintliga administrativa konton i Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Hård match mot Soft-match
För en ny installation av Connect finns det ingen praktisk skillnad mellan en mjuk- och en hård match. Skillnaden är i en katastrofåterställning situation. Om du har förlorat servern med Azure AD Connect kan du installera om en ny instans utan att förlora några data. Ett objekt med en sourceAnchor skickas till Connect under den första installationen. Matchningen kan sedan utvärderas av klienten (Azure AD Connect), vilket är mycket snabbare än att göra samma sak i Azure AD. En hård matchning utvärderas både av Connect och av Azure AD. En mjuk matchning utvärderas endast av Azure AD.

### <a name="other-objects-than-users"></a>Andra objekt än användare
För e-postaktiverade grupper och kontakter kan du mjukmatcha baserat på proxyAddresses. Hård matchning är inte tillämpligt eftersom du bara kan uppdatera sourceAnchor/immutableID (endast med PowerShell) på användare. För grupper som inte är e-postaktiverade finns det för närvarande inget stöd för mjukmatchning eller hårdmatchning.

### <a name="admin-role-considerations"></a>Överväganden om administratörsroll
Azure AD Connect matchar inte lokala användarobjekt med objekt som har en administratörsroll för att förhindra att lokala användare matchas med objekt som har en administratörsroll för att förhindra att lokala användare matchas med objekt som har en administratörsroll. Detta är som standard. Så här löser du detta:

1.  Ta bort katalogrollerna från användarobjektet för endast molnet.
2.  Om det fanns ett misslyckat användarsynkroniseringsförsök tar du bort objektet Karantän i karantän i molnet.
3.  Utlösa en synkronisering.
4.  Du kan också lägga till katalogrollerna tillbaka till användarobjektet i molnet när matchningen har skett.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Skapa en ny lokal Active Directory från data i Azure AD
Vissa kunder börjar med en molnbaserad lösning med Azure AD och de har ingen lokal AD. Senare vill de använda lokala resurser och vill skapa en lokal AD baserat på Azure AD-data. Azure AD Connect kan inte hjälpa dig för det här scenariot. Det skapar inte användare lokalt och det har ingen möjlighet att ställa in lösenordet lokalt till samma som i Azure AD.

Om den enda anledningen till att du planerar att lägga till lokala AD är att stödja LOBs (Line-of-Business apps), så kanske du bör överväga att använda [Azure AD-domäntjänster](../../active-directory-domain-services/index.yml) istället.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
