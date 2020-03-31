---
title: Lägga till en användare som labbskapare i Azure Lab Services
description: Den här artikeln visar hur du lägger till en användare i rollen Lab Creator för ett labbkonto i Azure Lab Services. Labbskaparna kan skapa labb i det här labbkontot.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444776"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Lägga till labbskapare i ett labbkonto i Azure Lab Services
Den här artikeln visar hur du lägger till användare som labbskapare i ett labbkonto i Azure Lab Services. Dessa användningsområden kan sedan skapa klassrumslabb i labbkontot. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Lägga till Microsoft-användarkonto i rollen Lab Creator
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste användaren vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa labbkontot läggs automatiskt till i den här rollen. Om du planerar att använda samma användarkonto för att skapa ett klassrumslabb kan du hoppa över det här steget. Om du vill använda ett annat konto för att skapa ett klassrumslabb gör du följande: 

Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen **Labbskapare**:

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till rolltilldelning** i verktygsfältet. 

    ![Åtkomstkontroll -> Lägg till rolltilldelning, knapp](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. På sidan **Lägg till rolltilldelning** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägg till labbuppgiftsförfattare](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Om du lägger till en användare som inte är Microsoft-konto som labbskapare läser du avsnittet [Lägg till en användare som inte kommer från Microsoft-kontot som labbskapare.](#add-a-non-microsoft-account-user-as-a-lab-creator) 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Lägga till en användare som inte kommer från Microsoft-kontot som labbskapare
Om du vill lägga till en användare som labbskapare använder du deras e-postkonton. Följande typer av e-postkonton kan användas:

- Ett e-postkonto som tillhandahålls av universitetets Office 365 Azure Active Directory (AAD). 
- Ett Microsoft-e-postkonto, `@hotmail.com` `@msn.com`till `@live.com`exempel `@outlook.com`, , eller .
- Ett e-postkonto som inte kommer från Microsoft, till exempel ett som tillhandahålls av Yahoo eller Google. Dessa typer av konton måste dock vara kopplade till ett Microsoft-konto.
- Ett GitHub-konto. Det här kontot måste vara länkat till ett Microsoft-konto.

### <a name="using-a-non-microsoft-email-account"></a>Använda ett e-postkonto som inte kommer från Microsoft
Labbskapare/lärare kan använda e-postkonton som inte kommer från Microsoft för att registrera sig och logga in på ett klassrumslabb.  Inloggningen till Lab Services-portalen kräver dock att lärare först skapar ett Microsoft-konto som är länkat till deras e-postadress som inte kommer från Microsoft.

Många lärare kanske redan har ett Microsoft-konto kopplat till sina e-postadresser som inte kommer från Microsoft. Lärare har till exempel redan ett Microsoft-konto om de har använt sin e-postadress med Microsofts andra produkter eller tjänster, till exempel Office, Skype, OneDrive eller Windows.  

När lärare loggar in på Lab Services-portalen uppmanas de att ange sin e-postadress och sitt lösenord. Om läraren försöker logga in med ett konto som inte är Microsoft-konto kopplat får läraren följande felmeddelande: 

![Felmeddelande](../media/how-to-configure-student-usage/cant-find-account.png)

Om du vill registrera dig för ett [http://signup.live.com](http://signup.live.com)Microsoft-konto bör lärare gå till .  


### <a name="using-a-github-account"></a>Använda ett GitHub-konto
Lärare kan också använda ett befintligt GitHub-konto för att registrera sig och logga in på ett klassrumslabb. Om läraren redan har ett Microsoft-konto kopplat till sitt GitHub-konto kan de logga in och ange sitt lösenord enligt föregående avsnitt. Om de ännu inte har länkat sitt GitHub-konto till ett Microsoft-konto bör de välja **inloggningsalternativ:**

![Länken Inloggningsalternativ](../media/how-to-configure-student-usage/signin-options.png)

På sidan **Inloggningsalternativ** väljer du **Logga in med GitHub**.

![Logga in med GitHub-länken](../media/how-to-configure-student-usage/signin-github.png)

Slutligen uppmanas de att skapa ett Microsoft-konto som är kopplat till deras GitHub-konto. Det sker automatiskt när läraren väljer **Nästa**.  Instruktören loggas sedan omedelbart in och ansluts till klassrumslabbet.


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labbanvändare får du tillgång till klassrumslabb](how-to-use-classroom-lab.md)
