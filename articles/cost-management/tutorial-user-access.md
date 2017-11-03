---
title: "Tilldela åtkomst i Azure kostnaden Management | Microsoft Docs"
description: "Tilldela åtkomst till data som hanteras med användarkonton som definierar åtkomstnivåer till entiteter."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a42f3b51bf6d888d0d5602887ed317c6164391ef
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="assign-access-to-cost-management-data"></a>Tilldela åtkomst till kostnaden hanteringsdata

Åtkomst till kostnaden hanteringsdata tillhandahålls av användare eller enhet management. Cloudyn användarkonton styr åtkomst till *entiteter* och administrativa funktioner. Det finns två typer av åtkomst: admin och användare. Om inte ändrat per användare, administratörsåtkomst kan en användare obegränsad användning av alla funktioner i Cloudyn-portalen, inklusive: Användarhantering, hantering av mottagare listor och rotenhet åtkomst till alla entitetsdata. Åtkomst är avsedd för användare att visa rapporter och skapa rapporter med hjälp av åtkomst som de behöver entitetsdata.

Entiteter används för att återspegla business organisationens hierarkisk struktur. De identifierar avdelningar, avdelningar och team i din organisation i Cloudyn. Entitetshierarkin hjälper dig att spåra utgifter av enheter.

När du har registrerat din Azure-avtalet eller konto, har ett konto med administratörsbehörighet skapats i Cloudyn, så du kan utföra alla steg i den här kursen. Den här kursen ingår åtkomst till kostnaden hanteringsdata inklusive användarhantering och hantering av enheten. Lär dig att:

> [!div class="checklist"]
> * Skapa en användare med administratörsåtkomst
> * Skapa en användare med åtkomst
> * Skapa entiteter



## <a name="create-a-user-with-admin-access"></a>Skapa en användare med administratörsåtkomst

Även om du redan har administratörsåtkomst kan medarbetare i din organisation också behöva ha administratörsåtkomst. Klicka på symbolen växel i övre högra och välj i portalen Cloudyn **Användarhantering**. Klicka på **Lägg till nya användare** att lägga till en ny användare.

Ange nödvändig information om användaren. Du kan lämna lösenordsfältet tomt så att användaren kan ange ett nytt lösenord på första inloggning. En länk med tecken i information skickas till användaren via e-post från Cloudyn när du väljer **meddela användare via e-post**. Välj behörigheter för att tillåta Användarhantering så att användaren kan skapa och ändra andra användare. Visar en lista över hantering av mottagare att tillåta användaren att redigera mottagande listor.

Under **användaren har administratörsåtkomst**, rotenhet i din organisation har valts. Lämna rot som valts och sedan spara informationen. Att välja den rotenheten kan användaren behörighet admin inte bara rotenhet i trädet utan även alla enheter som finns under den.  
  ![lägga till nya användare med administratörsåtkomst](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Skapa en användare med åtkomst
Vanliga användare som behöver åtkomst till kostnaden hanteringsdata som instrumentpaneler och rapporter ska ha åtkomst till visa dem. Skapa en ny användare med användaråtkomst liknar det du har skapat med administratörsåtkomst med följande skillnader:

- Rensa **Tillåt Användarhantering**, **Tillåt mottagaren visar Management**, och radera den **användaren har administratörsåtkomst** lista.
- Välj de enheter som användaren behöver åtkomst till i den **användare har åtkomst** lista.
- Du kan också tillåta admin får tillgång till specifika enheter efter behov.

![lägga till nya användare med åtkomst](.\media\tutorial-user-access\new-user-access.png)

Om du vill se en video som självstudiekurs om att lägga till användare, se [att lägga till användare till Azure kostnaden Management av Cloudyn](https://youtu.be/Nzn7GLahx30).

## <a name="create-entities"></a>Skapa entiteter

När du definierar din kostnaden enhetshierarki är ett bra tips att identifiera strukturen för din organisation.

När du skapar trädet Överväg hur eller behöver se deras kostnader som åtskiljs av affärsenheter, kostnad försäljning avdelningarna, miljöer och datacenter. Entiteten trädet i Cloudyn är flexibel på grund av entiteten arv. Enskilda prenumerationer för moln-konton är kopplade till specifika enheter. Entiteter är flera innehavare. Du kan tilldela specifika användare åtkomst till sina segment av ditt företag med entiteter. Gör det för att hålla data isolerade även över stora delar av företag som dotterbolag. Och isolering av data kan hjälpa dig med styrning.  

När du har registrerat din Azure-avtalet eller ett konto med Cloudyn, har inklusive användning, prestanda, fakturering och taggdata från dina prenumerationer Azure-resursdata kopierats till Cloudyn-konto. Dock måste du manuellt skapa entitet-trädet. Om du hoppade över Azure Resource Manager-registrering sedan endast faktureringsinformation och några rapporter för tillgångsinformation finns i Cloudyn-portalen.

I Cloudyn-portalen klickar du på **inställningar** i övre högra och välj **moln konton**. Du börjar med en enda entitet (rot) och skapa din enhet trädet under roten. Här är ett exempel på en enhetshierarki kan likna många IT-organisationer när trädet är klar:

![entiteten träd](.\media\tutorial-user-access\entity-tree.png)

Bredvid **entiteter**, klickar du på **lägga till entiteten**. Ange information om den person eller avdelning som du vill lägga till. Den **fullständiga namn** och **e-post** fält som behöver inte matcha befintliga användare. Om du vill visa en lista över åtkomstnivåer söker i hjälpen för *att lägga till en entitet*.

![Lägga till entitet](.\media\tutorial-user-access\add-entity.png)

När du är klar **spara** entiteten.


Om du vill se en video som självstudier om hur du skapar en enhetshierarki kostnad, se [skapar en enhetshierarki kostnaden i Azure kostnaden Management med Cloudyn](https://youtu.be/dAd9G7u0FmU).

Om du är en Azure-Företagsavtal användare kan se en självstudiekurs video om hur du kopplar konton och prenumerationer på entiteter på [ansluter till Azure Resource Manager med Azure kostnaden Management med Cloudyn](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en användare med administratörsåtkomst
> * Skapa en användare med åtkomst
> * Skapa entiteter

Gå vidare till nästa kurs att lära dig göra prognoser för utgifter med historiska data.

> [!div class="nextstepaction"]
> [Prognostisera framtida utgifter](tutorial-forecast-spending.md)
