---
title: "Referens för rapportering om multifaktorautentisering i Azure Portal | Microsoft Docs"
description: Referensinformation om rapportering om multifaktorautentisering i Azure Portal
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3c676b0c9c8090fb9680bc71051a9247ed0a4574
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Referens för rapportering om multifaktorautentisering i Azure Portal

Med [rapportering i Azure Active Directory (Azure AD)](active-directory-reporting-azure-portal.md) i [Azure Portal](https://portal.azure.com) får du all information du behöver för att ta reda på statusen för din miljö.

[Rapporterna om inloggningsaktiviteter](active-directory-reporting-activity-sign-ins.md) innehåller information om användningen av hanterade program och användarnas inloggningsaktiviteter, som även omfattar information om användning av multifaktorautentisering (MFA). 

MFA-data ger dig information om hur MFA fungerar i din organisation. Du får hjälp att besvara frågor som: 

- Kontrollerades inloggningen med MFA? 

- Hur slutförde användaren MFA? 

- Varför kunde användaren inte slutföra MFA?  

Du kan få bättre förståelse för hur MFA används i din organisation genom att sammanställa alla inloggningsdata. Dessa data hjälper dig att besvara frågor som: 

- Hur många användare kontrolleras av MFA?  

- Hur många användare kan inte slutföra MFA-kontrollen? 

- Vilka vanliga MFA-relaterade problem råkar slutanvändarna ut för? 


Dessa data är tillgängliga via Azure Portal och [API:et för rapportering](active-directory-reporting-api-getting-started-azure-portal.md). 


## <a name="data-structure"></a>Datastruktur


Rapporterna om inloggningsaktiviteter för MFA ger dig åtkomst till följande information:

**MFA krävs:** Om MFA krävs för inloggning eller inte. MFA kan krävas på grund av MFA per användare, villkorlig åtkomst eller av andra orsaker. Möjliga värden är `Yes` eller `No`.

**MFA-autentiseringsmetod:** Autentiseringsmetoden som användaren använde för att slutföra MFA. Möjliga värden: 

- Textmeddelande 

- Meddelanden via mobilapp 

- Telefonsamtal (autentiseringstelefon) 

- Verifieringskod från mobilapp 

- Telefonsamtal (kontorstelefon) 

- Telefonsamtal (alternativ autentiseringstelefon) 

**Information om MFA-autentisering:**: Delvis gömd version av telefonnumret, till exempel: + X XXXXXXXX64. 

**MFA-resultat:** Mer information om huruvida MFA uppfylldes eller nekades:

- Om MFA uppfylldes tillhandahåller den här kolumnen mer information om hur MFA uppfylldes. 

- Om MFA nekades innehåller den här kolumnen orsaken till nekandet. Möjliga värden är `Satisfied` eller `Denied`. 

I följande avsnitt visas möjliga strängvärden för MFA-resultatfältet.

## <a name="status-strings"></a>Statussträngar

Det här avsnittet innehåller möjliga värden för statussträngen för MFA-resultatet.

### <a name="satisfied-status-strings"></a>Uppfyllda statussträngar


- Azure Multi-Factor Authentication

    - slutfört i molnet 

    - har upphört på grund av de principer som konfigurerats på klienten 

    - uppmaning om registrering 

    - uppfyllt av anspråk i token 

    - uppfyllt av anspråk i token 

    - uppfyllt av anspråk i token 

    - uppfyllt av anspråk i token 

    - uppfyllt av anspråk som tillhandahållits av en extern provider 

    - uppfyllt av stark autentisering 

    - hoppades över eftersom flödet som utnyttjades var flödet för inloggning av asynkron Windows-meddelandekö 

    - hoppades över eftersom flödet som utnyttjades var flödet för inloggning av asynkron Windows-meddelandekö 

    - hoppades över på grund av applösenord 

    - hoppades över på grund av plats 

    - hoppades över på grund av en registrerad enhet 
    
    - hoppades över på grund av en sparad enhet 

    - slutfördes 

- Omdirigerad till extern provider för multifaktorautentisering 

 
### <a name="denied-status-strings"></a>Nekade statussträngar

Azure Multi-Factor Authentication nekad; 

- autentisering pågår 

- duplicera autentiseringsförsök 

- angett felaktig kod för många gånger 

- ogiltig autentisering 

- ogiltig verifieringskod från mobilapp 

- felaktig konfiguration 

- telefonsamtalet dirigerades till röstmeddelanden 

- telefonnumret har ett ogiltigt format 

- tjänstfel 

- kunde inte nå användarens telefon 

- kunde inte skicka meddelande via mobilapp till enheten 

- kunde inte skicka meddelande via mobilapp 

- användaren nekade autentiseringen 

- användaren svarade inte på meddelandet via mobilappen 

- användaren har inte några verifieringsmetoder registrerade 

- användaren har angett en felaktig kod 

- användaren har angett en felaktig PIN-kod 

- användaren avslutade telefonsamtalet utan att ha slutfört autentiseringen 

- användaren har blockerats 

- användaren angav aldrig verifieringskoden 

- användaren kunde inte hittas 
 
- verifieringskoden har redan använts en gång 



## <a name="next-steps"></a>Nästa steg

Läs mer i informationen om [Azure Active Directory-rapportering](active-directory-reporting-azure-portal.md).




























