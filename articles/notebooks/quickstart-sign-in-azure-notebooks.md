---
title: Logga in på Azure-anteckningsböcker
description: Snabbt logga in på Azure-anteckningsböcker och ange ett användar-ID, vilket ger dig möjlighet att komma åt sparade projekt och dela anteckningsböcker med andra.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: 234703da9662380a59c7673ca3b34a1b01b7dbef
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277445"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Snabbstart: Logga in och ange ett användar-ID

Men du kan alltid visa Azure-datorer utan att logga in, måste du logga in på Kör anteckningsböcker, få åtkomst till sparade projekt och anteckningsböcker och dela dina anteckningsböcker med andra.

## <a name="sign-in"></a>Logga in

1. Välj **Logga** in längst upp till höger i [Notebooks.Azure.com](https://notebooks.azure.com/).

    ![Platsen för kommandot logga in på Azure-anteckningsböcker](media/accounts/sign-in-command.png)

1. När du uppmanas till det anger du e-postadressen till ett Microsoft-konto eller ett arbets-eller skol konto och väljer **Nästa**. Konto typer beskrivs i [ditt användar konto för Azure Notebooks](azure-notebooks-user-account.md). Om du inte har ett Microsoft-konto eller om du vill göra ett för användning särskilt med Azure Notebooks väljer du **skapa ett**:

    ![Skapa nytt Microsoft-konto-kommando i inloggningsuppmaning](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Om du försöker skapa ett nytt konto med en e-postadress som redan har ett konto som är associerat med det kan du se meddelandet "du kan inte registrera dig här med en e-postadress till arbetet eller skolan. Använd ett personligt e-postmeddelande, till exempel Gmail eller Yahoo!, eller skaffa ett nytt Outlook-e-postmeddelande. " I så fall kan du försöka logga in med e-postadressen för arbetet utan att skapa ett nytt konto.

1. Ange ditt lösenord när du tillfrågas.

1. Om du loggar in för första gången, frågar Azure anteckningsböcker för behörighet att komma åt ditt konto. Välj **Ja** om du vill fortsätta:

    ![Kontot behörighet fråga](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ange ett användar-ID

1. Vid första inloggningen, har du tilldelat en tillfällig användar-ID som ”anon-idrca3”. När du har ett användar-ID som börjar med ”anon-”, Azure anteckningsböcker uppmanas du att skapa ett ID på egen hand. Ditt användar-ID används i valfri URL som du har fått att dela dina projekt och anteckningsböcker, så du väljer något som är unikt och beskrivande.

    ![Uppmanas du för att ange ett användar-ID för Azure-anteckningsböcker](media/accounts/create-user-id.png)

    Om du väljer **Nej tack**fortsätter Azure Notebooks att uppmana dig att ange ett användar-ID varje gången du loggar in. Ditt användar-ID kan också ställas in när som helst i din [användar profil](azure-notebooks-user-profile.md).

1. När du har loggat in går Azure Notebooks till din offentliga profil sida där du kan välja **Redigera profil information** för att fylla i resten av din information (mer information finns i [din profil och ditt användar-ID](azure-notebooks-user-profile.md)):

    ![Inledande vyn för en sida för Azure-anteckningsböcker](media/accounts/profile-page-new.png)

> [!NOTE]
> Om du ser meddelandet "användar-ID används redan", prova med ett annat ID. Användar-ID: n är unika för alla Azure Notebooks konton och Azure Notebooks också reserverar vissa användar-ID: n, t. ex. Microsoft varumärkes namn.

## <a name="sign-out"></a>Logga ut

Om du vill logga ut väljer du ditt användar namn längst upp till höger på sidan och väljer sedan **Logga ut**:

![Platsen för utloggning kommando i Azure-anteckningsböcker](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: skapa och dela en bärbar dator](quickstart-create-share-jupyter-notebook.md)
