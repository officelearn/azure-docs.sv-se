---
title: Självstudie – Tilldela åtkomst i Azure Cost Management | Microsoft Docs
description: I den här självstudien lärde du dig att tilldela åtkomst till kostnadshanteringsdata med användarkonton som definierar åtkomstnivåer till entiteter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/17/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 3ceed8b88b9c81954c967d3d7ddd964c532867ab
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301615"
---
# <a name="tutorial-assign-access-to-cost-management-data"></a>Självstudie: Tilldela åtkomst till kostnadshanteringsdata

Åtkomsten till Cost Management-data hanteras med hjälp av användare och entiteter. Cloudyn-användarkontot avgör åtkomsten till *entiteter* och administrativa funktioner. Det finns två typer av åtkomst: administratör och användare. Om administratörsåtkomsten inte modifieras per användare så ger den obegränsad tillgång till alla funktioner i Cloudyn-portalen, inklusive användarhantering, hantering av mottagarlistor och rotåtkomst till alla entitetsdata. Användaråtkomsten är avsedd för slutanvändare som vill visa och skapa rapporter för de entitetsdata de har åtkomst till.

Entiteter används till att återspegla affärsorganisationens hierarkiska struktur. De identifierar organisationens avdelningar och team i Cloudyn. Med entitetshierarkin kan du noggrant spåra olika utgifter per entitet.

När du registrerade ditt Azure-avtal eller -konto skapades ett konto med administratörsbehörighet i Cloudyn, så att du kan utföra alla steg i den här självstudien. Den här självstudien tar upp åtkomsten till Cost Management-data samt hantering av användare och entiteter. Lär dig att:

> [!div class="checklist"]
> * Skapa en användare med administratörsåtkomst
> * Skapa en användare med användaråtkomst
> * Skapa och hantera entiteter


Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha ett Azure-konto.
- Du måste antingen ha en utvärderingsregistrering eller en betald prenumeration för Azure Cost Management.

## <a name="create-a-user-with-admin-access"></a>Skapa en användare med administratörsåtkomst

Även om du redan har administratörsåtkomst kanske medarbetare i organisationen också behöver ha administratörsåtkomst. Klicka på kugghjulssymbolen uppe till höger i Cloudyn-portalen och välj **User Management** (Användarhantering). Klicka på **Add New User** (Lägg till ny användare) för att lägga till en ny användare.

Ange nödvändig information om användaren. Inloggnings-ID:t måste vara en giltig e-postadress. Välj behörigheter och tillåt User Management (Användarhantering) så att användaren kan skapa och ändra andra användare. Med hanteringen av mottagarlistor kan användaren redigera mottagarlistor. En länk med inloggningsinformation skickas till användaren via e-post från Cloudyn när du väljer **Notify user by email** (Meddela användaren via e-post). Användaren anger ett lösenord vid sin första inloggning.

Under **User has admin access** (Användaren har administratörsåtkomst) är rotentiteten i din organisation vald. Lämna root valt och spara användarinformationen. När rotentiteten är vald har användaren inte bara administratörsbehörighet för rotentiteten i trädet utan även alla enheter under den.  
  ![lägg till en ny användare med administratörsåtkomst](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Skapa en användare med användaråtkomst
Vanliga användare som behöver åtkomst till Cost Management-data som instrumentpaneler och rapporter ska ha användaråtkomst för att visa dem. Skapa en ny användare med användaråtkomst ungefär som när du skapade användaren med administratörsåtkomst, med följande skillnader:

- Avmarkera **Allow User Management**, **Allow Recipient lists Management** och allt i listan **User has admin access**.
- Välj de entiteter som användaren behöver åtkomst till i listan **User has user access** (Användaren har användaråtkomst).
- Du kan också ge administratörer åtkomst till specifika entiteter om det behövs.

![lägg till en ny användare med användaråtkomst](.\media\tutorial-user-access\new-user-access.png)

Om du vill se en självstudievideo om att lägga till användare kan du titta på [Adding Users to Azure Cost Management ](https://youtu.be/Nzn7GLahx30) (Lägga till användare i Azure Cost Management).

## <a name="create-and-manage-entities"></a>Skapa och hantera entiteter

När du definierar din entitetshierarki för kostnader är det ett bra tips att identifiera organisationens struktur. Med entiteter kan du sortera utgifter efter enskilda konton eller prenumerationer. Du skapar kostnadsentiteter för att skapa logiska grupper som hjälper dig att hantera och spåra utgifter. När du skapar trädet ska du tänka på hur du vill se kostnaderna uppdelade mellan olika affärsenheter, kostnadsställen, miljöer och säljavdelningar. Entitetsträdet i Cloudyn är flexibelt på grund av entitetsarvet.

Enskilda prenumerationer för dina molnkonton är kopplade till specifika entiteter. Du associerar en entitet med kontot för en molntjänstleverantör eller prenumeration. Entiteter kan alltså ha flera klientorganisationer. Du kan tilldela specifika användare åtkomst till just sina affärssegment med entiteter. Om du gör det kan du hålla data isolerade även i stora delar av företag, som ett dotterbolag. Dataisoleringen hjälper även till med styrningen.  

När du registrerade ditt Azure-avtal eller -konto hos Cloudyn kopierades dina Azure-resursdata, inklusive användning, prestanda, fakturering och taggdata från dina prenumerationer, till Cloudyn-kontot. Du måste däremot skapa entitetsträdet manuellt. Om du hoppade över Azure Resource Manager-registreringen är endast faktureringsdata och några tillgångsrapporter tillgängliga i Cloudyn-portalen.

Klicka på kugghjulssymbolen uppe till höger i Cloudyn-portalen och välj **Molnkonton**. Du börjar med en enda entitet (roten) och skapar entitetsträdet under roten. Här är ett exempel på en entitetshierarki som liknar många IT-organisationer när trädet är färdigt:

![entitetsträd](.\media\tutorial-user-access\entity-tree.png)

Bredvid **Entities** (Entiteter) klickar du på **Add Entity** (Lägg till entitet). Ange information om personen eller avdelningen du vill lägga till. Fälten **Full Name** (Fullständigt namn) och **Email** (E-post) behöver inte matcha befintliga användare. Om du vill visa en lista med åtkomstnivåer kan du söka i hjälpen efter *Lägga till en entitet*.

![lägg till entitet](.\media\tutorial-user-access\add-entity.png)

När du är färdig sparar du entiteten med **Save** (Spara).

### <a name="entity-access-levels"></a>Åtkomstnivåer för entiteter

Med åtkomstnivåer för entiteter tillsammans med en användares behörighet kan du definiera vilken typ av åtgärder som är tillgängliga på Cloudyn-portalen.

- **Enterprise** – Ger behörighet att skapa och hantera underordnade kostnadsentiteter.
- **Enterprise + kostnadsallokering** – Ger behörighet att skapa och hantera underordnade kostnadsentiteter, inklusive kostnadsallokering för konsoliderade konton.
- **Enterprise, kostnad baserat på överordnad kostnadsallokering** – Ger behörighet att skapa och hantera underordnade kostnadsentiteter. Kostnaderna för kontot baseras på den överordnade kostnadsallokeringsmodellen.
- **Endast anpassade instrumentpaneler** – Ger endast behörighet att visa fördefinierade anpassade instrumentpaneler.
- **Endast instrumentpaneler** – Ger endast behörighet att visa instrumentpaneler.

### <a name="create-a-cost-entity-hierarchy"></a>Skapa en hierarki för kostnadsentiteter

För att skapa en hierarki för kostnadsentiteter måste du ha ett konto med åtkomst av typen Enterprise eller Enterprise + kostnadsallokering.

Klicka på kugghjulssymbolen uppe till höger i Cloudyn-portalen och välj **Molnkonton**. Trädet med **entiteter** visas i den vänstra rutan. Om det behövs expanderar du entitetsträdet så att du kan visa den entitet som du vill associera med ett konto.  Kontona för din molntjänstleverantör visas på flikarna i den högra rutan. Välj en flik och klicka och dra ett konto eller en prenumeration till entiteten. Rutan **Flytta** meddelar dig att kontot har skapats. Klicka på **OK**.

Du kan också associera flera konton med en entitet. Markera kontona och klicka sedan på **Flytta**. I rutan Flytta konton väljer du den entitet som du vill flytta kontot till och klickar sedan på **Spara**. Rutan Flytta konton uppmanar dig att bekräfta att du vill flytta kontona. Klicka på **Ja** och sedan på **OK**.

Om du vill se en självstudievideo om hur du skapar en entitetshierarki för kostnader kan du titta på [Creating a Cost Entity Hierarchy in Azure Cost Management](https://youtu.be/dAd9G7u0FmU) (Skapa en enhetshierarki i Azure Cost Management).

Om du är en Azure Enterprise Agreement-användare kan du titta på en självstudievideo om hur du kopplar konton och prenumerationer till entiteter i [Connecting to Azure Resource Manager with Azure Cost Management](https://youtu.be/oCIwvfBB6kk) (Ansluta till Azure Resource Manager med Azure Cost Management).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en användare med administratörsåtkomst
> * Skapa en användare med användaråtkomst
> * Skapa och hantera entiteter


Om du inte redan har aktiverat Azure Resource Manager API-åtkomst för dina konton, fortsätter du till följande artikel.

> [!div class="nextstepaction"]
> [Aktivera Azure-prenumerationer och -konton](activate-subs-accounts.md)
