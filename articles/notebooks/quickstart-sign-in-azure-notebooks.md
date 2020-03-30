---
title: Logga in på förhandsversionen av Azure-anteckningsböcker
description: Logga snabbt in på Förhandsversionen av Azure Notebooks och ange ett användar-ID, vilket ger dig möjlighet att komma åt sparade projekt och dela anteckningsböcker med andra.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: b6572a7c0b965b2b72916db577b47eff4f1921c0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75647024"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Snabbstart: Logga in och ange ett användar-ID för förhandsversionen av Azure Notebooks

Även om du alltid kan visa Azure-anteckningsböcker utan att logga in måste du logga in för att köra anteckningsböcker, komma åt sparade projekt och anteckningsböcker och dela dina anteckningsböcker med andra.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="sign-in"></a>Logga in

1. Välj **Logga in** högst upp till höger om [notebooks.azure.com](https://notebooks.azure.com/).

    ![Kommandot Plats för inloggning i Azure-anteckningsböcker](media/accounts/sign-in-command.png)

1. När du uppmanas till det anger du e-postadressen till ett Microsoft-konto eller ett arbets- eller skolkonto och väljer **Nästa**. Kontotyper beskrivs i [ditt användarkonto för Azure Notebooks](azure-notebooks-user-account.md). Om du inte har ett Microsoft-konto eller vill göra ett för användning specifikt med Azure Notebooks väljer du **Skapa ett:**

    ![Skapa nytt Microsoft-kontokommando i inloggningsprompt](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Om du försöker skapa ett nytt konto med en e-postadress som redan har ett konto kopplat till det kan du se meddelandet "Du kan inte registrera dig här med en e-postadress för arbete eller skola. Använd ett personligt e-postmeddelande, till exempel Gmail eller Yahoo!, eller skaffa ett nytt Outlook-e-postmeddelande." Försök i så fall att logga in med e-postadressen för arbetet utan att skapa ett nytt konto.

1. När du uppmanas till det anger du ditt lösenord.

1. Om du loggar in för första gången ber Azure Notebooks om behörighet att komma åt ditt konto. Välj **Ja** om du vill fortsätta:

    ![Frågar om kontobehörigheter](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ange ett användar-ID

1. Vid första inloggningen tilldelas du ett tillfälligt användar-ID som "anon-idrca3". När du har ett användar-ID som börjar med "anon-", uppmanas Du i Azure Notebooks att skapa ett eget ID. Ditt användar-ID används i alla webbadresser som du får för att dela dina projekt och anteckningsböcker, så välj något som är unikt och meningsfullt för dig.

    ![Fråga om att ange ett användar-ID för Azure-anteckningsböcker](media/accounts/create-user-id.png)

    Om du väljer **Inget tack**fortsätter Azure-anteckningsböcker att fråga dig om ett användar-ID varje gång du loggar in. Ditt användar-ID kan också ställas in när som helst i din [användarprofil](azure-notebooks-user-profile.md).

1. När du har loggat in navigerar Azure Notebooks till din offentliga profilsida, där du kan välja **Redigera profilinformation** för att fylla i resten av din information (mer information finns i [Din profil och ditt användar-ID):](azure-notebooks-user-profile.md)

    ![Inledande vy av en profilsida för Azure Notebooks](media/accounts/profile-page-new.png)

> [!NOTE]
> Om meddelandet "Användar-ID redan används" kan du prova ett annat ID. Användar-ID:er är unika i alla Azure Notebooks-konton och Azure Notebooks reserverar även vissa användar-ID:er, till exempel Microsofts varumärken.

## <a name="sign-out"></a>Logga ut

Om du vill logga ut väljer du ditt användarnamn längst upp till höger på sidan och väljer sedan **Logga ut:**

![Plats för utdatakommandot i Azure-anteckningsböcker](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Skapa och dela en anteckningsbok](quickstart-create-share-jupyter-notebook.md)
