---
title: "Anslut till Dynamics 365 (online) från Azure Logic Apps | Microsoft Docs"
description: "Skapa logik app arbetsflöden som hanterar Dynamics 365 (online) enheter via API som tillhandahålls av Dynamics 365-kopplingen"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Anslut till Dynamics 365 från logik app arbetsflöden

Du kan ansluta till Dynamics 365 (online) och skapa användbart business-flöden som skapar posterna, uppdatera objekt eller returnera en lista över poster med Logic Apps. Med Dynamics 365-kopplingen kan du:

* Skapa ditt företag flödet som baseras på de data som du får från Dynamics 365 (online).
* Använd åtgärder som får ett svar och sedan se utdata för andra åtgärder. När ett objekt uppdateras i Dynamics 365 (online), kan du skicka ett e-postmeddelande med hjälp av Office 365.

Det här avsnittet visar hur du skapar en logikapp som skapar en uppgift i Dynamics 365 när en ny lead skapas i Dynamics 365.

## <a name="prerequisites"></a>Förutsättningar
* Ett Azure-konto.
* En Dynamics 365 (online)-konto.

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Skapa en uppgift när en ny lead skapas i Dynamics 365

1.  [Logga in på Azure](https://portal.azure.com).

2.  Skriv i sökrutan Azure `Logic apps`, och tryck på RETUR.

      ![Sök efter Logic Apps](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  Under **logikappar**, klickar du på **Lägg till**.

      ![Lägg till LogicApp](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  Om du vill skapa logikappen Slutför den **namn**, **prenumeration**, **resursgruppen**, och **plats** fält och klicka sedan på **skapa**.

5.  Välj den nya logikappen. När du får den **distributionen lyckades** meddelande, klicka på **uppdatera**.

6.  Under **utvecklingsverktyg**, klickar du på **logik App Designer**. I listan klickar du på **tom Logikapp**.

7.  I sökrutan skriver `Dynamics 365`. Dynamics 365 utlösare listan Välj **Dynamics 365 – när en post skapas**.

8.  Om du uppmanas att logga in på Dynamics 365 göra.

9.  Ange följande information i utlösaren-information:

  * **Organisationens namn**. Välj Dynamics 365-instans som du vill att lyssna på logikappen.

  * **Entitetsnamnet**. Välj den enhet som du vill att lyssna på. Den här händelsen fungerar som en utlösare för att starta logikappen. 
  I den här genomgången **leder** är markerad.

  * **Hur ofta du vill söka efter objekt?** Dessa värden anger hur ofta logikappen söker efter uppdateringar som rör utlösaren. Standardinställningen är att söka efter uppdateringar tre minuters mellanrum.

    * **Frekvensen**. Välj sekunder, minuter, timmar eller dagar.

    * **Intervallet**. Ange antal sekunder, minuter, timmar eller dagar som du vill skicka före nästa kontrollen.

      ![Logik Apputlösare information](./media/connectors-create-api-crmonline/trigger-details.png)

10. Klicka på **nytt steg**, och klicka sedan på **lägga till en åtgärd**.

11. I sökrutan skriver `Dynamics 365`. Välj i listan åtgärder **Dynamics 365 – skapa en ny post**.

12. Ange följande information:

    * **Organisationens namn**. Välj Dynamics 365-instans där du vill att flödet för att skapa posten. 
    Observera att den här instansen inte behöver vara samma instans där händelsen utlöses från.

    * **Entitetsnamnet**. Välj den enhet som du vill skapa en post när händelsen utlöses. 
    I den här genomgången **uppgifter** är markerad.

13. Klicka i den **ämne** som visas. Du kan välja något av dessa fält dynamiskt innehåll listan som visas:

    * **Efternamn**. Markera det här fältet infogar efternamn för lead i ämnesfältet för aktiviteten när uppgiftsposten skapas.
    * **Avsnittet**. Att välja det här fältet infogar fältet Ämne för lead i ämnesfältet för aktiviteten när uppgiftsposten har skapats. 
    Klicka på **avsnittet** att lägga till som att den **ämne** rutan.

      ![Logik App skapa nya registrera information](./media/connectors-create-api-crmonline/create-record-details.png)

14. På verktygsfältet logik App Designer **spara**.

    ![Logik App Designer verktygsfältet spara](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. Starta Logikappen, klicka på **kör**.

    ![Logik App Designer verktygsfältet spara](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Nu skapa en lead-post i Dynamics 365 för försäljning och se din flödet i praktiken!

## <a name="set-advanced-options-for-a-logic-app-step"></a>Ange avancerade alternativ för en logik app steg

Klicka på Ange filtrera data i en logik app steg **visa avancerade alternativ** steget och sedan lägga till ett filter eller ordning av frågan.

Du kan till exempel använda filterfråga för att hämta endast aktiva konton och ordning genom att namnet på kontot. Om du vill utföra den här uppgiften, ange OData-filterfråga `statuscode eq 1`, och välj **kontonamn** från listan över dynamiskt innehåll. Mer information: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) och [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Logikapp avancerade alternativ](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Rekommenderade metoder när du använder avancerade alternativ

När du lägger till ett värde till ett fält måste du matcha fälttypen om du skriver ett värde eller välj ett värde från listan över dynamiskt innehåll.

Fälttyp  |Hur du ska använda detta  |Var du hittar  |Namn  |Datatyp  
---------|---------|---------|---------|---------
Textfält|Textfält kräver en rad med text eller dynamiskt innehåll som är ett fält av typen text. Exempel inkluderar fälten kategori och underkategori.|Inställningar > anpassningar > Anpassa systemet > enheter > aktivitet > fält |category |Rad med Text        
Heltalsfält | Vissa fält kräver heltal eller dynamiskt innehåll som är av typen Integer. Exempel: procent färdigt och varaktighet. |Inställningar > anpassningar > Anpassa systemet > enheter > aktivitet > fält |värdet |Heltal         
Datumfält | Vissa fält kräver ett datum som anges i formatet åååå-mm-dd eller dynamiskt innehåll som är ett fält av typen date. Exempel är skapad, startdatum, verklig Start senast på håller tid, Verkligt slut och förfallodatum. | Inställningar > anpassningar > Anpassa systemet > enheter > aktivitet > fält |createdon |Datum och tid
Ange fälten som kräver både en post-ID och sökning |Vissa fält som refererar till en annan entitetspost kräver både post-ID och typen sökning. |Inställningar > anpassningar > Anpassa systemet > enheter > konto > fält  | accountid  | Primär nyckel

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Fler exempel på fält som kräver en post-ID och ett sökning skriver
Expandera i föregående tabell följer fler exempel på fält som inte fungerar med värden som väljs från listan över dynamiskt innehåll. I stället kräver dessa fält båda ID och sökning posttyp in i fälten i PowerApps.  
* Ägare och typen ägare. Fältet ägare måste vara en giltig användare eller det team-post-ID. Ägaren måste vara antingen **systemusers** eller **team**.
* Kund och kunden. Fältet måste vara ett giltigt konto eller kontakta post-ID. Ägaren måste vara antingen **konton** eller **kontakter**.
* Angående och för typen. Fältet Ämne måste vara en giltig post-ID, till exempel ett konto eller kontakta post-ID. Om typen måste vara sökning-typ för posten som **konton** eller **kontakter**.

I följande exempel för uppgiften Skapa åtgärd lägger till en kontopost som motsvarar post-ID lägger till den i fältet ämne av uppgiften.

![Flödet recordId och typ för kontot](./media/connectors-create-api-crmonline/recordid-type-account.png)

Det här exemplet ger också aktiviteten till en viss användare baserat på användarens post-ID.

![Flödet recordId och typ för kontot](./media/connectors-create-api-crmonline/recordid-type-user.png)

Se följande avsnitt för att hitta en post-ID: *post-ID: T*

## <a name="find-the-record-id"></a>Hitta post-ID

1. Öppna en post, till exempel en kontopost.

2. Klicka på i verktygsfältet Åtgärder **Pop-ut** ![popout post](./media/connectors-create-api-crmonline/popout-record.png).
Alternativt verktygsfältet åtgärder för att kopiera hela Webbadressen till e-postprogram, klickar du på **e-post en länk**.

   POST-ID visas mellan % 7b och %7 d kodning tecken i URL: en.

   ![Flödet recordId och typ för kontot](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Felsökning
Visa statusinformation om händelsen för att felsöka en misslyckad steg i en logikapp.

1. Under **Logikappar**, Välj din logikapp och klicka sedan på **översikt**. 

   I sammanfattningen visas samt Körstatus för logikappen. 

   ![Logikapp Körstatus](./media/connectors-create-api-crmonline/tshoot1.png)

2. Klicka på misslyckade händelsen om du vill visa mer information om eventuella misslyckade körs. Klicka på steget ett steg som misslyckades.

   ![Expandera steg som misslyckades](./media/connectors-create-api-crmonline/tshoot2.png)

   Steg-information visas och kan hjälpa dig att felsöka orsaken till felet.

   ![Det gick inte steg-information](./media/connectors-create-api-crmonline/tshoot3.png)

Läs mer om hur du felsöker logikappar [diagnostisera fel i logik app](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/crm/). 

## <a name="next-steps"></a>Nästa steg
Utforska andra tillgängliga kopplingar i Logic Apps på vår [API: er listan](apis-list.md).
