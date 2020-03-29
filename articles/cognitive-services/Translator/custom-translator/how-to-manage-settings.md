---
title: Hur hanterar man inställningar? - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: Hantera inställningar, skapa arbetsyta, dela arbetsyta och hantera prenumerationsnyckel i Anpassad översättare.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219515"
---
# <a name="how-to-manage-settings"></a>Så här hanterar du inställningar

På sidan Anpassade översättares inställningar kan du skapa en ny arbetsyta, dela arbetsytan och lägga till eller ändra prenumerationsnyckeln för Microsoft Translation.

Så här öppnar du inställningssidan:

1. Logga in på portalen [Anpassad översättare.](https://portal.customtranslator.azure.ai/)
2. På Custom Translator portal, klicka på växelikonen i sidofältet.

    ![Ange länk](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Associera Microsoft Translator-prenumeration

Du måste ha en prenumerationsnyckel för Microsoft Translator Text API som är associerad med arbetsytan för att träna eller distribuera modeller.

Om du inte har någon prenumeration följer du stegen nedan:

1. Prenumerera på Microsoft Translator Text API. I den här artikeln visas hur du prenumererar på Microsoft Translator Text API.
2. Notera nyckeln för din översättareprenumeration. Någon av Key1 eller Key2 är acceptabla.
3. Navigera tillbaka till portalen För anpassad översättare.

### <a name="add-existing-key"></a>Lägg till befintlig nyckel

1.  Navigera till sidan "Inställningar" för arbetsytan.
2.  Klicka på Lägg till tangent

    ![Så här lägger du till prenumerationsnyckel](media/how-to/how-to-add-subscription-key.png)

3. I dialogrutan anger du nyckeln för din översättareprenumeration och klickar sedan på knappen "Lägg till".

    ![Så här lägger du till prenumerationsnyckel](media/how-to/how-to-add-subscription-key-dialog.png)
4.  När du har lagt till en nyckel kan du ändra eller ta bort nyckeln när som helst.

    ![Prenumerationsnyckel efter tillägg](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Hantera din arbetsyta

En arbetsyta är en arbetsyta för att komponera och bygga ditt anpassade översättningssystem. En arbetsyta kan innehålla flera projekt, modeller och dokument.

Om olika delar av ditt arbete behöver delas med olika personer kan det vara användbart att skapa flera arbetsytor.

## <a name="create-a-new-workspace"></a>Skapa en ny arbetsyta

1.  Navigera till sidan "Inställningar" på arbetsytan.
2.  Klicka på knappen "Ny arbetsyta" i avsnittet "Skapa ny arbetsyta".

    ![Skapa ny arbetsyta](media/how-to/create-new-workspace.png)

4.  Ange namnet på den nya arbetsytan i dialogrutan.
5.  Klicka på "Skapa".

    ![Skapa en ny arbetsyta](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Dela arbetsytan

I Anpassad översättare kan du dela din arbetsyta med andra om olika delar av ditt arbete behöver delas med olika personer.

1.  Navigera till sidan "Inställningar" på arbetsytan.
2.  Klicka på knappen Dela i avsnittet "Delningsinställningar".

    ![Dela arbetsyta](media/how-to/share-workspace.png)

3.  I dialogrutan anger du en kommaavgränsad lista över e-postadresser som du vill att arbetsytan ska delas med. Se till att du delar med den e-postadress som personen använder för att logga in på Anpassad översättare med. Välj sedan lämplig delningsbehörighet.

4.  Om arbetsytan fortfarande har standardnamnet "Min arbetsyta" måste du ändra den innan arbetsytan delas.
5.  Klicka på "Spara".

## <a name="sharing-permissions"></a>Dela behörigheter

1.  **Läsare:** En läsare på arbetsytan kan visa all information på arbetsytan.

2.  **Redaktör:** En redigerare på arbetsytan kan lägga till dokument, träna modeller och ta bort dokument och projekt. De kan lägga till en prenumerationsnyckel, men de kan inte ändra vem arbetsytan delas med, ta bort arbetsytan eller ändra arbetsytans namn.

3.  **Ägare:** En ägare har fullständig behörighet till arbetsytan.

## <a name="change-sharing-permission"></a>Ändra behörighet för delning

När en arbetsyta delas visas alla e-postadresser som arbetsytan delas med i avsnittet "Delningsinställningar". Du kan ändra befintlig delningsbehörighet för varje e-postadress om du har ägaråtkomst till arbetsytan.

1.  I avsnittet "Delningsinställningar" för varje e-postmeddelande visar en rullgardinsmeny den aktuella behörighetsnivån.

2.  Klicka på den nedrullningsbara menyn och välj den nya behörighetsnivå som du vill tilldela den e-postadressen.

    ![Behörighetsinställningar för delning](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [migrerar arbetsytan och projektet](how-to-migrate.md) från [Microsoft Translator Hub](https://hub.microsofttranslator.com)
