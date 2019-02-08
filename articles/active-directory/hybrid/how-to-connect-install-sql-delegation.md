---
title: Installera Azure AD Connect med SQL-delegerade administratörsbehörigheter | Microsoft Docs
description: Det här avsnittet beskriver en uppdatering till Azure AD Connect där efter installationen med ett konto som har bara SQL dbo-behörigheter.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: e681d192b3df429fc7aa5f340baa56fa739bad34
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891648"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Installera Azure AD Connect med SQL-delegerade administratörsbehörigheter
Före den senaste versionen av Azure AD Connect kan stöds administrativa delegering, när du distribuerar konfigurationer som krävs för SQL, inte.  Användare som vill installera Azure AD Connect som behövs för att ha administratörsbehörighet för servern (SA) på SQLServer.

Med den senaste versionen av Azure AD Connect, vara distribuera databasen nu utförs out of band av SQL-administratören och installeras sedan av Azure AD Connect-administratören med databasägarrättigheter.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill använda den här funktionen måste du upptäcker att det finns flera rörliga delar och var och en kan omfatta en annan administratör i din organisation.  I följande tabell sammanfattas de enskilda rollerna och sina respektive uppgifter distribuera Azure AD Connect med den här funktionen.

|Roll|Beskrivning|
|-----|-----|
|Domän eller skog AD-administratör|Skapar domänen på tjänstkontot som används av Azure AD Connect för att köra synkroniseringstjänsten.  Mer information om tjänstkonton finns i [konton och behörigheter](reference-connect-accounts-permissions.md).
|SQL-administratör|Skapar ADSync-databas och ger inloggningen + dbo åtkomst till Azure AD Connect-administratör och det tjänstkonto som skapats av administratören domän/skog.|
Azure AD Connect-administratör|Installerar Azure AD Connect och anger kontot under Anpassad installation.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Steg för att installera Azure AD Connect med SQL delegerade behörigheter
Använd följande steg för att etablera databasen out of band och installera Azure AD Connect med ägarbehörighet för databasen.

>[!NOTE]
>Även om det inte krävs, är det **rekommenderas starkt** att Latin1_General_CI_AS sorteringen är markerad när du skapar databasen.


1.  Låt SQL-administratören skapa ADSync-databas med en skiftlägesokänslig sorteringsföljden **(Latin1_General_CI_AS)**.  Databasen måste ha namnet **ADSync**.  Den återställningsmodellen och kompatibilitetsnivån inneslutning typ uppdateras till rätt värden när Azure AD Connect är installerad.  Men sorteringsföljden måste anges korrekt av SQL-administratören annars Azure AD Connect kommer att blockera installationen.  Om du vill återställa SA måste ta bort och återskapa databasen.</br>
![Sortering](./media/how-to-connect-install-sql-delegation/sql4.png)
2.  Bevilja Azure AD Connect-administratör och tjänstkontot i domänen följande behörigheter:
    - SQL-inloggning 
    - **databasen owner(dbo)** rättigheter.  </br>
![Behörigheter](./media/how-to-connect-install-sql-delegation/sql3a.png)
3.  Skicka ett e-postmeddelande till Azure AD Connect-administratören som anger SQL server och instans namn som ska användas när du installerar Azure AD Connect.

## <a name="additional-information"></a>Ytterligare information
När databasen har etablerats kan Azure AD Connect-administratören installera och konfigurera den lokala synkronisering när så önskas.  

Läs mer om hur du installerar Azure AD Connect med en befintlig databas [installera Azure AD Connect med en befintlig ADSync-databas](how-to-connect-install-existing-database.md)

## <a name="next-steps"></a>Nästa steg
- [Komma igång med Azure AD Connect med standardinställningar](how-to-connect-install-express.md)
- [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md)
- [Installera Azure AD Connect med en befintlig ADSync-databas](how-to-connect-install-existing-database.md)  
