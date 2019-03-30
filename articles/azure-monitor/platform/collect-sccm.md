---
title: Ansluta Configuration Manager till Azure Monitor | Microsoft Docs
description: Den här artikeln visar hur du ansluter Configuration Manager till arbetsyta i Azure Monitor och börja analysera data.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: e5cf89b7fe01946de9944a7026ec448cd55dd6dc
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630672"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Ansluta Configuration Manager till Azure Monitor
Du kan ansluta din miljö för System Center Configuration Manager till Azure Monitor för att synkronisera enheten samlingsdata och referera till dessa samlingar i Azure Monitor och Azure Automation.  

## <a name="prerequisites"></a>Förutsättningar

Azure Monitor har stöd för System Center Configuration Manager current branch versionen 1606 och högre.  

## <a name="configuration-overview"></a>Översikt över konfiguration
Följande steg sammanfattar hur du konfigurerar Configuration Manager-integrering med Azure Monitor.  

1. Registrera Configuration Manager som en Webbapp och/eller webb-API-app i Azure-portalen och kontrollera att du har klient-ID och klientens hemliga nyckel från registrering från Azure Active Directory. Se [Använd portalen för att skapa Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md) detaljerad information om hur du utför det här steget.
2. I Azure-portalen [ge Configuration Manager (den registrerade webbappen) med behörighet att komma åt Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. I Configuration Manager och Lägg till en anslutning med hjälp av guiden Lägg till OMS-anslutning.
4. I Configuration Manager och uppdatera anslutningsegenskaperna om den hemliga nyckeln som lösenord eller klienten någonsin upphör att gälla eller går förlorad.
5. [Ladda ned och installera Microsoft Monitoring Agent](#download-and-install-the-agent) på datorn som kör Configuration Manager service anslutning platssystemrollen. Agenten skickar data för Configuration Manager till Log Analytics-arbetsyta i Azure Monitor.
6. I Azure Monitor [importera samlingar från Configuration Manager](#import-collections) som datorgrupper.
7. I Azure Monitor kan du visa data från Configuration Manager som [datorgrupper](computer-groups.md).

Du kan läsa mer om hur du ansluter Configuration Manager till Azure Monitor på [synkronisera data från Configuration Manager till Microsoft Log Analytics-arbetsytan](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Bevilja Configuration Manager med behörigheter till Log Analytics
I följande procedur du bevilja den *deltagare* roll i Log Analytics-arbetsytan till AD-program och tjänstens huvudnamn som du skapade tidigare för Configuration Manager.  Om du inte redan har en arbetsyta, se [skapa en arbetsyta i Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) innan du fortsätter.  På så sätt kan Configuration Manager för att autentisera och ansluta till Log Analytics-arbetsytan.  

> [!NOTE]
> Du måste ange behörigheter i Log Analytics-arbetsytan för Configuration Manager. I annat fall får du ett felmeddelande när du använder guiden för konfiguration av i Configuration Manager.
>

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. I listan med Log Analytics-arbetsytor, väljer du arbetsytan för att ändra.
3. I den vänstra rutan, Välj **åtkomstkontroll (IAM)**.
4. På sidan åtkomst åtkomstkontroll (IAM) klickar du på **Lägg till rolltilldelning** och **Lägg till rolltilldelning** visas fönstret.
5. I den **Lägg till rolltilldelning** fönstret under den **rollen** listrutan Välj den **deltagare** roll.  
6. Under den **tilldela åtkomst till** listrutan, Välj den Configuration Manager-program som skapats i AD tidigare och klicka sedan på **OK**.  

## <a name="download-and-install-the-agent"></a>Ladda ned och installera agenten
I artikeln [ansluta Windows-datorer till Azure Monitor i Azure](agent-windows.md) att förstå metoderna som är tillgängliga för att installera Microsoft Monitoring Agent på datorn som är värd för tjänstanslutningspunkten för Configuration Manager platssystemroll.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Lägg till en Log Analytics-anslutning till Configuration Manager
För att lägga till Log Analytics-anslutning, Configuration Manager-miljön måste ha en [tjänstanslutningspunkten](https://technet.microsoft.com/library/mt627781.aspx) konfigurerats för online-läge.

1. I den **Administration** arbetsytan av Configuration Manager, Välj **OMS-anslutningsprogrammet**. Då öppnas det **guiden Lägg till Log Analytics-anslutning**. Välj **Nästa**.

   >[!NOTE]
   >OMS kallas nu för Log Analytics som är en funktion i Azure Monitor.
   
2. På den **Allmänt** skärmen, bekräfta att du har gjort följande åtgärder och att du har informationen om varje objekt och välj sedan **nästa**.

   1. I Azure-portalen, du har registrerat Configuration Manager som en Webbapp och/eller webb-API-app och som du har den [klient-ID från registreringen](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md).
   2. Du har skapat en app hemlig nyckel för registrerad app i Azure Active Directory i Azure-portalen.  
   3. Du har angett den registrerade webbappen med behörighet att komma åt Log Analytics-arbetsyta i Azure Monitor i Azure-portalen.  
      ![Anslutning till Log Analytics guiden fliken Allmänt](./media/collect-sccm/sccm-console-general01.png)
3. På den **Azure Active Directory** skärmen, konfigurera anslutningsinställningar till Log Analytics-arbetsytan genom att tillhandahålla ditt **klient**, **klient-ID**, och **Klienten hemlig nyckel**och välj sedan **nästa**.  
   ![Anslutning till Log Analytics guiden Azure Active Directory-sidan](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Om du gör alla andra procedurer, sedan informationen på den **OMS anslutningskonfiguration** skärmen visas automatiskt på den här sidan. Information om vilka inställningar som ska visas för dina **Azure-prenumeration**, **Azure-resursgrupp**, och **Operations Management Suite-arbetsyta**.  
   ![Anslutningen till Log Analytics guiden Log Analytics-anslutning sida](./media/collect-sccm/sccm-wizard-configure04.png)
5. Guiden ansluter till Log Analytics-arbetsytan med hjälp av informationen som du har indata. Välj enhetssamlingar som du vill synkronisera med tjänsten och klicka sedan på **Lägg till**.  
   ![Välj samlingar](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Verifiera dina anslutningsinställningar på den **sammanfattning** skärmen och välj sedan **nästa**. Den **förlopp** skärmen visar anslutningsstatus och sedan ska **Slutför**.

> [!NOTE]
> Du måste ansluta den översta nivån i hierarkin till Azure Monitor. Om du ansluter en fristående primär plats till Azure Monitor och sedan lägga till en central administrationsplats i din miljö kan behöva du ta bort och återskapa anslutningen i den nya hierarkin.
>
>

När du har länkat Configuration Manager till Azure Monitor kan du lägga till eller ta bort samlingar och visa egenskaperna för anslutningen.

## <a name="update-log-analytics-workspace-connection-properties"></a>Uppdatera anslutningsegenskaperna för Log Analytics-arbetsyta
Om lösenord eller klientens hemliga nyckel upphör att gälla någonsin eller tappas bort, måste du manuellt uppdatera egenskaperna för Log Analytics-anslutning.

1. I Configuration Manager och navigera till **molntjänster**och välj sedan **OMS-anslutningsprogrammet** att öppna den **OMS anslutningsegenskaper** sidan.
2. Den här sidan, klicka på den **Azure Active Directory** fliken för att visa dina **klient**, **klient-ID**, **utgångsdatum för klientens hemliga nyckel**. **Kontrollera** din **klientens hemliga nyckel** om det har gått ut.

## <a name="import-collections"></a>Importera samlingar
När du har lagt till en Log Analytics-anslutning till Configuration Manager och har installerat agenten på datorn som kör Configuration Manager-tjänstanslutning platssystemrollen, nästa steg är att importera samlingar från Configuration Manager i Azure Övervaka som datorgrupper.

När du har slutfört inledande konfiguration för att importera enhetssamlingar från hierarkin hämtas medlemskapsinformation samling var tredje timme om du vill behålla det aktuella medlemskapet. Du kan välja att inaktivera det när som helst.

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics-arbetsytor**.
2. Välj den arbetsyta som Configuration Manager har registrerats med i listan med Log Analytics-arbetsytor.  
3. Välj **Avancerade inställningar**.
4. Välj **datorgrupper** och välj sedan **SCCM**.  
5. Välj **Import Configuration Manager-samlingsmedlemskap** och klicka sedan på **spara**.  
   ![Datorgrupper - SCCM-fliken](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visa data från Configuration Manager
När du har lagt till en Log Analytics-anslutning till Configuration Manager och har installerat agenten på den dator som kör Configuration Manager service anslutning platssystemrollen, skickas data från agenten till Log Analytics-arbetsyta i Azure Monitor. I Azure Monitor, Configuration Manager-samlingar visas som [datorgrupper](../../azure-monitor/platform/computer-groups.md). Du kan visa grupper från den **Configuration Manager** sidan **Settings\Computer grupper**.

När samlingarna har importerats kan du se hur många datorer med samlingsmedlemskap har identifierats. Du kan också se antalet samlingar som har importerats.

![Datorgrupper - SCCM-fliken](./media/collect-sccm/sccm-computer-groups02.png)

När du klickar på någon öppnas sökning och visar antingen alla importerade grupper eller alla datorer som ingår i varje grupp. Med hjälp av [Loggsökning](../../azure-monitor/log-query/log-query-overview.md), du kan starta djupgående analys av data för Configuration Manager.

## <a name="next-steps"></a>Nästa steg
* Använd [Loggsökning](../../azure-monitor/log-query/log-query-overview.md) att visa detaljerad information om dina data för Configuration Manager.
