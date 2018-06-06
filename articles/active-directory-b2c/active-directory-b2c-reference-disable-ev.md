---
title: Inaktivera e-Postverifiering under registrering i Azure Active Directory B2C konsumenten | Microsoft Docs
description: Ett avsnitt som visar hur du inaktiverar e-Postverifiering under konsumenten registrering i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 24242054ea4af3797fbeca1ed48bb698e4f4296b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712019"
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
