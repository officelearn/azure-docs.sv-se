---
title: 'Självstudiekurs: Kör en spelbok i Azure Sentinel'
description: 'Självstudiekurs: I den här artikeln beskrivs hur du kör en spelbok i Azure Sentinel.'
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
ms.openlocfilehash: f6adcb978dbe540d3bdd352089d4dde407d0fb4c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77585092"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Självstudiekurs: Konfigurera automatiska hotsvar i Azure Sentinel



Den här självstudien hjälper dig att använda säkerhetsspelböcker i Azure Sentinel för att ställa in automatiska hotsvar på säkerhetsrelaterade problem som identifieras av Azure Sentinel.


> [!div class="checklist"]
> * Förstå spelböcker
> * Skapa en spelbok
> * Kör en spelbok
> * Automatisera hotsvar


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Vad är en säkerhetsspelbok i Azure Sentinel?

En säkerhetsspelbok är en samling procedurer som kan köras från Azure Sentinel som svar på en avisering. En säkerhetsuppspelningsbok kan hjälpa dig att automatisera och dirigera ditt svar och kan köras manuellt eller ställas in så att det körs automatiskt när specifika aviseringar utlöses. Säkerhetsspelböcker i Azure Sentinel baseras på [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), vilket innebär att du får all kraft, anpassningsbarhet och inbyggda mallar för Logic Apps. Varje spelbok skapas för den specifika prenumeration du väljer, men när du tittar på Playbooks-sidan ser du alla spelböcker i alla valda prenumerationer.

> [!NOTE]
> Spelböcker utnyttjar Azure Logic Apps, därför avgifter tillkommer. Besök prissidan för [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) om du vill ha mer information.

Om du till exempel är orolig för att angripare ska komma åt nätverksresurserna kan du ställa in en avisering som söker efter skadliga IP-adresser som kommer åt nätverket. Sedan kan du skapa en spelbok som gör följande:
1. När aviseringen utlöses öppnar du en biljett i ServiceNow eller något annat IT-biljettsystem.
2. Skicka ett meddelande till säkerhetsoperationskanalen i Microsoft Teams eller Slack för att se till att dina säkerhetsanalytiker är medvetna om incidenten.
3. Skicka all information i aviseringen till din seniora nätverksadministratör och säkerhetsadministratör. E-postmeddelandet innehåller också två knappar för användaralternativ **Block** eller **Ignorera**.
4. Spelboken fortsätter att köras efter att ett svar har tagits emot från administratörerna.
5. Om administratörerna väljer **Blockera**blockeras IP-adressen i brandväggen och användaren är inaktiverad i Azure AD.
6. Om administratörerna väljer **Ignorera**stängs aviseringen i Azure Sentinel och incidenten stängs i ServiceNow.

Säkerhetsuppspelningsböcker kan köras antingen manuellt eller automatiskt. Att köra dem manuellt innebär att när du får en avisering kan du välja att köra en spelbok på begäran som svar på den valda aviseringen. Om du kör dem automatiskt innebär det att när du skapar korrelationsregeln ställer du in den så att den automatiskt körs en eller flera spelböcker när aviseringen utlöses.


## <a name="create-a-security-playbook"></a>Skapa en säkerhetsspelbok

Så här skapar du en ny säkerhetsspelbok i Azure Sentinel:

1. Öppna **Azure Sentinel-instrumentpanelen.**
2. Under **Ledning**väljer du **Spelböcker**.

   ![Logikapp](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Klicka på **Lägg till** på sidan Azure Sentinel **- Playbooks.**

   ![Skapa en logikapp](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. På **sidan Skapa logikapp** skriver du den begärda informationen för att skapa den nya logikappen och klickar på **Skapa**. 

5. Välj den mall som du vill använda i [**Logic App Designer.**](../logic-apps/logic-apps-overview.md) Om du väljer en mall som kräver autentiseringsuppgifter måste du ange dem. Du kan också skapa en ny tom spelbok från grunden. Välj **Tom Logikapp**. 

   ![Designer för logikappar](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Du tas till Logic App Designer där du antingen kan skapa nya eller redigera mallen. Mer information om hur du skapar en spelbok med [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Om du skapar en tom spelbok skriver du *Azure Sentinel*i fältet Sök alla kopplingar **och utlösare** och väljer **När ett svar på en Azure Sentinel-avisering utlöses**. <br>När den har skapats visas den nya spelboken i **playbookslistan.** Om den inte visas klickar du på **Uppdatera**.

1. Använd **funktionerna Hämta entiteter,** som gör att du kan hämta relevanta entiteter inifrån listan **Entiteter,** till exempel konton, IP-adresser och värdar. På så sätt kan du köra åtgärder på specifika entiteter.

7. Nu kan du definiera vad som händer när du utlöser spelboken. Du kan lägga till en åtgärd, ett logiskt villkor, växla skiftlägesvillkor eller loopar.

   ![Designer för logikappar](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Så här kör du en säkerhetsspelbok

Du kan köra en spelbok på begäran.

Så här kör du en spelbok på begäran:

1. På **incidentsidan** väljer du en incident och klickar på **Visa fullständig information**.

2. På fliken **Aviseringar** klickar du på den avisering du vill köra spelboken på och bläddrar hela vägen till höger och klickar på **Visa spelböcker** och välj en spelbok för att **köra** från listan över tillgängliga spelböcker i prenumerationen. 



## <a name="automate-threat-responses"></a>Automatisera hotsvar

SIEM/SOC-team kan översvämmas regelbundet med säkerhetsvarningar. Volymen av varningar som genereras är så stor, att tillgängliga säkerhetsadministratörer är överväldigade. Detta resulterar alltför ofta i situationer där många aviseringar inte kan undersökas, vilket gör organisationen sårbar för attacker som går obemärkt förbi. 

Många, om inte de flesta, av dessa aviseringar överensstämmer med återkommande mönster som kan åtgärdas genom specifika och definierade reparationsåtgärder. Azure Sentinel kan du redan definiera din reparation i spelböcker. Det är också möjligt att ställa in automatisering i realtid som en del av spelboksdefinitionen så att du kan automatisera ett definierat svar på vissa säkerhetsvarningar fullt ut. Med hjälp av automatisering i realtid kan svarsteam avsevärt minska sin arbetsbelastning genom att helt automatisera de rutinmässiga svaren på återkommande typer av aviseringar, så att du kan koncentrera dig mer på unika aviseringar, analysera mönster, hotjakt med mera.

Så här automatiserar du svaren:

1. Välj den avisering som du vill automatisera svaret för.
1. På sidan **Redigera varningsregel** under **Automatisering i realtid**väljer du den **utlösta spelbok** som du vill köra när den här aviseringsregeln matchas.
1. Välj **Spara**.

   ![automatisering i realtid](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att köra en spelbok i Azure Sentinel. Fortsätt till [hur du proaktivt jagar efter hot](hunting.md) med Azure Sentinel.


