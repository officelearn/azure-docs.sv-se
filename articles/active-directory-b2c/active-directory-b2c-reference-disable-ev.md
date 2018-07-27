---
title: Inaktivera e-Postverifiering under konsument registrering i Azure Active Directory B2C | Microsoft Docs
description: Ett avsnitt som visar hur du inaktiverar e-Postverifiering under konsument registrering i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e008fb87b57b92f8f7e914e6b4344b52d42f9ef8
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263937"
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C: Inaktivera e-Postverifiering under konsument registrering
När aktiverat kan Azure Active Directory (Azure AD) B2C en konsument att registrera dig för program genom att tillhandahålla en e-postadress och skapa ett lokalt konto. Azure AD B2C säkerställer giltiga e-postadresser genom att kräva att konsumenterna kan kontrollera dem när du registrerade dig. Det förhindrar också en skadlig automatiserad process genererar falska konton för programmen.

Vissa programutvecklare föredrar att hoppa över e-Postverifiering när du registrerade dig och i stället har konsumenter som kontrollerar e-postadressen senare. Azure AD B2C kan konfigureras för att inaktivera e-Postverifiering för detta ändamål. Detta skapar en jämnare registreringsprocessen och ger utvecklare möjlighet att skilja konsumenter som har verifierat e-postadressen från dessa konsumenter som inte har.

Som standard har registreringsprinciper e-Postverifiering aktiveras. Använd följande steg för att stänga av den:

1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **registreringsprinciper** eller **registrerings-eller logga in** beroende på vad du har konfigurerat för registrering.
3. Klicka på principen (till exempel ”B2C_1_SiUp”) för att öppna den. 
4. Klicka på **redigera** överst på bladet.
5. Klicka på **anpassning av SID-UI**.
6. Klicka på **registreringssida för lokalt konto**.
7. Klicka på **e-postadress** i den **namn** kolumnen under den **registreringsattribut** avsnittet.
8. Visa/Dölj de **Kräv verifiering** alternativet att **nr**.
9. Klicka på **OK** längst ned på sidan tills du når den **redigera principen** bladet.
10. Klicka på **spara** överst på bladet. Du är klar!

> [!NOTE]
> Inaktivera e-Postverifiering i registreringsprocessen kan leda till att skicka meddelanden. Om du inaktiverar standardvärdet, rekommenderar vi att lägga till egna verifieringssystem.
> 
> 

Vi är alltid öppen för feedback och förslag! Om du har problem med det här avsnittet eller har rekommendationer för att förbättra det här innehållet, skulle vi uppskattar din feedback längst ned på sidan. Lägga till dem i för funktionsförfrågningar om [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
