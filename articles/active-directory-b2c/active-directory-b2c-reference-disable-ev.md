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
ms.openlocfilehash: e36dd19aa020b8cb2a623cda904cf7fa8a0b26da
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004608"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Inaktivera e-Postverifiering under konsument registrering i Azure Active Directory B2C 
När aktiverat kan Azure Active Directory (Azure AD) B2C en konsument att registrera dig för program genom att tillhandahålla en e-postadress och skapa ett lokalt konto. Azure AD B2C säkerställer giltiga e-postadresser genom att kräva att konsumenterna kan kontrollera dem när du registrerade dig. Det förhindrar också en skadlig automatiserad process genererar falska konton för programmen.

Vissa programutvecklare föredrar att hoppa över e-Postverifiering när du registrerade dig och i stället har konsumenter som kontrollerar e-postadressen senare. Azure AD B2C kan konfigureras för att inaktivera e-Postverifiering för detta ändamål. Detta skapar en jämnare registreringsprocessen och ger utvecklare möjlighet att skilja konsumenter som har verifierat e-postadressen från dessa konsumenter som inte har.

Som standard har registreringsprinciper e-Postverifiering aktiveras. Använd följande steg för att stänga av den:

1. Klicka på **registreringsprinciper** eller **registrerings-eller logga in** beroende på vad du har konfigurerat för registrering.
2. Klicka på principen (till exempel ”B2C_1_SiUp”) för att öppna den. 
3. Klicka på **redigera** överst på bladet.
4. Klicka på **anpassning av SID-UI**.
5. Klicka på **registreringssida för lokalt konto**.
6. Klicka på **e-postadress** i den **namn** kolumnen under den **registreringsattribut** avsnittet.
7. Visa/Dölj de **Kräv verifiering** alternativet att **nr**.
8. Klicka på **OK** längst ned på sidan tills du når den **redigera principen** bladet.
9. Klicka på **Spara** överst på bladet. Du är klar!

> [!NOTE]
> Inaktivera e-Postverifiering i registreringsprocessen kan leda till att skicka meddelanden. Om du inaktiverar standardvärdet, rekommenderar vi att lägga till egna verifieringssystem.
> 
>