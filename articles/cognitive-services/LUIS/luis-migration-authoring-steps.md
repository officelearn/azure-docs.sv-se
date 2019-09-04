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
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 13eb301daadb70456c0c0d7db8991d7a695b1de9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259688"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Steg för att migrera till Azure Authoring-resursen

Från Language Understanding-portalen (LUIS) migrerar du alla appar som du äger för att använda Azures redigerings resurs.

## <a name="prerequisites"></a>Förutsättningar

* **Du kan också**säkerhetskopiera apparna från Luis-portalens lista med appar genom att exportera varje app eller använda export- [API: et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Du kan också**Spara varje apps collaborator's-lista. Den här e-postlistan tillhandahålls som en del av migreringsprocessen.
* **Krävs**måste du ha en Azure- [prenumeration](https://azure.microsoft.com/free/). En del av prenumerations processen kräver fakturerings information. Du kan dock använda kostnads fria (F0) pris nivåer när du använder LUIS. Du kanske upptäcker att du behöver en betald nivå, eftersom användningen ökar. 

Om du inte har någon Azure-prenumeration kan du [Registrera dig](https://azure.microsoft.com/free/). 

## <a name="access-the-migration-process"></a>Åtkomst till migreringsprocessen

Varje vecka uppmanas du att migrera dina appar. Du kan avbryta det här fönstret utan att migrera. Om du vill migrera före nästa schemalagda period kan du starta migreringsprocessen från **Lås** ikonen i det övre verktygsfältet i Luis-portalen. 

## <a name="app-owner-begins-the-migration-process"></a>Appens ägare börjar migreringsprocessen

Migreringsprocessen är tillgänglig om du äger LUIS-appar. 

1. 1. Logga in på [Luis-portalen](https://www.luis.ai) och godkänn användnings villkoren.
1. I popup-fönstret migrering kan du fortsätta migreringen eller migrera senare. Välj **migrera nu**. Om du väljer att migrera senare har du 9 månader på att migrera till den nya redigerings nyckeln i Azure.

    ![Första popup-fönstret i migreringsprocessen väljer du migrera nu.](./media/migrate-authoring-key/migrate-now.png)

1. Om någon av dina appar har medarbetare, uppmanas du att **Skicka ett e-postmeddelande** till dem för att få veta om migreringen. Det här är ett valfritt steg. Standard programmet för e-post öppnas med ett ljust formaterat e-postmeddelande. 

    ![Skicka e-post till medarbetare om migrering](./media/migrate-authoring-key/send-collaborators-email.png)

1. Välj att skapa en LUIS Authoring-resurs genom att välja **Start genom att skapa en redigerings resurs för att migrera dina appar till**. 

    ![Skapa en redigerings resurs](./media/migrate-authoring-key/choose-authoring-resource.png)

1. I nästa fönster anger du information om resurs nyckeln. När du har angett informationen väljer du **skapa resurs**. 

    ![Skapa en redigerings resurs](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    När du **skapar en ny redigerings resurs**anger du följande information: 

    * **Resurs namn** – ett anpassat namn som du väljer, används som en del av URL: en för din redigering och förutsägelse slut punkts frågor.
    * **Klient** organisation – klienten som din Azure-prenumeration är associerad med. 
    * **Prenumerations namn** – den prenumeration som ska faktureras för resursen.
    * **Resurs grupp** – ett namn på en anpassad resurs grupp som du väljer eller skapar. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering. 
    * **Plats** – plats valet baseras på **resurs grupps** valet.
    * **Pris nivå** – pris nivån avgör den högsta transaktionen per sekund och månad. 

1. När en redigerings resurs skapas visas meddelandet lyckades. Välj **Stäng** för att stänga popup-fönstret.

    ![Din redigerings resurs har skapats.](./media/migrate-authoring-key/migration-success.png)

    I listan **Mina appar** visas appar som migrerats till den nya redigerings resursen. 

    Du behöver inte känna till redigerings resursens nyckel för att fortsätta redigera dina appar i LUIS-portalen. Om du planerar att redigera dina appar program mässigt behöver du redigera nyckel värden. Dessa värden visas på sidan **Hantera-> Azure-resurser** på Luis-portalen och finns också i Azure Portal på resursens **nyckel** sida.  

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Efter migreringsprocessen lägger du till deltagare i din redigerings resurs

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Lär dig [hur du lägger till bidrags givare](luis-how-to-collaborate.md). 

## <a name="next-steps"></a>Nästa steg


* Granska [begrepp](luis-concept-keys.md) om redigerings-och körnings nycklar
* Granska [hur du tilldelar nycklar](luis-how-to-azure-subscription.md) och lägger till [deltagare](luis-how-to-collaborate.md)
