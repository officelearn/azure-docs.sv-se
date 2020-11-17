---
title: 'Självstudie: köra en Spelbok i Azure Sentinel'
description: Använd den här självstudien för att hjälpa dig att använda säkerhets spel böcker i Azure Sentinel för att ställa in automatiserade hot svar på säkerhetsrelaterade problem.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: b6fd26b4965b92f5f06a008d67e2d585fd1b41b7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94652084"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Självstudie: Konfigurera automatiserade hot svar i Azure Sentinel



Den här självstudien hjälper dig att använda säkerhets spel böcker i Azure Sentinel för att ställa in automatiserade hot svar på säkerhetsrelaterade problem som identifieras av Azure Sentinel.


> [!div class="checklist"]
> * Förstå spel böcker
> * Skapa en Spelbok
> * Kör en Spelbok
> * Automatisera hot svar


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Vad är en säkerhets Spelbok i Azure Sentinel?

En säkerhets Spelbok är en samling procedurer som kan köras från Azure Sentinel som svar på en avisering. En säkerhets Spelbok kan hjälpa dig att automatisera och dirigera ditt svar och kan köras manuellt eller ställas in så att det körs automatiskt när vissa aviseringar utlöses. Säkerhets spel böcker i Azure Sentinel baseras på [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vilket innebär att du får alla Power, anpassningsbarhet och inbyggda mallar av Logic Apps. Varje Spelbok skapas för den angivna prenumerationen som du väljer, men när du tittar på sidan spel böcker visas alla spel böcker i alla valda prenumerationer.

> [!NOTE]
> Spel böcker utnyttjar Azure Logic Apps, och därför gäller avgifterna. Besök prissidan för [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) om du vill ha mer information.

Om du till exempel oroar dig om angripare som har åtkomst till dina nätverks resurser kan du ange en avisering som söker efter skadliga IP-adresser som har åtkomst till nätverket. Sedan kan du skapa en Spelbok som gör följande:
1. När aviseringen utlöses öppnar du en biljett i ServiceNow eller något annat system för IT-biljetter.
2. Skicka ett meddelande till din säkerhets åtgärds kanal i Microsoft Teams eller slack för att se till att dina säkerhetsanalytiker är medvetna om incidenten.
3. Skicka all information i aviseringen till den erfarna nätverks administratören och säkerhets administratören. E-postmeddelandet innehåller också två alternativ knappar för användare **blockera** eller **Ignorera**.
4. Spelbok fortsätter att köras när ett svar tas emot från administratörerna.
5. Om administratörer väljer **blockera** BLOCKERAs IP-adressen i brand väggen och användaren inaktive ras i Azure AD.
6. Om administratörer väljer **Ignorera**, stängs aviseringen i Azure Sentinel och incidenten stängs i ServiceNow.

Säkerhets spel böcker kan köras antingen manuellt eller automatiskt. Att köra dem manuellt innebär att när du får en avisering kan du välja att köra en Spelbok på begäran som ett svar på den valda aviseringen. Att köra dem innebär automatiskt att när du skapar korrelations regeln anger du att den automatiskt ska köra en eller flera spel böcker när aviseringen utlöses.


## <a name="create-a-security-playbook"></a>Skapa en säkerhets Spelbok

Följ de här stegen för att skapa en ny säkerhets Spelbok i Azure Sentinel:

1. Öppna instrument panelen för **Azure Sentinel** .
2. Under **konfiguration** väljer du **spel böcker**.

   ![Logikapp](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. På sidan **Azure Sentinel-spel böcker** klickar du på knappen **Lägg till** .

   ![Skapa en logikapp](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. På sidan **skapa logisk app** , ange den begärda informationen för att skapa din nya Logic app och klicka på **skapa**. 

5. I [**Logic App Designer**](../logic-apps/logic-apps-overview.md)väljer du den mall som du vill använda. Om du väljer en mall som kräver autentiseringsuppgifter måste du ange dem. Du kan också skapa en ny tom Spelbok från grunden. Välj **Tom Logic app**. 

   ![Skärm bild som visar den tomma Logic app-panelen.](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Du kommer till Logic App Designer där du kan bygga en ny eller redigera mallen. Mer information om hur du skapar en Spelbok med [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Om du skapar en tom Spelbok i fältet **Sök alla anslutningar och utlösare** skriver du *Azure Sentinel* och väljer **när ett svar på en Azure Sentinel-avisering utlöses**. <br>När den har skapats visas den nya Spelbok i listan **spel böcker** . Om den inte visas klickar du på **Uppdatera**.

1. Använd funktionen **Get entities** , som gör att du kan hämta relevanta entiteter från listan **entiteter** , till exempel konton, IP-adresser och värdar. På så sätt kan du köra åtgärder på vissa entiteter.

7. Nu kan du definiera vad som händer när du utlöser spelboken. Du kan lägga till en åtgärd, ett logiskt villkor, ett växlings Falls villkor eller slingor.

   ![Designer för logikappar](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Så här kör du en säkerhets Spelbok

Du kan köra en Spelbok på begäran.

Så här kör du en Spelbok på begäran:

1. På sidan **incidenter** väljer du en incident och klickar på **Visa fullständig information**.

2. På fliken **aviseringar** klickar du på den avisering som du vill köra Spelbok på och bläddrar sedan hela vägen till höger och klickar på **Visa spel böcker** och väljer en Spelbok som ska **köras** från listan över tillgängliga spel böcker i prenumerationen. 



## <a name="automate-threat-responses"></a>Automatisera hot svar

SIEM/SOC-team kan vara översvämmas med regelbundna säkerhets aviseringar. Volymen av de aviseringar som genereras är så stor att de tillgängliga säkerhets administratörerna är överbelastade. Detta resulterar i en alltför ofta i situationer där många aviseringar inte kan undersökas, vilket gör att organisationen blir sårbar för attacker som går förlorade. 

Många, om inte de flesta, av dessa aviseringar följer återkommande mönster som kan åtgärdas med hjälp av bestämda och definierade reparations åtgärder. Med Azure Sentinel kan du definiera din reparation i spel böcker. Det är också möjligt att ställa in real tids automatisering som en del av din Spelbok-definition så att du fullständigt kan automatisera ett definierat svar på specifika säkerhets aviseringar. Genom att använda real tids automatisering kan svars grupper avsevärt minska sin arbets belastning genom att helt automatisera de rutinmässiga svaren på återkommande typer av aviseringar, så att du kan koncentrera dig på unika aviseringar, analysera mönster, Hot jakt och mycket annat.

Automatisera svar:

1. Välj den avisering som du vill automatisera svaret för.
1. På sidan **Redigera aviserings regel** under **real tids automatisering** väljer du den **Utlös ande Spelbok** som du vill köra när den här varnings regeln matchas.
1. Välj **Spara**.

   ![automatisering i real tid](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du kör en Spelbok i Azure Sentinel. Fortsätt till [hur du proaktivt söker efter hot](hunting.md) med Azure Sentinel.