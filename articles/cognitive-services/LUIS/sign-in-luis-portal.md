---
title: Logga in på LUIS-portalen
description: Om du är en ny användare som loggar in på LUIS-portalen, kommer inloggningen att skilja sig något beroende på ditt aktuella användar konto.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: ae51dca466a9aaf489ba4628e13a5e13de25b9bc
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546870"
---
# <a name="sign-in-to-luis-portal"></a>Logga in på LUIS-portalen

Om du är en ny användare som loggar in på LUIS-portalen, kommer inloggningen att skilja sig något beroende på ditt aktuella användar konto:
  * Associerat med en Azure-prenumeration
  * Inte associerat med en Azure-prenumeration

## <a name="determine-account-type"></a>Ange kontotyp

När du först loggar in på LUIS-portalen använder du följande visuella indikatorer för att fastställa konto typen.

### <a name="account-without-azure-subscription"></a>Konto utan Azure-prenumeration

Ett konto som inte är associerat med en Azure-prenumeration har Azure-ikonen i det övre högra navigerings fältet. När du migrerar till den associerade konto typen visas ikonen inte längre.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Partiell skärm bild som visar LUIS navigerings fält med Azure-ikonen.":::

### <a name="account-with-azure-subscription"></a>Konto med Azure-prenumeration

Med ett konto som är associerat med en Azure-prenumeration kan du välja vilken prenumeration och resurs som ska användas.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Delvis skärm bild som visar LUIS-portalen med list rutorna för att skapa och redigera resurs val.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Logga in med ett konto som är associerat med en Azure-prenumeration

1. Logga in på [Luis-portalen](https://www.luis.ai) och godkänn användnings villkoren.

1. Du har två alternativ för att registrera dig:

    * Fortsätt att använda en Azure-resurs, vilket är den rekommenderade sökvägen och snart är den enda tillgängliga sökvägen. Med den här sökvägen kan du länka ditt LUIS-konto till en Azure Authoring-resurs när du registrerar dig genom att välja en befintlig resurs i din prenumeration eller skapa en ny resurs. Detta motsvarar att logga in migrerat utan att behöva göra [migreringen](luis-migration-authoring.md#what-is-migration) senare. Alla användare kommer att behöva migreras senast den 2 november 2020.

    * Fortsätt att använda start-eller utvärderings nyckeln. Med den här sökvägen kan du logga in på LUIS med start-eller utvärderings resursen som tillhandahålls utan att behöva skapa några resurser. Om du väljer den här sökvägen måste du eventuellt [migrera ditt konto](luis-migration-authoring.md#migration-steps) och länka dina program till en redigerings resurs. Därför rekommenderar vi att du väljer den sökväg där du fortsätter med din Azure-resurs.

    [Lär dig mer om redigerings-och start nycklar](luis-how-to-azure-subscription.md#luis-resources). Båda resurserna ger dig 1 000 000 kostnads fria redigerings transaktioner och 1000 kostnads fria förutsägelse slut transaktioner.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Partiell skärm bild för att välja en typ av Language Understanding du redigerar resurs.":::

1. Använd en befintlig redigerings resurs

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Välj redigerings resurs":::

    Om du redan har LUIS att skriva resurser i din prenumeration och associera ett med ditt LUIS-konto under inloggningen, väljer du alternativet **Använd befintlig redigerings resurs** och anger följande information:

    * **Klient** organisation – klienten som din Azure-prenumeration är associerad med. Du kommer inte att kunna växla klienter från det befintliga fönstret. Du kan byta innehavare genom att välja den högra avataren, som innehåller dina initialer i det översta fältet.
    * **Prenumerations namn** – den prenumeration som ska associeras med resursen. Om du har mer än en prenumeration som tillhör din klient väljer du den som du vill använda i list rutan.
    * **Resurs namn** – den redigerings resurs som du vill att kontot ska kopplas till.

    > [!Note]
    > Om den redigerings resurs som du söker är nedtonad i list rutan innebär det att du har loggat in på en annan regional Portal. [Förstå begreppet region portaler](luis-reference-regions.md#luis-authoring-regions).

1. Skapa en ny redigerings resurs

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Skapa en redigerings resurs":::

    När du **skapar en ny redigerings resurs** anger du följande information:

    * **Klient** organisation – klienten som din Azure-prenumeration är associerad med. Du kommer inte att kunna växla klienter från det befintliga fönstret. Du kan byta innehavare genom att välja den högra avataren, som innehåller dina initialer i det översta fältet.
    * **Resurs namn** – ett anpassat namn som du väljer, används som en del av URL: en för dina redigerings transaktioner. Resurs namnet får bara innehålla alfanumeriska tecken,-och får inte börja eller sluta med-. Om något annat kortnamn ingår i namnet kommer det inte att gå att skapa en resurs.
    * **Prenumerations namn** – den prenumeration som ska associeras med resursen. Om du har mer än en prenumeration som tillhör din klient väljer du den som du vill använda i list rutan.
    * **Resurs grupp** – ett namn på en anpassad resurs grupp som du väljer i din prenumeration. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering. Om du för närvarande inte har en resurs grupp i din prenumeration kommer du inte att kunna skapa en i LUIS-portalen. Gå till [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) för att skapa en och gå sedan till Luis för att fortsätta inloggnings processen.

1. När du har valt din sökväg kan det ta några sekunder innan tecknet "ditt konto har migrerats" visas. Slutför genom att välja **Fortsätt**.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Bekräfta redigering av resurs":::

    > [!Note]
    > Om du har en prenumeration och minst en redigerings resurs i den region som du registrerar dig på i portalen, kan du logga in automatiskt till LUIS som migrerats och associeras med en resurs utan att du behöver välja vilken sökväg som ska användas.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Logga in med användar kontot som inte är associerat med en Azure-prenumeration

1. Logga in på [Luis-portalen](https://www.luis.ai) och kontrol lera att du godkänner användnings villkoren.

1. Slutför genom att välja **Fortsätt**. Du loggas in automatiskt med en utvärderings-/start nyckel. Det innebär att du kommer att behöva [migrera ditt konto](luis-migration-authoring.md#migration-steps) och länka dina program till en redigerings resurs. För att genomgå migreringsprocessen måste du logga in för en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Inget prenumerations scenario":::

## <a name="troubleshooting"></a>Felsökning

* Om du skapar en redigerings resurs från Azure Portal i en annan region än portalen som du loggar in på, blir redigerings resursen nedtonad.
* När du skapar en ny resurs måste du se till att resurs namnet endast innehåller alfanumeriska tecken,-och får inte börja eller sluta med-. Annars fungerar det inte.
* Kontrol lera att du har [rätt behörigheter för din prenumeration för att skapa en Azure-resurs](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Om du inte har rätt behörighet kan du kontakta prenumerationens administratör för att ge dig tillräckliga behörigheter.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [startar en ny app](luis-how-to-start-new-app.md)
