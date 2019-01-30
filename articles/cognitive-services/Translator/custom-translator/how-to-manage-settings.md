---
title: Hur du hanterar inställningar? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: Så här att hantera inställningar, skapa arbetsyta, dela arbetsyta och hantera prenumerationsnyckel i anpassade Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 6fabbb57538f5e10e6cd91ebc109707045e54033
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227088"
---
# <a name="how-to-manage-settings"></a>Hantera inställningar

I sidan anpassade Translator-inställningar kan du skapa en ny arbetsyta, dela din arbetsyta och lägga till eller ändra din prenumerationsnyckel för Microsoft Translation.

Öppna inställningssidan:

1. Logga in på den [anpassad Translator](https://portal.customtranslator.azure.ai/) portal.
2. Klicka på kugghjulsikonen i sidopanelen på anpassade Translator-portalen.

    ![Ange länken](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Associera Microsoft Translator-prenumeration

Du måste ha en Microsoft Translator Text API-prenumerationsnyckel som är associerade med din arbetsyta för att träna eller distribuera modeller.

Om du inte har en prenumeration, följer du stegen nedan:

1. Prenumerera på Microsoft Translator Text API. Den här artikeln visar hur du prenumerera på Microsoft Translator Text API.
2. Skriv upp nyckeln för din prenumeration av translator. Något av Key1 och Key2 accepteras.
3. Gå tillbaka till den anpassade Translator-portalen.

### <a name="add-existing-key"></a>Lägg till befintlig nyckel

1.  Gå till sidan ”Inställningar” för arbetsytan.
2.  Klicka på Lägg till nyckel

    ![Hur du lägger till prenumerationsnyckel](media/how-to/how-to-add-subscription-key.png)

3. I dialogrutan Ange nyckel för din prenumeration av translator, och klicka på knappen ”Lägg till”.
 
    ![Hur du lägger till prenumerationsnyckel](media/how-to/how-to-add-subscription-key-dialog.png)
4.  När du har lagt till en nyckel, kan du ändra eller ta bort nyckeln när som helst.

    ![Lägg till prenumerationsnyckel](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Hantera din arbetsyta

En arbetsyta är en arbetsyta för att skapa och skapa dina anpassade översättningssystemet. En arbetsyta kan innehålla flera projekt, modeller och dokument. 

Om en annan del av ditt arbete måste delas med olika personer, kan det vara praktiskt att skapa flera arbetsytor. 

## <a name="create-a-new-workspace"></a>Skapa en ny arbetsyta

1.  Gå till sidan för arbetsytan ”inställningar”.
2.  Klicka på ”ny arbetsyta” i avsnittet ”Skapa ny arbetsyta”.
    
    ![Skapa ny arbetsyta](media/how-to/create-new-workspace.png)

4.  I dialogrutan anger du namnet på den nya arbetsytan.
5.  Klicka på ”Skapa”.
    
    ![Skapa ny arbetsyta dialog](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Dela din arbetsyta

I anpassade Translator kan du dela din arbetsyta med andra, om en annan del av ditt arbete måste delas med olika personer. 

1.  Gå till sidan för arbetsytan ”inställningar”.
2.  Klicka på knappen ”Dela” i avsnittet ”Dela inställningar”.
    
    ![Dela arbetsyta](media/how-to/share-workspace.png)

3.  I dialogrutan, ange en kommaavgränsad lista över e-postadresser som du vill att den här arbetsytan som delas med. Kontrollera att du delar med den e-postadressen som personen använder för att logga in till anpassad Translator med. Välj en lämplig nivå av behörighet.

4.  Om din arbetsyta har fortfarande standardnamnet ”Min arbetsyta”, kommer du att behöva ändra den innan du delar din arbetsyta.
5.  Klicka på ”Spara”.

## <a name="sharing-permissions"></a>Delningsbehörigheter

1.  **Läsare:** En läsare i arbetsytan kommer att kunna se all information på arbetsytan. 

2.  **Redigerare:** Redigerare i arbetsytan kommer att kunna lägga till dokument, skapa modeller och ta bort dokument och projekt. De kan lägga till en prenumerationsnyckel men det går inte att ändra vem arbetsytan delas med, ta bort arbetsytan eller ändra namnet på arbetsytan.

3.  **Ägare:** En ägare har fullständiga behörigheter till arbetsytan.

## <a name="change-sharing-permission"></a>Ändra delning behörighet

När en arbetsyta delas visar ”dela” avsnittet alla e-postadresser som delas med den här arbetsytan. Du kan ändra befintliga delning behörigheten för varje e-postadress om du har ägaråtkomst till arbetsytan.

1.  I avsnittet ”Dela inställningar” för varje e-postmeddelande visas en listruta aktuella behörighetsnivå.

2.  Klicka på den nedrullningsbara menyn och välj den nya behörighetsnivå som du vill tilldela till den e-postadressen.

    ![Behörighetsinställningar för delning](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du migrerar din arbetsyta och projekt](how-to-migrate.md) från [Microsoft Translator Hub](https://hub.microsofttranslator.com)
