---
title: Ansluta Configuration Manager till Azure Monitor | Microsoft-dokument
description: Den här artikeln visar stegen för att ansluta Configuration Manager till arbetsytan i Azure Monitor och börja analysera data.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655264"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Ansluta Configuration Manager till Azure Monitor
Du kan ansluta microsoft endpoint Configuration Manager-miljön till Azure Monitor för att synkronisera enhetsinsamlingsdata och referera till dessa samlingar i Azure Monitor och Azure Automation.  

## <a name="prerequisites"></a>Krav

Azure Monitor stöder Configuration Manager nuvarande gren, version 1606 och högre.

>[!NOTE]
>Funktionen för att ansluta Configuration Manager med en Log Analytics-arbetsyta är valfri och är inte aktiverad som standard. Du måste aktivera den här funktionen innan du använder den. Mer information finns i avsnittet [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Översikt över konfiguration

I följande steg sammanfattas stegen för att konfigurera Configuration Manager-integrering med Azure Monitor.  

1. I Azure Active Directory registrerar du Configuration Manager som ett webbprogram och/eller webb-API-app och kontrollerar att du har klient-ID och klienthemlignyckel från registreringen från Azure Active Directory. Se [Använda portal för att skapa Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md) för detaljerad information om hur du utför det här steget.

2. I Azure Active Directory [beviljar du Configuration Manager (den registrerade webbappen) med behörighet att komma åt Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. Lägg till en anslutning med hjälp av Azure **Services-guiden** i Configuration Manager.

4. [Hämta och installera Log Analytics-agenten för Windows](#download-and-install-the-agent) på datorn som kör rollen för tjänsten Configuration Manager-anslutningspunktsplatsen. Agenten skickar Configuration Manager-data till Log Analytics-arbetsytan i Azure Monitor.

5. Importera samlingar [från Configuration Manager](#import-collections) som datorgrupper i Azure Monitor.

6. I Azure Monitor visar du data från Configuration Manager som [datorgrupper](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Bevilja Configuration Manager med behörighet till Log Analytics

I följande procedur *beviljar* du rollen Deltagare i log analytics-arbetsytan till AD-programmet och tjänsthuvudhuvudet som du skapade tidigare för Configuration Manager. Om du inte redan har en arbetsyta läser du [Skapa en arbetsyta i Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) innan du fortsätter. På så sätt kan Configuration Manager autentisera och ansluta till din Log Analytics-arbetsyta.  

> [!NOTE]
> Du måste ange behörigheter på log analytics-arbetsytan för Configuration Manager. Annars visas ett felmeddelande när du använder konfigurationsguiden i Configuration Manager.
>

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.

2. Välj arbetsytan som du vill ändra i listan över Log Analytics-arbetsytor.

3. Välj **Åtkomstkontroll (IAM) i**den vänstra rutan .

4. På sidan Åtkomstkontroll (IAM) klickar du på **Lägg till rolltilldelning** och **fönstret Lägg till rolltilldelning** visas.

5. Välj rollen **Deltagare** under listrutan **Roll** i fönstret **Lägg till rolltilldelning.**  

6. Under listrutan **Tilldela åtkomst till** väljer du det Configuration Manager-program som skapades i AD tidigare och klickar sedan på **OK**.  

## <a name="download-and-install-the-agent"></a>Ladda ner och installera agenten

Läs artikeln [Anslut Windows-datorer till Azure Monitor i Azure](agent-windows.md) för att förstå vilka metoder som är tillgängliga för installation av Log Analytics-agenten för Windows på datorn som är värd för platssystemplatsen Configuration Manager-tjänstens anslutningspunktsplats.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Anslut Configuration Manager till Log Analytics-arbetsyta

>[!NOTE]
> För att kunna lägga till en Log Analytics-anslutning måste Configuration Manager-miljön ha en [tjänstanslutningspunkt](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) konfigurerad för onlineläge.

> [!NOTE]
> Du måste ansluta den översta platsen i hierarkin till Azure Monitor. Om du ansluter en fristående primär plats till Azure Monitor och sedan lägger till en central administrationsplats i din miljö måste du ta bort och återskapa anslutningen i den nya hierarkin.

1. På **administrationsarbetsytan** i Configuration Manager väljer du **Molntjänster** och väljer sedan **Azure Services**. 

2. Högerklicka på **Azure Services** och välj sedan Konfigurera Azure **Services**. Sidan **Konfigurera Azure Services** visas. 
   
3. På skärmen **Allmänt** bekräftar du att du har gjort följande åtgärder och att du har information för varje objekt och väljer sedan **Nästa**.

4. På sidan Azure Services i Azure Services-guiden:

    1. Ange ett **namn** för objektet i Configuration Manager.
    2. Ange en valfri **beskrivning som** hjälper dig att identifiera tjänsten.
    3. Välj Azure-tjänsten **OMS Connector**.

    >[!NOTE]
    >OMS kallas nu Log Analytics som är en funktion i Azure Monitor.

5. Välj **Nästa** om du vill fortsätta till sidan Egenskaper för Azure-appar i Azure Services-guiden.

6. På **appsidan** i Azure Services-guiden väljer du först Azure-miljön i listan och klickar sedan på **Importera**.

7. Ange följande information på sidan **Importera appar:**

    1. Ange **Azure AD-klientnamnet** för appen.

    2. Ange för **Azure AD-klient-ID** som Azure AD-klienten. Du hittar den här informationen på **sidan** Egenskaper för Azure Active Directory. 

    3. Ange **programnamnet för Programnamn.**

    4. Ange för **klient-ID**, program-ID för den skapade Azure AD-appen som skapats tidigare.

    5. Ange för **hemlig nyckel**, klientens hemliga nyckel för den skapade Azure AD-appen.

    6. Ange för utgången av **hemliga nyckeln**, förfallodatum för nyckeln.

    7. Ange för **App ID URI**, App-ID URI för den skapade Azure AD-appen som skapats tidigare.

    8. Välj **Verifiera** och till höger ska resultaten visa **Verifierad!**.

8. På **sidan Konfiguration** granskar du informationen för att verifiera **Azure-prenumerationerna,** **Azure-resursgruppen**och **operations management suite-arbetsytefälten** är förifyllda och anger att Azure AD-programmet har tillräcklig behörighet i resursgruppen. Om fälten är tomma anger det att ditt program inte har de rättigheter som krävs. Välj de enhetssamlingar som ska samlas in och vidarebefordras till arbetsytan och välj sedan **Lägg till**.

9. Granska alternativen på sidan **Bekräfta inställningar** och välj **Nästa** för att börja skapa och konfigurera anslutningen.

10. När konfigurationen är klar visas sidan **Slutförande.** Välj **Stäng**. 

När du har länkat Configuration Manager till Azure Monitor kan du lägga till eller ta bort samlingar och visa egenskaperna för anslutningen.

## <a name="update-log-analytics-workspace-connection-properties"></a>Uppdatera anslutningsegenskaper för logganalysarbetsyta

Om ett lösenord eller en klienthemlig nyckel upphör att gälla eller försvinner måste du uppdatera anslutningsegenskaperna log Analytics manuellt.

1. På **administrationsarbetsytan** i Configuration Manager väljer du **Molntjänster** och väljer sedan **OMS-anslutning** för att öppna sidan **OMS-anslutningsegenskaper.**
2. På den här sidan klickar du på fliken **Azure Active Directory** för att visa **klient-,** **klient-ID-** **och klientnyckelns förfallodatum**. **Verifiera** din **klienthemlighetsnyckel** om den har upphört att gälla.

## <a name="import-collections"></a>Importera samlingar

När du har lagt till en Log Analytics-anslutning till Configuration Manager och installerat agenten på datorn som kör systemrollen för Configuration Manager-tjänstanslutningspunktsplats, är nästa steg att importera samlingar från Configuration Manager i Azure Övervaka som datorgrupper.

När du har slutfört den första konfigurationen för att importera enhetssamlingar från hierarkin hämtas insamlingsinformationen var tredje timme för att hålla medlemskapet aktuellt. Du kan välja att inaktivera detta när som helst.

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics-arbetsytor**.
2. Välj den arbetsytekonfigurationshanteraren som är registrerad hos i listan över Logganalysarbetsytor.  
3. Välj **Avancerade inställningar**.
4. Välj **Datorgrupper** och välj sedan **SCCM**.  
5. Välj **Medlemskap i Import Configuration Manager-samling** och klicka sedan på **Spara**.  
   
    ![Fliken Datorgrupper – fliken SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visa data från Configuration Manager

När du har lagt till en Log Analytics-anslutning till Configuration Manager och installerat agenten på datorn som kör systemrollen för Configuration Manager-tjänstanslutningspunktsplats, skickas data från agenten till Log Analytics-arbetsytan i Azure Monitor. I Azure Monitor visas dina Configuration Manager-samlingar som [datorgrupper](../../azure-monitor/platform/computer-groups.md). Du kan visa grupperna från sidan **Configuration Manager** under **Inställningar\Datorgrupper**.

När samlingarna har importerats kan du se hur många datorer med samlingsmedlemskap som har upptäckts. Du kan också se antalet samlingar som har importerats.

![Fliken Datorgrupper – fliken SCCM](./media/collect-sccm/sccm-computer-groups02.png)

När du klickar på någon av dem öppnas loggfrågeredigeraren som visar antingen alla importerade grupper eller alla datorer som tillhör varje grupp. Med hjälp av [Loggsökning](../../azure-monitor/log-query/log-query-overview.md)kan du utföra ytterligare djupgående analyser av insamlingsmedlemskapsdata.

## <a name="next-steps"></a>Nästa steg

Använd [Loggsökning](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerad information om dina Configuration Manager-data.
