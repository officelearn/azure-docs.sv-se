---
title: Migrera till en Azure-resurs redigerings nyckel
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du migrerar Language Understanding (LUIS) som redigerar autentisering från ett e-postkonto till en Azure-resurs.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 455c7d66748740ae6e2cc11c6a44bbf30c1cbced
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018845"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrera till en Azure-resurs redigerings nyckel

Language Understanding (LUIS) som redigerar autentiseringen har ändrats från ett e-postkonto till en Azure-resurs. Även om det inte krävs för närvarande, kommer växling till en Azure-resurs att verkställas i framtiden.


## <a name="what-is-migration"></a>Vad är migrering?

Migrering är en process för att ändra auktorisering av autentisering från ett e-postkonto till en Azure-resurs. Ditt konto kommer att länkas till en Azure-prenumeration och en Azure Authoring-resurs när du har migrerat. *Alla LUIS-användare (ägare eller medarbetare) behöver eventuellt migrera.*

Migreringen måste göras från LUIS-portalen. Om du skapar redigerings nycklarna med hjälp av LUIS CLI måste du till exempel slutföra migreringsprocessen i LUIS-portalen. Du kan fortfarande ha samförfattare till dina program efter migreringen, men de läggs till på Azure-resurs nivån i stället för på program nivån.

> [!Note]
> Innan migreringen kallas _medarbetares medarbetare_ på Luis app-nivå. Efter migreringen används Azure-rollen för _deltagare_ för samma funktion på Azure-resurs nivån.

## <a name="note-before-you-migrate"></a>Observera innan du migrerar

* Du måste migrera redigerings upplevelsen med **nov, 2, 2020**. 
* Migrering är en enkelriktad process. Du kan inte gå tillbaka när du har migrerat.
* Program migreras automatiskt med dig om du är ägare till programmet.
* Ägaren kan inte välja en delmängd av appar som ska migreras och processen kan inte ångras.
* Programmen kommer att försvinna från medarbetarens sida efter att ägaren migreras.
* Ägare uppmanas att skicka e-postmeddelanden till medarbetare för att informera om migreringen.
* Program kommer inte att migreras med dig om du är en medarbetare i programmet.
* Det finns inget sätt för en ägare att veta att medarbetare har migrerat.
* Migrering samlar inte automatiskt in medarbetare och flyttar eller lägger till dem i Azure Authoring-resursen. Appens ägare är den som måste slutföra det här steget efter migreringen. Det här steget kräver [behörighet till Azure Authoring-resursen](./luis-how-to-collaborate.md).
* När medarbetare har tilldelats till Azure-resursen måste de migrera till åtkomst till program. Annars har de ingen åtkomst till att redigera programmen.
* Det går inte att lägga till en migrerad användare som en medarbetare för programmet.
* Om du äger förutsägelse nycklar som tilldelas program som ägs av en annan användare blockerar detta migreringen för både ägare och medarbetare. Se rekommendationerna längre fram i den här artikeln.

> [!Note]
> Om du behöver skapa en förutsägelse körnings resurs finns det [en separat process](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) för att skapa den.

## <a name="migration-prerequisites"></a>Krav för migrering

* Du måste vara associerad med en giltig Azure-prenumeration. Be din klient administratör att lägga till dig på prenumerationen eller [Registrera dig för en kostnads fri](https://azure.microsoft.com/free/cognitive-services).
* Du måste skapa en LUIS Azure Authoring-resurs från LUIS-portalen eller från Azure Portal. Att skapa en Authoring-resurs från LUIS-portalen är en del av det migreringsjobb som beskrivs i nästa avsnitt.
* Om du är en medarbetare i program, migreras inte programmen automatiskt. Vi rekommenderar att du säkerhetskopierar de här programmen genom att exportera dem eller genom att använda [export-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Du kan importera appen tillbaka till LUIS efter migreringen. Import processen skapar en ny app med ett nytt app-ID som du är ägare till.
* Om du är ägare till programmet behöver du inte exportera dina appar eftersom de migreras automatiskt. Vi rekommenderar att du sparar varje apps samarbets lista. En e-postmall som har den här listan tillhandahålls som en del av migreringsprocessen.


|Portalen|Syfte|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| Skapa förutsägelse-och redigerings resurser.<br> Tilldela deltagare i resurser.|
|[LUIS](https://www.luis.ai)| Migrera till nya redigerings resurser.<br> Skapa nya redigerings resurser i migreringsjobbet.<br> Tilldela eller ta bort tilldelningen av prognoser och redigering av resurser till appar från sidan **Hantera**  >  **Azure-resurser** . <br> Flytta program från en redigerings resurs till en annan.  |

> [!Note]
> Redigering av LUIS-appen är kostnads fri, som anges av F0-nivån. Läs [mer om pris nivåer](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Migreringsanvisningar

1. I LUIS-portalen som du arbetar med kan du starta migreringsprocessen från **Azure** -ikonen i det övre verktygsfältet.

   > [!div class="mx-imgBorder"]
   > ![Migrerings ikon](./media/migrate-authoring-key/migration-button.png)

2. I popup-fönstret migrering kan du fortsätta migreringen eller migrera senare. Välj **migrera nu**.

   > [!div class="mx-imgBorder"]
   > ![Första popup-fönstret i migreringsprocessen där du väljer migrera nu](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Om någon av dina appar har medarbetare, uppmanas du att skicka ett e-postmeddelande som gör det möjligt för dem att veta om migreringen. Detta är ett valfritt steg.

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

4. Om du är en medarbetare i ett program uppmanas du att exportera en kopia av apparna genom att välja det här alternativet under migreringen. Detta är ett valfritt steg.

   Om du väljer alternativet visas följande sida. Välj nedladdnings knapparna till vänster för att exportera de appar som du vill använda. Du kan importera dessa appar tillbaka när du har migrerat, eftersom de inte automatiskt migreras med dig.

   > [!div class="mx-imgBorder"]
   > ![Prompt för att exportera dina program.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Du kan välja att skapa en ny LUIS Authoring-resurs eller migrera till en befintlig redigerings resurs om du redan har skapat en från Azure. Välj det alternativ som du vill använda genom att välja någon av följande knappar.

   > [!div class="mx-imgBorder"]
   > ![Knappar för att skapa en ny redigerings resurs och använda en befintlig redigerings resurs](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Skapa en ny redigerings resurs från LUIS som ska migreras

Om du vill skapa en ny redigerings resurs väljer du **Skapa ny redigerings resurs** och anger följande information i nästa fönster. Välj sedan **Done** (Klar).

> [!div class="mx-imgBorder"]
> ![Fönster för att skapa en redigerings resurs](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Klient** organisations namn: klienten som din Azure-prenumeration är associerad med. Detta ställs in som standard till den klient som du använder just nu. Du kan växla mellan klienter genom att välja den högra avataren, som innehåller dina initialer.
* **Resurs namn**: ett eget namn som du väljer. Den används som en del av URL: en för din redigering och förutsägelse slut punkts frågor.
* **Prenumerations namn**: den prenumeration som ska associeras med resursen. Om du har mer än en prenumeration som tillhör din klient väljer du den som du vill använda i list rutan.
* **Namn på Azure-resurs grupp**: ett namn på en anpassad resurs grupp som du väljer i den nedrullningsbara listan. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering.

Observera att du kan ha 10 kostnads fria redigerings resurser per region, per prenumeration. Om din prenumeration har fler än 10 redigerings resurser i samma region kan du inte skapa någon ny.

När en redigerings resurs skapas visas meddelandet lyckades. Välj **Stäng** för att stänga popup-fönstret.

  > [!div class="mx-imgBorder"]
  > ![Meddelande som anger att din redigerings resurs har skapats](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>Använd befintlig redigerings resurs för att migrera

Om din prenumeration redan är kopplad till en LUIS som redigerar Azure-resurs, eller om du har skapat en resurs från Azure Portal och du vill migrera till den i stället för att skapa en ny, väljer du **Använd befintlig redigerings resurs**. Ange följande information i nästa fönster och välj sedan **slutförd**.

> [!div class="mx-imgBorder"]
>![Fönster för att ändra en befintlig redigerings resurs](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Klient** organisations namn: klienten som din Azure-prenumeration är associerad med. Detta ställs in som standard till den klient som du använder just nu.
* **Prenumerations namn**: den prenumeration som ska associeras med resursen. Om du har mer än en prenumeration som tillhör din klient väljer du den som du vill använda i list rutan.
* **Resurs namn**: den redigerings resurs som du vill migrera till.

> [!Note]
> Om du inte kan se din redigerings resurs i list rutan ser du till att du har skapat den på rätt plats enligt LUIS-portalen där du är inloggad. Kontrol lera också att det du har skapat är en redigerings resurs och inte en förutsägelse resurs.


Verifiera ditt redigerings resurs namn och välj knappen **migrera** .

> [!div class="mx-imgBorder"]
> ![För att välja en redigerings resurs, där knappen migrera nu är tillgänglig](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

Ett meddelande om lyckad visas. Välj **Stäng** för att stänga popup-fönstret.

> [!div class="mx-imgBorder"]
> ![Meddelande som säger att din redigerings resurs har migrerats](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Använda appar efter migrering

Efter migreringen kommer alla dina LUIS-appar för vilka du är ägaren nu att tilldelas en enda LUIS-Authoring-resurs.

I listan **Mina appar** visas appar som migrerats till den nya redigerings resursen. Innan du får åtkomst till dina appar väljer du resursen prenumeration och LUIS Authoring för att se de appar som du kan redigera.

 > [!div class="mx-imgBorder"]
 > ![Rutor för prenumerations-och redigerings resurs](./media/create-app-in-portal-select-subscription-luis-resource.png)

Du behöver inte känna till redigerings resursens nyckel för att fortsätta redigera dina appar i LUIS-portalen.

Om du planerar att redigera dina appar program mässigt behöver du redigera nyckel värden. Dessa värden visas på sidan **Hantera**  >  **Azure-resurser** på Luis-portalen. De är också tillgängliga i Azure Portal på resursens sidan **nycklar** . Du kan också skapa fler redigerings resurser och tilldela dem från samma sida.

 > [!div class="mx-imgBorder"]
 > ![Sida för att hantera redigerings resurser](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>Lägga till deltagare i att skriva resurser

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Lär dig [hur du lägger till deltagare](luis-how-to-collaborate.md) på din redigerings resurs. Deltagare kommer att ha åtkomst till alla program i den resursen.

Du kan lägga till deltagare till redigerings resursen från Azure Portal på sidan **Access Control (IAM)** för resursen. Mer information finns i [lägga till deltagare i din app](luis-how-to-collaborate.md).

> [!Note]
> Om ägaren av LUIS-appen migrerat och lagt till medarbetaren som deltagare på Azure-resursen, kommer medarbetaren fortfarande inte att ha åtkomst till appen om de också migrerar.

## <a name="luis-portal-migration-reminders"></a>Påminnelser om migrering av LUIS-portalen

[Luis-portalen](https://www.luis.ai) innehåller migreringsprocessen.

Du uppmanas att migrera om båda dessa villkor är uppfyllda:
* Du har appar i systemet för e-postautentisering för redigering.
* Du är appens ägare.

Varje vecka uppmanas du att migrera dina appar. Du kan stänga det här fönstret utan att migrera. Om du vill migrera före nästa schemalagda period kan du starta migreringsprocessen från **Azure** -ikonen i det övre verktygsfältet i Luis-portalen.

## <a name="prediction-resources-blocking-migration"></a>Förutsägelse resurser blockerar migrering
Migreringen påverkar negativt alla program körningar. När du migrerar tas alla medarbetare bort från dina appar och du tas bort som en medarbetare från andra appar. Den här processen innebär att de nycklar som en medarbetare tilldelar också tas bort, vilket kan bryta ditt program om det är i produktion. Detta är orsaken till att vi blockerar migreringen tills du manuellt tar bort medarbetare eller nycklar som har tilldelats dem.

Migreringen blockeras om något av dessa villkor är uppfyllt:

* Du har tilldelat förutsägelse-/körnings resurser i appar som du inte äger.
* Du har andra användare som tilldelar förutsägelse-/körnings resurser till appar som du äger.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>Rekommenderade steg om du är ägare till appen
Om du är ägare till vissa program och du har tilldelats medarbetare som har tilldelats en förutsägelse-/körnings nyckel för dessa program visas ett fel när du migrerar. Felet visar de program-ID: n som har förutsägelse nycklar som har tilldelats till dem som ägs av andra användare.

Vi rekommenderar att du gör följande:
* Meddela medarbetare om migreringen.
* Ta bort alla medarbetare från programmen som visas i fel meddelandet.
* Genomgå migreringsprocessen, vilket bör lyckas om du tar bort medarbetare manuellt.
* Tilldela medarbetare som deltagare till din nya redigerings resurs. Medarbetare migrerar och omtilldelar förutsägelse resurserna tillbaka till programmen. Observera att detta leder till en paus i programmet tills förutsägelse resurserna omtilldelas.

Det finns en annan möjlig lösning här. Innan du migrerar migreringen kan medarbetare lägga till program ägare som deltagare på sina Azure-prenumerationer från Azure Portal. Det här steget ger ägar åtkomst till resursen för körnings förutsägelse. Om ägaren migrerar med hjälp av den nya prenumerationen som de har lagts till i (som kommer att hittas under en ny klient) avblockerar det här steget inte bara migreringsprocessen för både medarbetare och app-ägare. Det ger också en smidig migrering av appar, med förutsägelse nyckeln fortfarande tilldelad till dem, vilket inte bryter apparna.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>Rekommenderade steg om du är en medarbetare i en app
Om du samarbetar med program och har tilldelat en förutsägelse-/körnings nyckel till dessa program visas ett fel när du migrerar. Felet visar en lista över program-ID: n och nyckel Sök vägar som blockerar migreringen.

Vi rekommenderar att du gör följande:
* Exportera program som en säkerhets kopia. Detta är ett valfritt steg i migreringsprocessen.
* Ta bort tilldelningen av förutsägelse resurser från sidan **Hantera**  >  **Azure-resurser** .
* Genomgå migreringsprocessen.
* Importera program igen efter migreringen.
* Tilldela om förutsägelse nycklar till dina program från sidan **Hantera**  >  **Azure-resurser** .

> [!Note]
> När du importerar dina program igen när du har migrerat har de olika app-ID: n. De kommer också att skilja sig från de som lanseras i produktionen. Du är nu ägare till dessa program.

## <a name="troubleshooting-the-migration-process"></a>Felsöka migreringsprocessen

När du försöker migrera men inte kan hitta din Azure-prenumeration i list rutan:
* Se till att du har en giltig Azure-prenumeration som har behörighet att skapa Cognitive Services-resurser. Gå till [Azure Portal](https://ms.portal.azure.com) och kontrol lera status för prenumerationen. Om du inte har ett kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/).
* Se till att du är i rätt klient som är associerad med din giltiga prenumeration. Du kan byta innehavare från avataren till vänster om dina initialer i det här verktygsfältet: ![ verktygsfält där du kan växla klienter](./media/migrate-authoring-key/switch-user-tenant-2.png)

Om du har en befintlig redigerings resurs men inte kan hitta den när du väljer alternativet **Använd befintlig redigerings resurs** :
* Resursen skapades förmodligen på en plats som skiljer sig från den portal där du är inloggad. Kontrol lera [Luis redigerings regioner och portaler](./luis-reference-regions.md#luis-authoring-regions).
* Skapa en ny resurs från LUIS-portalen i stället.

Om du väljer **resurs alternativet Skapa ny redigerare** och migreringen Miss lyckas med fel meddelandet "Det gick inte att hämta användarens Azure-information, försök igen senare":
* Din prenumeration kan ha 10 eller fler redigerings resurser per region, per prenumeration. I så fall kan du inte skapa en ny redigerings resurs.
* Migrera genom att välja alternativet **Använd befintlig redigerings resurs** och välja en av de befintliga resurserna under din prenumeration.

Om du ser följande fel kan du kontrol lera de [rekommenderade stegen om du är ägare till appen](#recommended-steps-if-youre-the-owner-of-the-app).
![Fel som visar migreringen misslyckades för ägare](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Om du ser följande fel kan du kontrol lera de [rekommenderade stegen om du är en medarbetare i en app](#recommended-steps-if-youre-a-collaborator-on-an-app).
![Fel som visar migreringen misslyckades för medarbetare](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Nästa steg

* Granska [begrepp om redigerings-och körnings nycklar](luis-how-to-azure-subscription.md).
* Läs om hur du [tilldelar nycklar](luis-how-to-azure-subscription.md) och [lägger till deltagare](luis-how-to-collaborate.md).
