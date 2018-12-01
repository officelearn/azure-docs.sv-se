---
title: Inaktivera e-Postverifiering under konsument registrering i Azure Active Directory B2C | Microsoft Docs
description: Ett avsnitt som visar hur du inaktiverar e-Postverifiering under konsument registrering i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0a03be96895ea88ae033fd56df861b985317295b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52719206"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Inaktivera e-Postverifiering under konsument registrering i Azure Active Directory B2C 
När aktiverat kan Azure Active Directory (Azure AD) B2C en konsument att registrera dig för program genom att tillhandahålla en e-postadress och skapa ett lokalt konto. Azure AD B2C säkerställer giltiga e-postadresser genom att kräva att konsumenterna kan kontrollera dem när du registrerade dig. Det förhindrar också en skadlig automatiserad process genererar falska konton för programmen.

Vissa programutvecklare föredrar att hoppa över e-Postverifiering när du registrerade dig och i stället har konsumenter som kontrollerar e-postadressen senare. Azure AD B2C kan konfigureras för att inaktivera e-Postverifiering för detta ändamål. Detta skapar en jämnare registreringsprocessen och ger utvecklare möjlighet att skilja konsumenter som har verifierat e-postadressen från dessa konsumenter som inte har.

Som standard har registrering användarflöden e-Postverifiering aktiveras. Använd följande steg för att stänga av den:

1. Klicka på **användarflöden**.
2. Klicka på ditt användarflöde (till exempel ”B2C_1_SiUp”) för att öppna den. 
3. Klicka på **sidan layouter**.
4. Klicka på **registreringssida för lokalt konto**.
5. Klicka på **e-postadress** i den **namn** kolumnen under den **användarattribut** avsnittet.
6. Under **kräver verifiering**väljer **nr**.
7. Klicka på **Spara** överst på bladet. Du är klar!

> [!NOTE]
> Inaktivera e-Postverifiering i registreringsprocessen kan leda till att skicka meddelanden. Om du inaktiverar standardvärdet, rekommenderar vi att lägga till egna verifieringssystem.
> 
>