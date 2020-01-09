---
title: Logga in på Azure Notebooks för hands version
description: Logga in snabbt i Azure Notebooks för hands version och ange ett användar-ID, vilket ger dig möjlighet att komma åt sparade projekt och dela antecknings böcker med andra.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: b6572a7c0b965b2b72916db577b47eff4f1921c0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647024"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Snabb start: Logga in och ange ett användar-ID för Azure Notebooks för hands version

Men du kan alltid visa Azure-datorer utan att logga in, måste du logga in på Kör anteckningsböcker, få åtkomst till sparade projekt och anteckningsböcker och dela dina anteckningsböcker med andra.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="sign-in"></a>Logga in

1. Välj **logga in** uppe till höger i [notebooks.azure.com](https://notebooks.azure.com/).

    ![Platsen för kommandot logga in på Azure-anteckningsböcker](media/accounts/sign-in-command.png)

1. När du uppmanas, anger du e-postadressen till en Account eller ett arbets eller skolkonto och välj **nästa**. Kontotyper beskrivs på [ditt konto för Azure-anteckningsböcker](azure-notebooks-user-account.md). Om du inte har ett Account eller vill göra något speciellt för Azure-datorer, Välj **skapar ett**:

    ![Skapa nytt Microsoft-konto-kommando i inloggningsuppmaning](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Om du försöker skapa ett nytt konto med en e-postadress som redan har ett konto som är associerat med det kan du se meddelandet "du kan inte registrera dig här med en e-postadress till arbetet eller skolan. Använd ett personligt e-postmeddelande, till exempel Gmail eller Yahoo!, eller skaffa ett nytt Outlook-e-postmeddelande. " I så fall kan du försöka logga in med e-postadressen för arbetet utan att skapa ett nytt konto.

1. Ange ditt lösenord när du tillfrågas.

1. Om du loggar in för första gången, frågar Azure anteckningsböcker för behörighet att komma åt ditt konto. Välj **Ja** fortsätta:

    ![Kontot behörighet fråga](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ange ett användar-ID

1. Vid första inloggningen, har du tilldelat en tillfällig användar-ID som ”anon-idrca3”. När du har ett användar-ID som börjar med ”anon-”, Azure anteckningsböcker uppmanas du att skapa ett ID på egen hand. Ditt användar-ID används i valfri URL som du har fått att dela dina projekt och anteckningsböcker, så du väljer något som är unikt och beskrivande.

    ![Uppmanas du för att ange ett användar-ID för Azure-anteckningsböcker](media/accounts/create-user-id.png)

    Om du väljer **Nej tack**, Azure-datorer fortsätter att begära för ett användar-ID varje gång du loggar in. Användaren kan ändra ID när som helst i din [användarprofil](azure-notebooks-user-profile.md).

1. När du har loggat in kan Azure-anteckningsböcker som navigerar till din offentliga profil-sida där du kan välja **redigera profilinformation** att fylla i resten av din information (Mer information finns i [din profil och användar-ID](azure-notebooks-user-profile.md)):

    ![Inledande vyn för en sida för Azure-anteckningsböcker](media/accounts/profile-page-new.png)

> [!NOTE]
> Om du ser meddelandet "användar-ID används redan", prova med ett annat ID. Användar-ID: n är unika för alla Azure Notebooks konton och Azure Notebooks också reserverar vissa användar-ID: n, t. ex. Microsoft varumärkes namn.

## <a name="sign-out"></a>Logga ut

Att logga ut, väljer användarnamnet längst upp till höger på sidan och sedan välja **logga ut**:

![Platsen för utloggning kommando i Azure-anteckningsböcker](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Skapa och dela en anteckningsbok](quickstart-create-share-jupyter-notebook.md)
