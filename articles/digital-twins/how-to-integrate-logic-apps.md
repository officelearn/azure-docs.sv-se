---
title: Integrera med Logic Apps
titleSuffix: Azure Digital Twins
description: Se hur du ansluter Logic Apps till Azure Digitals dubbla, med hjälp av en anpassad anslutning
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 20959709854f8366cc067437fe86c245fcbc3ef0
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401069"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integrera med Logic Apps med hjälp av en anpassad anslutning

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) är en moln tjänst som hjälper dig att automatisera arbets flöden i appar och tjänster. Genom att ansluta Logic Apps till Azures digitala dubbla API: er, kan du skapa sådana automatiserade flöden kring Azures digitala dubbla och deras data.

Azure Digitals dubbla är för närvarande inte en certifierad (fördefinierad) anslutning för Logic Apps. I stället är den aktuella processen för att använda Logic Apps med Azure Digitals dubbla, att skapa en [**anpassad Logic Apps anslutning**](../logic-apps/custom-connector-overview.md)med hjälp av en [anpassad Azure Digital-Swagger](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) som har ändrats för att fungera med Logic Apps.

I den här artikeln ska du använda [Azure Portal](https://portal.azure.com) för att **skapa en anpassad anslutning** som kan användas för att ansluta Logic Apps till en digital Azure-instans. Sedan skapar du **en Logic-app** som använder den här anslutningen för ett exempel scenario, där händelser som utlöses av en timer automatiskt uppdaterar en dubbla i din Azure Digital-instansen. 

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du **skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ** innan du börjar.

Logga in på [Azure Portal](https://portal.azure.com) med det här kontot.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurera Azure Digitals dubbla instanser

Om du vill ansluta en Azure Digitals-instans till Logic Apps i den här artikeln måste du redan har konfigurerat **Azure Digital-instansen** . 

Om du behöver konfigurera en ny instans nu är det enklaste sättet att köra ett skript exempel för automatisk distribution. Följ instruktionerna i [*How-to: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md) för att konfigurera en ny instans och nödvändig Azure AD-App-registrering. Anvisningarna innehåller också anvisningar för att kontrol lera att du har slutfört varje steg och att du är redo att gå vidare till att använda den nya instansen.

I den här självstudien behöver du följande värden från när du konfigurerar din instans. Om du behöver samla in värdena igen använder du länkarna nedan till motsvarande avsnitt i installations artikeln för att hitta dem i [Azure Portal](https://portal.azure.com).
* Azure Digitals dubbla instans **_värd namn_** ([Sök i portalen](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* ID för Azure AD App Registration- **_program (klient)_** ([Sök i portalen](how-to-set-up-instance-portal.md#collect-important-values))
* ID för Azure AD App Registration **_-katalogen (klient)_** ([Sök i portalen](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>Hämta klient hemlighet för app-registrering

Du måste också skapa en **_klient hemlighet_** för din Azure AD-App-registrering. Det gör du genom att gå till sidan [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Portal (du kan använda den här länken eller leta efter den i portalens Sök fält). Öppna informationen genom att välja registreringen i listan. 

Besök *certifikat och hemligheter* från registrerings menyn och välj *+ ny klient hemlighet*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Portal visning av en Azure AD App-registrering. Det finns en markering runt certifikat och hemligheter på resurs menyn och en markering på sidan runt ny klient hemlighet":::

Ange de värden som du vill ha som beskrivning och förfaller, och tryck sedan på *Lägg till*.
Hemligheten kommer att läggas till i listan över klient hemligheter på sidan *certifikat och hemligheter* . Anteckna värdet för att använda senare (du kan också kopiera det till Urklipp med kopierings ikonen).

### <a name="add-a-digital-twin"></a>Lägg till en digital delad

I den här artikeln används Logic Apps för att uppdatera en dubbel i din Azure Digital-instansen. Om du vill fortsätta måste du lägga till minst en i din instans. 

Du kan lägga till dubbla med [DigitalTwins-API: er](how-to-use-apis-sdks.md), [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)eller [Azure Digitals flätade CLI](how-to-use-cli.md). Detaljerade anvisningar om hur du skapar dubblare med hjälp av dessa metoder finns i [*How-to: hantera digitala dubbla*](how-to-manage-twin.md).

Du behöver det **_dubbla ID: t_** för en som är dubbel i din instans som du har skapat.

## <a name="create-custom-logic-apps-connector"></a>Skapa anpassad Logic Apps-koppling

I det här steget ska du skapa en [anpassad Logic Apps-anslutning](../logic-apps/custom-connector-overview.md) för Azure Digitals dubbla API: er. När du har gjort det kan du ansluta Azure Digital-luren när du skapar en Logic-app i nästa avsnitt.

Gå till sidan [Logic Apps anpassad anslutning](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) i Azure Portal (du kan använda den här länken eller Sök efter den i portalens Sök fält). Tryck på *+ Lägg till*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Sidan Logic Apps anpassad koppling i Azure Portal. Fokus runt knappen Lägg till":::

På sidan *skapa Logic Apps anpassad anslutning* som följer väljer du din prenumeration och resurs grupp och ett namn och en distributions plats för din nya anslutning. Tryck på *Granska + skapa*. Då kommer du till fliken *Granska + skapa* där du kan trycka på *skapa* längst ned för att skapa din resurs.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Fliken Granska + skapa på sidan Skapa Logic Apps anpassad anslutning i Azure Portal. Fokus runt knappen skapa":::

Du kommer till distributions sidan för anslutningen. När distributionen är färdig klickar du på knappen *gå till resurs* för att Visa kopplingens information i portalen.

### <a name="configure-connector-for-azure-digital-twins"></a>Konfigurera anslutnings program för Azure Digitals, dubbla

Sedan konfigurerar du den anslutning som du har skapat för att komma åt Azure Digital-dubbla.

Börja med att ladda ned en anpassad Azure Digital-Swagger som har ändrats för att fungera med Logic Apps. Hämta det **anpassade Azure Digital-swaggers** -exemplet från [den här länken](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) genom att trycka på *Hämta zip* -knappen. Navigera till den hämtade *Azure_Digital_Twins_Custom_Swaggers.zip* -mappen och packa upp den. Den anpassade Swagger för den här självstudien finns på *Azure_Digital_Twins_Custom_Swaggers\LogicApps\preview\2020-05-31-preview\digitaltwins.jspå*.

Gå sedan till sidan med din kopplings översikt i [Azure Portal](https://portal.azure.com) och tryck på *Redigera*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Sidan översikt för den koppling som skapades i föregående steg. Fokusera runt knappen Redigera":::

På sidan *redigera Logic Apps anpassad anslutning* som följer konfigurerar du den här informationen:
* **Anpassade anslutningar**
    - API-slut punkt: REST (lämna standard)
    - Import läge: OpenAPI-fil (lämna standard)
    - Fil: det här är den anpassade Swagger-fil som du laddade ned tidigare. Tryck på *Importera*, leta upp filen på din dator (*Azure_Digital_Twins_Custom_Swaggers\LogicApps\preview\2020-05-31-preview\digitaltwins.jspå*) och tryck på *Öppna*.
* **Allmän information**
    - Ikon, bakgrunds färg för ikon, beskrivning: Fyll i de värden som du vill ha.
    - Schema: HTTPS (lämna standard)
    - Värd: *värd namnet* för din Azure Digital-instans.
    - Bas-URL:/(lämna standard)

Tryck sedan på knappen *säkerhet* längst ned i fönstret för att fortsätta till nästa konfigurations steg.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Skärm bild av slutet av sidan redigera Logic Apps anpassad anslutning. Fokusera på knappen för att fortsätta till säkerhet":::

I säkerhets steget trycker du på *Redigera* och konfigurerar den här informationen:
* **Autentiseringstyp**: OAuth 2,0
* **OAuth 2,0**:
    - Identitetsprovider: Azure Active Directory
    - Klient-ID: *program-ID (klient)* för din Azure AD-App-registrering
    - Klient hemlighet: den *klient hemlighet* som du skapade i [*krav*](#prerequisites) för din Azure AD-App-registrering
    - Inloggnings-URL: https://login.windows.net (lämna standard)
    - Klient-ID: *katalog (klient)-ID* för din Azure AD App-registrering
    - Resurs-URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Omfattning: Directory. AccessAsUser. all
    - Omdirigerings-URL: (lämna kvar standardvärdet för tillfället)

Observera att fältet omdirigerings-URL står för att *Spara det anpassade anslutnings programmet för att generera omdirigerings-URL*. Gör detta nu genom att trycka på *Uppdatera koppling* längst upp i fönstret för att bekräfta dina anslutnings inställningar.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Skärm bild av den övre delen av sidan Redigera Logic Apps anpassad anslutning. Markera med knappen uppdatera koppling":::

<!-- Success message? didn't see one -->

Gå tillbaka till fältet omdirigerings-URL och kopiera värdet som har genererats. Du kommer att använda den i nästa steg.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Fältet omdirigerings-URL på sidan Redigera Logic Apps anpassad anslutning har nu värdet https://logic-apis-westus2.consent.azure-apim.net/redirect . Knappen för att kopiera värdet är markerad.":::

Detta är all information som krävs för att skapa din anslutning (du behöver inte fortsätta med den senaste säkerheten till definitions steget). Du kan stänga fönstret *redigera Logic Apps anpassade anslutnings* fönster.

>[!NOTE]
>På sidans översikts sida där du ursprungligen nådde *Redigera*, Observera att om du trycker på *Redigera* igen startas hela processen om att ange dina konfigurations alternativ. Värdena fylls inte i automatiskt från den senaste gången du gick igenom den, så om du vill spara en uppdaterad konfiguration med ändrade värden måste du ange alla andra värden och undvika att de skrivs över av standardvärdena.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Bevilja anslutnings behörigheter i Azure AD-appen

Använd sedan den anpassade kopplingens *URL* -värde som du kopierade i det sista steget för att ge anslutnings behörighet i din Azure AD-App-registrering.

Gå till sidan [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Portal och välj registreringen i listan. 

Lägg till en URI under *autentisering* från registrerings menyn.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Sidan autentisering för appens registrering i Azure Portal. Autentisering i menyn är markerat och på sidan markeras knappen Lägg till en URI."::: 

Ange den anpassade kopplingens *omdirigerings-URL* till det nya fältet och tryck på ikonen *Spara* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Sidan autentisering för appens registrering i Azure Portal. Den nya omdirigerings-URL: en är markerad och knappen Spara för sidan.":::

Nu är du färdig med att konfigurera en anpassad anslutning som kan komma åt Azure Digitals dubbla API: er. 

## <a name="create-logic-app"></a>Skapa en logikapp

Därefter skapar du en Logic-app som använder din nya anslutning för att automatisera Azure Digitals dubbla uppdateringar.

Gå till sidan [Logic Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2Fworkflows) i Azure Portal (du kan använda den här länken eller leta efter den i portalens Sök fält). Tryck på *skapa Logic app*.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Sidan Logic Apps i Azure Portal. Fokus runt knappen skapa Logic app":::

På sidan *Logic app* som följer väljer du din prenumeration och resurs grupp och ett namn och en distributions plats för din nya Logic-app. Tryck på *Granska + skapa*. Då kommer du till fliken *Granska + skapa* där du kan trycka på *skapa* längst ned för att skapa din resurs.

Du kommer till sidan distribution för Logic app. När distributionen är färdig klickar du på knappen *gå till resurs* för att fortsätta till *Logic Apps designer*där du kan fylla i arbets flödets logik.

### <a name="design-workflow"></a>Design arbets flöde

I *Logic Apps designer*, under *börja med en gemensam utlösare*, väljer du _**upprepning**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Sidan Logic Apps designer i Azure Portal. Fokusera runt den vanliga utlösaren upprepning":::

På sidan *Logic Apps designer* som följer ändrar du **upprepnings** frekvensen till den *andra*, så att händelsen utlöses var tredje sekund. Detta gör det enkelt att se resultaten senare utan att behöva vänta mycket lång tid.

Tryck på *+ nytt steg*.

Då öppnas rutan *Välj en åtgärd* . Växla till fliken *anpassad* . Du bör se din anpassade anslutning från tidigare i den översta rutan.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Skapa ett flöde i Logic Apps designer i Azure Portal. Fliken anpassad är markerad i rutan Välj en åtgärd. Användarens anpassade anslutning från tidigare visas i rutan, med en markering runt den.":::

Välj den för att visa en lista över API: er som finns i den anslutningen. Använd Sök fältet eller bläddra i listan och välj **DigitalTwins_Add**. (Detta är det API som används i den här artikeln, men du kan också välja andra API som ett giltigt val för en Logic Apps anslutning).

Du kan uppmanas att logga in med dina Azure-autentiseringsuppgifter för att ansluta till anslutningen. Om du får en dialog ruta som *efterfrågats* , följer du anvisningarna för att ge appen ett medgivande och acceptera.

I rutan ny *DigitalTwinsAdd* fyller du i fälten enligt följande:
* ID: Fyll i det *dubbla ID: t* för den digitala dubbla i din instans som du vill att Logic-appen ska uppdatera.
* Objekt-1: det här fältet anger du den text som den valda API-förfrågan kräver. För *DigitalTwinsUpdate*är den här texten i form av JSON-patch-kod. Mer information om att strukturera en JSON-korrigering för att uppdatera din dubbla finns i avsnittet [Uppdatera ett digitalt](how-to-manage-twin.md#update-a-digital-twin) avsnitt med *anvisningar: hantera digitala dubbla*.
* API-version: i den aktuella offentliga för hands versionen är detta värde *2020-05-31-för hands* version

Tryck på *Spara* i Logic Apps designer.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Appen har visats i Logic app Connector. Rutan DigitalTwinsAdd fylls med de värden som beskrivs ovan, inklusive ett exempel på en JSON-fil. Knappen Spara för fönstret är markerad.":::

## <a name="query-twin-to-see-the-update"></a>Fråga till dubbla för att se uppdateringen

Nu när din Logi Kap par har skapats, bör den dubbla uppdaterings händelsen som du definierade i Logic Apps designer inträffa vid en upprepning var tredje sekund. Det innebär att du i tre sekunder kan fråga din dubbla och se dina nya korrigerade värden.

Du kan fråga din dubbla via din metod för val (till exempel en [anpassad klient app](tutorial-command-line-app.md), [exempel program för Azure Digitals Utforskare](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), SDK: [er och API: er](how-to-use-apis-sdks.md)eller [CLI](how-to-use-cli.md)). 

Mer information om hur du frågar din Azure Digital-instansen finns i [*How-to: fråga den dubbla grafen*](how-to-query-graph.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en Logic-app som regelbundet uppdaterar en dubbel i din Azure Digital-instans med en korrigerings fil som du har angett. Du kan prova att välja andra API: er i den anpassade anslutningen för att skapa Logic Apps för en rad olika åtgärder på din instans.

Om du vill veta mer om vilka API-åtgärder som är tillgängliga och vilka uppgifter de behöver kan du gå [*till så här: använda Azures digitala dubbla API: er och SDK: er*](how-to-use-apis-sdks.md).
