---
title: Migrera till en Azure Authoring-resurs
titleSuffix: Azure Cognitive Services
description: Migrera till en Azure Authoring-resurs.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78194654"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Steg för att migrera till Azure Authoring-resursen

Från Language Understanding-portalen (LUIS) migrerar du alla appar som du äger för att använda Azures redigerings resurs.

## <a name="prerequisites"></a>Krav

* **Du kan också**säkerhetskopiera apparna från Luis-portalens lista med appar genom att exportera varje app eller använda export- [API: et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Du kan också**Spara varje apps collaborator's-lista. Alla medarbetare kan skicka ett e-postmeddelande som en del av migreringsprocessen.
* **Krävs**måste du ha en Azure- [prenumeration](https://azure.microsoft.com/free/). En del av prenumerations processen kräver fakturerings information. Du kan dock använda kostnads fria (F0) pris nivåer när du använder LUIS. Du kanske upptäcker att du behöver en betald nivå, eftersom användningen ökar.

Om du inte har någon Azure-prenumeration kan du [Registrera dig](https://azure.microsoft.com/free/).

## <a name="access-the-migration-process"></a>Åtkomst till migreringsprocessen

Varje vecka uppmanas du att migrera dina appar. Du kan avbryta det här fönstret utan att migrera. Om du vill migrera före nästa schemalagda period kan du starta migreringsprocessen från **Azure** -ikonen i det övre verktygsfältet i Luis-portalen.

> [!div class="mx-imgBorder"]
> ![Migrerings ikon](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Appens ägare börjar migreringsprocessen

Migreringsprocessen är tillgänglig om du äger LUIS-appar.

1. Logga in på [Luis-portalen](https://www.luis.ai) och godkänn användnings villkoren.
1. I popup-fönstret migrering kan du fortsätta migreringen eller migrera senare. Välj **migrera nu**. Om du väljer att migrera senare har du 9 månader på att migrera till den nya redigerings nyckeln i Azure.

    ![Första popup-fönstret i migreringsprocessen väljer du migrera nu.](./media/migrate-authoring-key/migrate-now.png)

1. Om någon av dina appar har medarbetare, uppmanas du att **Skicka ett e-postmeddelande** till dem för att få veta om migreringen. Detta är ett valfritt steg.

    När du har migrerat ditt konto till Azure kommer dina appar inte längre att vara tillgängliga för medarbetare.

    För varje medarbetare och app öppnas standard programmet för e-post med ett ljust formaterat e-postmeddelande. Du kan redigera e-postmeddelandet innan du skickar det.

    E-postmallen innehåller det exakta app-ID och app-namn.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Välj att skapa en LUIS Authoring-resurs genom att välja att använda en befintlig redigerings resurs eller för att skapa en ny redigerings resurs.

    > [!div class="mx-imgBorder"]
    > ![Skapa en redigerings resurs](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. I nästa fönster anger du information om resurs nyckeln. När du har angett informationen väljer du **skapa resurs**. Du kan ha 10 kostnads fria redigerings resurser per region, per prenumeration.

    ![Skapa en redigerings resurs](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    När du **skapar en ny redigerings resurs**anger du följande information:

    * **Resurs namn** – ett anpassat namn som du väljer, används som en del av URL: en för din redigering och förutsägelse slut punkts frågor.
    * **Klient** organisation – klienten som din Azure-prenumeration är associerad med.
    * **Prenumerations namn** – den prenumeration som ska faktureras för resursen.
    * **Resurs grupp** – ett namn på en anpassad resurs grupp som du väljer eller skapar. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering.
    * **Plats** – plats valet baseras på **resurs grupps** valet.
    * **Pris nivå** – pris nivån avgör den högsta transaktionen per sekund och månad.

1. Verifiera din redigerings resurs och **migrera nu**.

    ![Skapa en redigerings resurs](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. När en redigerings resurs skapas visas meddelandet lyckades. Välj **Stäng** för att stänga popup-fönstret.

    ![Din redigerings resurs har skapats.](./media/migrate-authoring-key/migration-success.png)

    I listan **Mina appar** visas appar som migrerats till den nya redigerings resursen.

    Du behöver inte känna till redigerings resursens nyckel för att fortsätta redigera dina appar i LUIS-portalen. Om du planerar att redigera dina appar program mässigt behöver du redigera nyckel värden. Dessa värden visas på sidan **Hantera-> Azure-resurser** på Luis-portalen och finns också i Azure Portal på resursens **nyckel** sida.

1. Innan du får åtkomst till dina appar väljer du resursen prenumeration och LUIS Authoring för att se de appar som du kan redigera.

    ![Välj prenumerations-och LUIS som skapar resurs för att se vilka appar som kan redigera.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>App Contributor påbörjar migreringsprocessen

Följ samma steg som för program ägaren för migrering. Processen skapar en ny redigerings resurs av typen `LUIS.Authoring`.

Du måste migrera ditt konto för att kunna läggas till som deltagare i migrerade appar som ägs av andra.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Efter migreringsprocessen lägger du till deltagare i din redigerings resurs

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Lär dig [hur du lägger till bidrags givare](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Felsöka fel med migreringsprocessen

Om du får ett `MissingSubscriptionRegistration` fel i Luis-portalen med ett rött meddelande fält under migreringsprocessen, skapar du en kognitiv tjänst resurs i [Azure Portal](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) eller [Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli). Läs mer om [orsaker till det här felet](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Nästa steg


* Granska [begrepp](luis-concept-keys.md) om redigerings-och körnings nycklar
* Granska [hur du tilldelar nycklar](luis-how-to-azure-subscription.md) och lägger till [deltagare](luis-how-to-collaborate.md)
