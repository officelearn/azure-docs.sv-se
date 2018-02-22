---
title: "Installera Azure AD Connect med administratörsbehörighet för SQL delegerad | Microsoft Docs"
description: "Det här avsnittet beskriver en uppdatering till Azure AD Connect som gör det möjligt för installation med ett konto som har bara SQL dbo-behörigheter."
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: jparsons
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c2d77c37f2f65c9a7db1fd5c4010fc43bcbc7ebf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Installera Azure AD Connect med administratörsbehörighet för SQL delegerad
Innan den senaste versionen i Azure AD Connect stöds administrativa delegering, när du distribuerar konfigurationer som krävs för SQL, inte.  Användare som vill installera Azure AD Connect som behövs för att ha administratörsbehörighet för servern (SA) på SQLServer.

Med den senaste versionen av Azure AD Connect vara etablering databasen kan nu utförs out-of-band av SQL-administratör och installeras sedan av Azure AD Connect-administratör med ägarrättigheter för databasen.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill använda den här funktionen måste du inser att det finns flera rörliga delar och var och en kan innebära att en annan administratör i din organisation.  I följande tabell sammanfattas de enskilda rollerna och deras respektive uppgifter för att distribuera Azure AD Connect med den här funktionen.

|Roll|Beskrivning|
|-----|-----|
|Domän eller skog AD-administratör|Skapar domänen nivån tjänstkontot som används av Azure AD Connect för att köra synkroniseringstjänsten.  Mer information om tjänstkonton finns [konton och behörigheter](active-directory-aadconnect-accounts-permissions.md).
|SQL-administratör|Skapar databasen ADSync och ger inloggningen + dbo åtkomst till Azure AD Connect-administratör och det tjänstkonto som skapats av administratören domänen/skogen.|
Administratör för Azure AD Connect|Installerar Azure AD Connect och anger kontot under Anpassad installation.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Steg för att installera Azure AD Connect med SQL delegerade behörigheter
Använd följande steg för att tillhandahålla databasen out of band och installera Azure AD Connect med databasbehörighet för ägare.

>[!NOTE]
>Även om det inte krävs, är det **rekommenderas starkt** att Latin1_General_CI_AS sorteringen är markerad när du skapar databasen.


1.  SQL-administratör skapa ADSync databasen med en skiftlägeskänslig sorteringsordning har **(Latin1_General_CI_AS)**.  Databasen måste ha namnet **ADSync**.  Återställningsmodellen, kompatibilitetsnivå och inneslutning typen uppdateras till rätt värden när Azure AD Connect är installerat.  Men i sorteringsordningen måste vara korrekt inställda av SQL-administratören annars Azure AD Connect kommer att blockera installationen.  Om du vill återställa SA måste ta bort och återskapa databasen.</br>
![Sortering](media/active-directory-aadconnect-sql-delegation/sql1.png)
2.  Tilldela Azure AD Connect-administratören och domänkontot följande behörigheter:
    - SQL-inloggning 
    - **databasen owner(dbo)** rättigheter.  </br>
![Behörigheter](media/active-directory-aadconnect-sql-delegation/sql3.png)
3.  Skicka ett e-postmeddelande till Azure AD Connect-administratören som anger namnet på SQL server- och instansnamn som ska användas när du installerar Azure AD Connect.

## <a name="additional-information"></a>Ytterligare information
När databasen har etablerats kan Azure AD Connect-administratören installera och konfigurera synkronisering av lokala när vill.  

Förutom stöd för nya installationer av Azure AD Connect är den här funktionen gör det också möjligt delegering för scenarier med den **/UseExistingDatabase** flaggan.  Mer information om hur du installerar Azure AD Connect med en befintlig databas finns [installera Azure AD Connect med en befintlig ADSync-databas](active-directory-aadconnect-existing-database.md)


## <a name="next-steps"></a>Nästa steg
- [Komma igång med Azure AD Connect med standardinställningar](active-directory-aadconnect-get-started-express.md)
- [Anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
- [Installera Azure AD Connect med en befintlig ADSync-databas](active-directory-aadconnect-existing-database.md)  