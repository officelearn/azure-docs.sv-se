---
title: Hur hanterar jag inställningar? – Anpassad översättare
titleSuffix: Azure Cognitive Services
description: Hantera inställningar, skapa arbets yta, dela arbets yta och hantera prenumerations nyckel i anpassad översättare.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 50bff8e55d1b125f56696ac485bc1e05193dea55
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992954"
---
# <a name="how-to-manage-settings"></a>Hantera inställningar

På sidan Inställningar för anpassad översättare kan du skapa en ny arbets yta, dela din arbets yta och lägga till eller ändra din prenumerations nyckel för Microsoft-översättning.

Så här öppnar du sidan inställningar:

1. Logga in på den [anpassade översättnings](https://portal.customtranslator.azure.ai/) portalen.
2. På den anpassade översättnings portalen klickar du på kugg hjuls ikonen på sido panelen.

    ![Inställnings länk](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Kopplar Microsoft Translator-prenumeration

Du måste ha en prenumerations nyckel för översättare kopplad till din arbets yta för att träna eller distribuera modeller.

Om du inte har någon prenumeration följer du stegen nedan:

1. Prenumerera på Translator. Den här artikeln visar hur du prenumererar på Translator.
2. Notera nyckeln för din Translator-prenumeration. Antingen KEY1 eller Key2 är acceptabla.
3. Gå tillbaka till den anpassade översättnings portalen.

### <a name="add-existing-key"></a>Lägg till befintlig nyckel

1.    Gå till sidan Inställningar för din arbets yta.
2.    Klicka på Lägg till nyckel

   ![Så här lägger du till prenumerations nyckel](media/how-to/how-to-add-subscription-key.png)

3. I dialog rutan anger du nyckeln för din Translator-prenumeration och klickar sedan på knappen Lägg till.

    ![Så här lägger du till prenumerations nyckel](media/how-to/how-to-add-subscription-key-dialog.png)
4.    När du har lagt till en nyckel kan du ändra eller ta bort nyckeln när du vill.

   ![Prenumerations nyckel efter tillägg](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Hantera din arbetsyta

En arbets yta är en arbets yta för att skriva och skapa ett anpassat översättnings system. En arbets yta kan innehålla flera projekt, modeller och dokument.

Om olika delar av arbetet måste delas med olika personer kan det vara praktiskt att skapa flera arbets ytor.

## <a name="create-a-new-workspace"></a>Skapa en ny arbetsyta

1.    Gå till sidan Inställningar för arbets yta.
2.    Klicka på knappen "ny arbets yta" i avsnittet "Skapa ny arbets yta".

   ![Skapa ny arbets yta](media/how-to/create-new-workspace.png)

4.    I dialog rutan anger du namnet på den nya arbets ytan.
5.    Klicka på "skapa".

   ![Dialog rutan skapa ny arbets yta](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Dela din arbets yta

I en anpassad översättare kan du dela din arbets yta med andra, om olika delar av arbetet måste delas med olika personer.

1.    Gå till sidan Inställningar för arbets yta.
2.    Klicka på knappen Dela i avsnittet delnings inställningar.

   ![Dela arbets yta](media/how-to/share-workspace.png)

3.    I dialog rutan anger du en kommaavgränsad lista med e-postadresser som du vill att den här arbets ytan ska delas med. Se till att du delar med den e-postadress som personen använder för att logga in på anpassad översättare med. Välj sedan lämplig nivå för delnings behörighet.

4.    Om arbets ytan fortfarande har standard namnet "min arbets yta" måste du ändra den innan du delar din arbets yta.
5.    Klicka på Spara.

## <a name="sharing-permissions"></a>Delnings behörigheter

1.    **Läsare:** En läsare på arbets ytan kommer att kunna visa all information på arbets ytan.

2.    **Redigerare:** En redigerare i arbets ytan kommer att kunna lägga till dokument, träna modeller och ta bort dokument och projekt. De kan lägga till en prenumerations nyckel, men du kan inte ändra vilken arbets yta som delas med, ta bort arbets ytan eller ändra arbets ytans namn.

3.    **Ägare:** En ägare har fullständig behörighet till arbets ytan.

## <a name="change-sharing-permission"></a>Ändra delnings behörighet

När en arbets yta delas visas alla e-postadresser som arbets ytan delas med i avsnittet delnings inställningar. Du kan ändra befintliga delnings behörigheter för varje e-postadress om du har ägar åtkomst till arbets ytan.

1.    I avsnittet delnings inställningar för varje e-postmeny visas den aktuella behörighets nivån i list rutan för varje e-postmeddelande.

2.    Klicka på list menyn och välj den nya behörighets nivå som du vill tilldela den e-postadressen.

   ![Delnings behörighets inställningar](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du migrerar din arbets yta och ditt projekt](how-to-migrate.md) från [Microsoft Translator Hub](https://hub.microsofttranslator.com)
