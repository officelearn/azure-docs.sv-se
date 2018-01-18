---
title: "Inaktivera emain verifiering under konsumenten anmälan - Azure Active Directory B2C"
description: Ett avsnitt som visar hur du inaktiverar e-Postverifiering under konsumenten registrering i Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 433f32b8-96d2-4113-aa82-efcf42fa9827
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/06/2017
ms.author: parakhj
ms.custom: seohack1
ms.openlocfilehash: 57da51fafbac8a1c165c37437e82c75cb238fd3d
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C: Inaktivera e-Postverifiering under konsumenten registrering
När aktiverat, kan Azure Active Directory (AD Azure) B2C en konsument registrera dig för program genom att tillhandahålla en e-postadress och skapa ett lokalt konto. Azure AD B2C garanterar giltiga e-postadresser genom att kräva att konsumenterna kan kontrollera dem under registreringen. Det förhindrar också en skadlig automatiserad process genererar falska konton för programmen.

Vissa programutvecklare föredrar att hoppa över e-Postverifiering under registreringen och i stället har konsumenter kontrollera e-postadressen senare. Stöd för detta kan Azure AD B2C konfigureras för att inaktivera e-verifiering. Gör detta skapar en jämnare registreringsprocessen och ger utvecklare möjlighet att skilja användare som har verifierats sina e-postadress från dessa användare inte har.

Principer för registrering har e-Postverifiering aktiverad som standard. Använd följande steg för att stänga av den:

1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **principer för registrering** eller **principer för registrering eller inloggning** beroende på vad du har konfigurerat för registrering.
3. Klicka på principen (till exempel ”B2C_1_SiUp”) för att öppna den. Klicka på **redigera** längst upp på bladet.
4. Klicka på **sidan anpassningar**.
5. Klicka på **registreringssidan för lokalt konto**.
6. Klicka på **e-postadress** i den **namn** kolumnen under den **registreringsattribut** avsnitt.
7. Växla den **Kräv verifiering** att **nr**.
8. Klicka på **OK** längst ned tills du når den **Redigera princip** bladet.
9. Klicka på **spara** längst upp på bladet. Du är klar!

> [!NOTE]
> Inaktivera e-Postverifiering i registreringsprocessen kan leda till att skicka meddelanden. Om du inaktiverar standardversionen rekommenderar vi att lägga till egna verifieringssystem.
> 
> 

Vi är alltid öppna för feedback och förslag! Om du har problem med det här avsnittet eller rekommendationer för att förbättra det här innehållet, skulle vi uppskattar din feedback längst ned på sidan. För funktionsbegäranden, lägga till dem i [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).