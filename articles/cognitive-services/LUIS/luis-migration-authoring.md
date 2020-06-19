---
title: Migrera till Azure-resurs för redigering 2
titleSuffix: Azure Cognitive Services
description: Migrera till en Azure Authoring-resurs nyckel 2.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983801"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrera till en Azure-resurs redigerings nyckel

Language Understanding (LUIS) som redigerar autentiseringen har ändrats från ett e-postkonto till en Azure-resurs. När det inte krävs för närvarande kommer växling till en Azure-resurs att verkställas i framtiden.


## <a name="what-is-migration"></a>Vad är migrering?

Migrering är en process för att ändra auktorisering av autentisering från ett e-postkonto till en Azure-resurs. Ditt konto kommer att länkas till en Azure-prenumeration och en Azure Authoring-resurs när du har migrerat. **Alla LUIS-användare (ägare eller medarbetare) behöver eventuellt migrera.** Migreringen måste göras från LUIS-portalen. Om du skapar redigerings nycklarna, till exempel med LUIS CLI, måste du fortfarande slutföra migreringsprocessen i LUIS-portalen. Du kan fortfarande ha medförfattare i dina program efter migreringen, men de läggs till på Azure-resurs nivån i stället för på program nivån.

> [!Note]
> Innan migreringen kallas medförfattare _för medarbetare på Luis_ app-nivå. Efter migreringen används Azure-rollen för _deltagare_ för samma funktioner, men på Azures resurs nivå.

## <a name="note-before-you-migrate"></a>Observera innan du migrerar

* Migrering är en **enkelriktad process.** Du kan inte gå tillbaka när du har migrerat.
* Program kommer **automatiskt att migreras** med dig om du är **ägare** till programmet.
* Ägaren kan inte välja en delmängd av appar som ska migreras och processen inte kan ångras.
* Programmen kommer att **försvinna från medarbetares sidor** när **ägaren migreras**.
* Ägare uppmanas att skicka e-postmeddelanden till medarbetare för att informera om migreringen.
* Program kommer **inte att migreras** med dig om du är en **medarbetare** i programmet.
* Det finns inget sätt för en ägare att veta att hans medarbetare har migrerat.
* **Migrering samlar inte** automatiskt in medarbetare och flyttar eller lägger till dem i Azure Authoring-resursen. Appens ägare är den som måste slutföra det här steget efter migreringen. Det här steget kräver [behörighet till Azure Authoring-resursen](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* När de har tilldelats till Azure-resursen **måste medarbetare migrera till åtkomst till program**. Annars kommer de inte att ha åtkomst till att redigera programmen.
* Det går inte att lägga till en migrerad användare som en medarbetare för programmet.
* Om **du äger förutsägelse nycklar som tilldelas program som ägs av en annan användare** **blockerar detta migreringen** för både ägare och medarbetare. Nedan finns rekommendationer.

> [!Note]
> Om du behöver skapa en förutsägelse körnings resurs finns det [en separat process](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) för att skapa den.

## <a name="migration-prerequisites"></a>Krav för migrering

* Du måste vara associerad med en giltig Azure-prenumeration. Be din klient administratör att lägga till dig på prenumerationen eller registrera dig för en kostnads fri [här](https://azure.microsoft.com/free/).
* Du måste skapa en LUIS Azure Authoring-resurs från LUIS-portalen eller från Azure Portal. Att skapa en Authoring-resurs från LUIS-portalen är en del av det migreringsjobb som beskrivs i nästa avsnitt.
* Om du är en **medarbetare i program**migreras inte program automatiskt. **Vi rekommenderar att du säkerhetskopierar dessa program** genom att exportera dem eller använda [export-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Du kan importera appen tillbaka till LUIS efter migreringen. Import processen skapar en ny app med ett nytt app-ID som du är ägare till.
* Om du är **ägare till programmet**behöver du inte exportera dina appar eftersom de migreras automatiskt. **Vi rekommenderar att du sparar varje apps collaborator's-lista.** En e-postmall som har den här listan tillhandahålls som en del av migreringsprocessen.


|Portalen|Syfte|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Skapa förutsägelse-och redigera resurser.<br>* Tilldela deltagare i resurser.|
|[LUIS](https://www.luis.ai)|* Migrera till nya redigerings resurser.<br>* Skapa en ny Authoring-resurs i migreringsjobbet.<br>* Tilldela eller ta bort tilldelningar av förutsägelse och redigering av resurser till appar från sidan **hantera > Azure-resurser** . <br>* Flytta program från en redigerings resurs till en annan.  |

> [!Note]
> **Redigering av din Luis-app är kostnads fri**, vilket anges av `F0` nivån. Läs [mer om pris nivåer](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Migreringsanvisningar

1. I LUIS-portalen som du arbetar med kan du starta migreringsprocessen från **Azure** -ikonen i det övre verktygsfältet.

   > [!div class="mx-imgBorder"]
   > ![Migrerings ikon](./media/migrate-authoring-key/migration-button.png)

2. I popup-fönstret migrering kan du fortsätta migreringen eller migrera senare. Välj **migrera nu**.

   > [!div class="mx-imgBorder"]
   > ![Första popup-fönstret i migreringsprocessen väljer du migrera nu.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Om någon av dina appar har medarbetare, uppmanas du att **Skicka ett e-postmeddelande** till dem för att få veta om migreringen. Detta är ett valfritt steg.

   För varje medarbetare och app öppnas standard programmet för e-post med ett ljust formaterat e-postmeddelande. Du kan redigera e-postmeddelandet innan du skickar det. E-postmallen innehåller det exakta app-ID och app-namn.
   
   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources
      
   Thank you
   ```
   
   > [!Note]
   > När du har migrerat ditt konto till Azure kommer dina appar inte längre att vara tillgängliga för medarbetare.

4. Om du är en medarbetare i ett program uppmanas du att **Exportera en kopia av apparna** genom att välja det här alternativet under migreringen. När du har valt alternativet hittar du sidan nedan där du klickar på knappen Ladda ned till vänster för att exportera de appar som du vill använda. Du kan importera dessa appar tillbaka när du har migrerat eftersom de inte kommer att migreras automatiskt med dig. Detta är ett valfritt steg.

   > [!div class="mx-imgBorder"]
   > ![Prompt för att exportera programmet.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Du kan välja att skapa en ny LUIS Authoring-resurs eller migrera till en befintlig redigerings resurs om du redan har skapat en från Azure. Välj det alternativ som du vill ha genom att välja rätt knapp nedan.

   > [!div class="mx-imgBorder"]
   > ![Skapa en redigerings resurs](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Skapa en ny redigerings resurs från LUIS som ska migreras

Om du vill skapa en ny redigerings resurs väljer du **skapa en ny redigerings resurs** och anger följande information i nästa fönster.

> [!div class="mx-imgBorder"]
> ![Skapa en redigerings resurs](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Resurs namn** – ett anpassat namn som du väljer, används som en del av URL: en för din redigering och förutsägelse slut punkts frågor.
* **Prenumerations namn** – den prenumeration som ska associeras med resursen. Om du har mer än en prenumeration som tillhör din klient väljer du den som du vill använda i list rutan.
* **Resurs grupp** – ett namn på en anpassad resurs grupp som du väljer i list rutan. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering.
* **Klient** organisation – klienten som din Azure-prenumeration är associerad med. Detta ställs in som standard till den klient som du väljer för närvarande. Du kan byta innehavare genom att välja den till höger avatar som innehåller dina initialer.

När du har angett ovanstående information väljer du **slutförd**.

Observera att du kan ha 10 kostnads fria redigerings resurser per region, per prenumeration. Om din prenumeration har fler än 10 redigerings resurser i samma region kan du inte skapa någon ny.

* När en redigerings resurs skapas visas meddelandet lyckades. Välj **Stäng** för att stänga popup-fönstret.

  > [!div class="mx-imgBorder"]
  > ![Din redigerings resurs har skapats.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>Använd befintlig redigerings resurs för att migrera

Om din prenumeration redan är kopplad till en LUIS som redigerar Azure-resurs eller om du har skapat den från Azure Portal och du vill migrera till den i stället för att skapa en ny resurs väljer du **Använd befintlig redigerings resurs** och anger följande information i nästa fönster.

> [!div class="mx-imgBorder"]
>![Skapa en redigerings resurs](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Klient** organisation – klienten som din Azure-prenumeration är associerad med. Detta ställs in som standard till den klient som du väljer för närvarande.
* **Prenumerations namn** – den prenumeration som ska associeras med resursen. Om du har mer än en prenumeration som tillhör din klient väljer du den som du vill använda i list rutan.
* **Resurs namn** – Välj den redigerings resurs som du vill migrera till.

> [!Note]
> Om du inte kan se din redigerings resurs i list rutan ser du till att du har skapat den på **rätt plats** enligt Luis-portalen som du är inloggad på. Kontrol lera också att det som du har skapat verkligen är en **redigerings resurs** och inte en **förutsägelse resurs**.


* Verifiera ditt redigerings resurs namn och klicka på knappen **migrera nu** .

 > [!div class="mx-imgBorder"]
 > ![Skapa en redigerings resurs](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* Meddelandet lyckades visas. Välj **Stäng** för att stänga popup-fönstret.

 > [!div class="mx-imgBorder"]
 > ![Din redigerings resurs har skapats.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Använda appar efter migrering

* Efter migreringen kommer alla LUIS-appar som du äger, att tilldelas till en enda LUIS-Authoring-resurs.
* I listan **Mina appar** visas appar som migrerats till den nya redigerings resursen.
* Innan du får åtkomst till dina appar väljer du resursen prenumeration och LUIS Authoring för att se de appar som du kan redigera.

 > [!div class="mx-imgBorder"]
 > ![Välj prenumerations-och LUIS som skapar resurs för att se vilka appar som kan redigera.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* Du behöver inte känna till redigerings resursens nyckel för att fortsätta redigera dina appar i LUIS-portalen.
* Om du planerar att redigera dina appar program mässigt behöver du redigera nyckel värden. Dessa värden visas på sidan **Hantera-> Azure-resurser** på Luis-portalen och finns också i Azure Portal på resursens **nyckel** sida. Du kan också skapa fler redigerings resurser och tilldela dem från samma sida.

 > [!div class="mx-imgBorder"]
 > ![Hantera redigering av resurs.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Lägg till deltagare i att redigera resurser

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Lär dig [hur du lägger till deltagare](luis-how-to-collaborate.md) på din redigerings resurs. Deltagare kommer att ha åtkomst till alla program i den resursen.

Du kan lägga till deltagare till redigerings resursen från _Azure Portal_på sidan **Access Control (IAM)** för resursen. Mer information finns i [lägga till deltagar åtkomst](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Om ägaren av LUIS-appen migrerat och lagt till medarbetaren som deltagare på Azure-resursen, kommer medarbetaren fortfarande inte att ha åtkomst till appen om de också migrerar.

## <a name="luis-portal-migration-reminders"></a>Påminnelser om migrering av LUIS-portalen

[Luis-portalen](https://www.luis.ai) innehåller migreringsprocessen.

Du uppmanas att migrera om:
* Du har appar i systemet för e-postautentisering för redigering.
* Och du är appens ägare.

Varje vecka uppmanas du att migrera dina appar. Du kan avbryta det här fönstret utan att migrera. Om du vill migrera före nästa schemalagda period kan du starta migreringsprocessen från **Azure** -ikonen i det övre verktygsfältet i Luis-portalen.

Du kan försena migreringsprocessen genom att avbryta fönstret. Du uppmanas regelbundet att migrera tills du migrerar eller så skickas tids gränsen för migreringen. Du kan starta migreringsprocessen från det övre navigerings fältets lås ikon.

## <a name="prediction-resources-blocking-migration"></a>Förutsägelse resurser blockerar migrering
Migreringen påverkar negativt alla program körningar. När du migrerar tas alla medarbetare bort från dina appar och du tas bort som en medarbetare från andra appar. Den här processen innebär att de nycklar som en medarbetare tilldelar också tas bort, vilket kan bryta ditt program om det är i produktion. Detta är orsaken till att vi blockerar migreringen tills du tar bort medarbetare eller nycklar som tilldelats dem manuellt.

### <a name="when-does-prediction-resources-block-migration"></a>När blockerar migreringen förutsägelse av resurser?
* Migreringen blockeras om du har tilldelat förutsägelse-/körnings resurser i appar som du inte äger.
* Migreringen blockeras om du har andra användare som tilldelar förutsägelse-/körnings resurser till appar som du äger.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Rekommenderade steg för att göra om du är ägare till appen
Om du är ägare till vissa program och har tilldelats medarbetare som har tilldelats förutsägelse-/körnings nyckel till dessa program visas ett fel meddelande när du migrerar det program-ID: n som har de förutsägelse nycklar som har tilldelats till dem som ägs av andra användare.

Vi rekommenderar att du:
* Meddela medarbetare om migreringen.
* Ta bort alla medarbetare från programmen som visas i fel meddelandet.
* Genomgå migreringsprocessen som bör utföras om du tar bort medarbetare manuellt.
* Tilldela medarbetare som deltagare till din nya redigerings resurs.
* Medarbetare är att migrera och omtilldela förutsägelse resurserna tillbaka till programmen.
OBS! detta leder till en paus i programmet tills förutsägelse resurserna tilldelas på nytt.

En annan lösning här är, innan migreringen ägers, kan medarbetare lägga till app-ägare som deltagare i sina Azure-prenumerationer från Azure Portal. Detta ger ägar åtkomst till resursen för körnings förutsägelse. Om ägaren migrerar med hjälp av den nya prenumerationen som de har lagts till i (som kommer att hittas under en ny klient), kommer detta inte bara att avblockera migreringsprocessen för både medarbetare och app-ägare, men det tillåter en smidig migrering av appar med förutsägelse nyckeln fortfarande tilldelade till att inte förstöra apparna.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Rekommenderade steg för att göra om du är en medarbetare i en app
Om du samarbetar med program och har tilldelat en förutsägelse/körnings nyckel till dessa program visas ett fel när du migrerar som visar de program-ID: n och nyckel Sök vägar som blockerar migreringen.

Vi rekommenderar att du:
* Exportera program som säkerhets kopiering. Detta tillhandahålls som ett valfritt steg i migreringsprocessen.
* Avtilldela förutsägelse resurserna från sidan **hantera > Azure-resurser** .
* Genomgå migreringsprocessen.
* Importera program igen efter migreringen.
* Tilldela om förutsägelse nycklar till sidan program **hantering – > Azure-resurser** .

> [!Note]
> När du importerar tillbaka dina program efter att du har migrerat, kommer de att ha olika app-ID: n och de kommer att skilja sig från de som träffar i produktionen. Du är nu ägare till dessa program.

## <a name="troubleshooting-migration-process"></a>Felsöka migreringsprocessen

När du försöker migrera men inte kan hitta din Azure-prenumeration i list rutan:
* Se till att du har en giltig Azure-prenumeration som har behörighet att skapa Cognitive Services-resurser. Gå till [Azure Portal](https://ms.portal.azure.com) och kontrol lera status för prenumerationen. Om du inte har en sådan, [skapar du en kostnads fri utvärderings version](https://azure.microsoft.com/free/).
* Se till att du är i rätt klient som är associerad med din giltiga prenumeration. Du kan byta innehavare från den ytterligare vänstra avataren i verktygsfältet nedan: ![ Växla klienter.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Om du redan har en befintlig redigerings resurs men inte kan hitta den när du väljer alternativet Använd befintlig redigerings resurs:
* Resursen skapades förmodligen på en annan plats än portalen du har loggat in på. Kontrol lera [Luis redigerings regioner och portalerna](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)
* Skapa en ny resurs från LUIS-portalen i stället

Om du väljer alternativet för att skapa en ny Authoring-resurs och migreringen misslyckades med fel meddelandet "Det gick inte att hämta användarens Azure-information, försök igen senare"
* Din prenumeration kan ha 10 eller fler redigerings resurser per region per prenumeration. I så fall kan du inte skapa en ny redigerings resurs.
* Migrera genom att välja alternativet Använd befintlig redigerings resurs och välj en av de befintliga resurserna som du har under din prenumeration.

Om du ser felet nedan markerar du [rekommenderade steg för att göra om du är ägare till app-avsnittet] ![ migreringen Miss lyckas för ägare](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Om du ser felet nedan kontrollerar du [rekommenderade steg för att göra om du är en medarbetare i ett app-avsnitt] ![ migreringen Miss lyckas för medarbetare](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Nästa steg

* Granska [begrepp](luis-concept-keys.md) om redigerings-och körnings nycklar
* Granska [hur du tilldelar nycklar](luis-how-to-azure-subscription.md) och lägger till [deltagare](luis-how-to-collaborate.md)
