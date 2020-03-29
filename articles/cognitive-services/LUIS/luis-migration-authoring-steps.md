---
title: Migrera till en Azure-redigeringsresurs
titleSuffix: Azure Cognitive Services
description: Migrera till en Azure-redigeringsresurs.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194654"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Steg för att migrera till Azure-redigeringsresursen

Från LUIS-portalen (Language Understanding) migrerar du alla appar som du äger för att använda Azure-redigeringsresursen.

## <a name="prerequisites"></a>Krav

* **Du kan också**säkerhetskopiera apparna från LUIS-portalens applista genom att exportera varje app eller använda [export-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Du kan också**spara varje apps medarbetares lista. Alla medarbetare kan skickas ett e-postmeddelande som en del av migreringsprocessen.
* **Obligatoriskt**måste du ha en [Azure-prenumeration](https://azure.microsoft.com/free/). En del av prenumerationsprocessen kräver faktureringsinformation. Du kan dock använda kostnadsfria (F0) prisnivåer när du använder LUIS. Du kan så småningom hitta du behöver en betald nivå, eftersom din användning ökar.

Om du inte har en Azure-prenumeration [registrerar du dig](https://azure.microsoft.com/free/).

## <a name="access-the-migration-process"></a>Få tillgång till migreringsprocessen

Varje vecka uppmanas du att migrera dina appar. Du kan avbryta det här fönstret utan att migrera. Om du vill migrera före nästa schemalagda period kan du påbörja migreringsprocessen från **Azure-ikonen** i det övre verktygsfältet i LUIS-portalen.

> [!div class="mx-imgBorder"]
> ![Ikon för Migrering](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Appägaren inleder migreringsprocessen

Migreringsprocessen är tillgänglig om du äger några LUIS-appar.

1. Logga in på [LUIS-portalen](https://www.luis.ai) och godkänn användarvillkoren.
1. Med popup-fönstret för migrering kan du fortsätta migreringen eller migrera senare. Välj **Migrera nu**. Om du väljer att migrera senare har du nio månader på dig att migrera till den nya redigeringsnyckeln i Azure.

    ![Välj Migrera nu.](./media/migrate-authoring-key/migrate-now.png)

1. Om någon av dina appar har medarbetare uppmanas du att skicka ett e-postmeddelande till dem om migreringen om någon av dina appar uppmanas att **skicka ett e-postmeddelande** till dem om migreringen. Detta är ett valfritt steg.

    När du har migrerat ditt konto till Azure är dina appar inte längre tillgängliga för medarbetare.

    För varje medarbetare och app öppnas standardprogrammet för e-post med ett lätt formaterat e-postmeddelande. Du kan redigera e-postmeddelandet innan du skickar det.

    E-postmallen innehåller exakt app-ID och appnamn.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Välj att skapa en LUIS-redigeringsresurs genom att välja att använda en befintlig redigeringsresurs eller för att skapa en ny redigeringsresurs.

    > [!div class="mx-imgBorder"]
    > ![Skapa redigeringsresurs](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. I nästa fönster anger du information om resursnyckeln. När du har angett informationen väljer du **Skapa resurs**. Du kan ha 10 kostnadsfria redigeringsresurser per region, per prenumeration.

    ![Skapa redigeringsresurs](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    När **du skapar en ny redigeringsresurs**anger du följande information:

    * **Resursnamn** - ett eget namn som du väljer, som används som en del av URL:en för dina redigerings- och förutsägelseslutpunktsfrågor.
    * **Klient** - klienten som din Azure-prenumeration är associerad med.
    * **Prenumerationsnamn** - den prenumeration som faktureras för resursen.
    * **Resursgrupp** - ett anpassat resursgruppnamn som du väljer eller skapar. Med resursgrupper kan du gruppera Azure-resurser för åtkomst och hantering.
    * **Plats** - platsvalet baseras på valen **av resursgrupp.**
    * **Prisnivå** - prisnivån bestämmer den maximala transaktionen per sekund och månad.

1. Verifiera din redigeringsresurs och **Migrera nu**.

    ![Skapa redigeringsresurs](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. När redigeringsresursen skapas visas meddelandet lyckade resultat. Välj **Stäng** om du vill stänga popup-fönstret.

    ![Redigeringsresursen har skapats.](./media/migrate-authoring-key/migration-success.png)

    I listan **Mina appar** visas de appar som migrerats till den nya redigeringsresursen.

    Du behöver inte känna till redigeringsresursens nyckel för att fortsätta redigera dina appar i LUIS-portalen. Om du planerar att redigera dina appar programmässigt behöver du värdena för redigeringsnyckeln. Dessa värden visas på sidan **Hantera -> Azure-resurser** i LUIS-portalen och är även tillgängliga i Azure-portalen på resursens **keys-sida.**

1. Innan du öppnar dina appar väljer du prenumerations- och LUIS-redigeringsresursen för att se de appar du kan skapa.

    ![Välj prenumeration och LUIS-redigeringsresurs för att se vilka appar du kan skapa.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Appdeltagaren inleder migreringsprocessen

Följ samma steg som appens ägare för migrering. Processen skapar en ny författarresurs av slag `LUIS.Authoring`.

Du måste migrera ditt konto för att läggas till som deltagare till migrerade appar som ägs av andra.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Efter migreringsprocessen lägger du till deltagare i din redigeringsresurs

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Läs om hur du [lägger till deltagare](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Felsöka fel med migreringsprocessen

Om du `MissingSubscriptionRegistration` får ett fel i LUIS-portalen med ett rött meddelandefält under migreringsprocessen skapar du en Cognitive Service-resurs i [Azure-portalen](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) eller [Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli). Läs mer om [orsakerna till det här felet](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Nästa steg


* Granska [begrepp](luis-concept-keys.md) om redigerings- och körningsnycklar
* Granska [hur du tilldelar nycklar](luis-how-to-azure-subscription.md) och lägger till [deltagare](luis-how-to-collaborate.md)
