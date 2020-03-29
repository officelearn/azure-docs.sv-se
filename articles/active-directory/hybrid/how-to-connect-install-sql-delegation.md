---
title: Installera Azure AD Connect med SQL-delegerade administratörsbehörigheter | Microsoft-dokument
description: I det här avsnittet beskrivs en uppdatering av Azure AD Connect som gör det möjligt att installera med ett konto som bara har SQL dbo-behörigheter.
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
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60243514"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Installerar Azure AD Connect med SQL-delegerade administratörsbehörigheter
Före den senaste Azure AD Connect-versionen stöddes inte administrativ delegering när du distribuerar konfigurationer som krävde SQL.  Användare som ville installera Azure AD Connect behövde ha behörigheter för serveradministratörer (SA) på SQL-servern.

Med den senaste versionen av Azure AD Connect kan etablering av databasen nu utföras utanför bandet av SQL-administratören och sedan installeras av Azure AD Connect-administratören med databasägarrättigheter.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill använda den här funktionen måste du inse att det finns flera rörliga delar och var och en kan involvera en annan administratör i organisationen.  I följande tabell sammanfattas de enskilda rollerna och deras respektive uppgifter vid distribution av Azure AD Connect med den här funktionen.

|Roll|Beskrivning|
|-----|-----|
|Domän- eller Skogs-AD-administratör|Skapar tjänstkontot på domännivå som används av Azure AD Connect för att köra synkroniseringstjänsten.  Mer information om tjänstkonton finns i [Konton och behörigheter](reference-connect-accounts-permissions.md).
|SQL-administratör|Skapar ADSync-databasen och ger inloggning + dbo åtkomst till Azure AD Connect-administratören och tjänstkontot som skapats av domän-/skogsadministratören.|
Azure AD Connect-administratör|Installerar Azure AD Connect och anger tjänstkontot under anpassad installation.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Steg för installation av Azure AD Connect med SQL-delegerade behörigheter
Så här etablerar du databasen utanför bandet och installerar Azure AD Connect med databasägarebehörigheter gör du följande steg.

>[!NOTE]
>Även om det inte **krävs, rekommenderas** det starkt att Latin1_General_CI_AS sortering väljs när databasen skapas.


1. Låt SQL-administratören skapa ADSync-databasen med en okänslig sorteringssekvens **(Latin1_General_CI_AS)**.  Databasen måste heta **ADSync**.  Återställningsmodellen, kompatibilitetsnivån och inneslutningstypen uppdateras till rätt värden när Azure AD Connect installeras.  Sorteringssekvensen måste dock ställas in korrekt av SQL-administratören annars blockerar Azure AD Connect installationen.  Om du vill återställa säkerhetsassocietyran måste du ta bort och återskapa databasen.
 
   ![Sortering](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Ge Azure AD Connect-administratören och domäntjänstkontot följande behörigheter:
   - SQL-inloggning 
   - **databasägare(dbo)-rättigheter.**
 
   ![Behörigheter](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect stöder inte inloggningar med ett kapslat medlemskap.  Det innebär att ditt Azure AD Connect-administratörskonto och domäntjänstkonto måste vara länkat till en inloggning som beviljas dbo-rättigheter.  Det kan inte bara vara medlem i en grupp som tilldelas en inloggning med dbo rättigheter.

3. Skicka ett e-postmeddelande till Azure AD Connect-administratören som anger SQL-servern och instansnamnet som ska användas vid installation av Azure AD Connect.

## <a name="additional-information"></a>Ytterligare information
När databasen har etablerats kan Azure AD Connect-administratören installera och konfigurera lokal synkronisering när den passar dem.

Om SQL-administratören har återställt ADSync-databasen från en tidigare Azure AD Connect-säkerhetskopia måste du installera den nya Azure AD Connect-servern med hjälp av en befintlig databas. Mer information om hur du installerar Azure AD Connect med en befintlig databas finns i [Installera Azure AD Connect med en befintlig ADSync-databas](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Nästa steg
- [Komma igång med Azure AD Connect med standardinställningar](how-to-connect-install-express.md)
- [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md)
- [Installera Azure AD Connect med en befintlig ADSync-databas](how-to-connect-install-existing-database.md)  
