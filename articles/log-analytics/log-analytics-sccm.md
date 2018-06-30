---
title: Ansluta Configuration Manager till Log Analytics | Microsoft Docs
description: Den här artikeln visar stegen för att ansluta Configuration Manager till logganalys och börja analysera data.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 6bb59e4b63933500bc8571dca2422eec6c3456ee
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129765"
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Ansluta Configuration Manager till logganalys
Du kan ansluta din miljö för System Center Configuration Manager till Azure logganalys att synkronisera enheten samlingsdata och referera till dessa samlingar i logganalys och Azure Automation.  

## <a name="prerequisites"></a>Förutsättningar

Logganalys stöder System Center Configuration Manager aktuell gren, version 1606 och högre.  

## <a name="configuration-overview"></a>Konfigurationsöversikt
Följande steg sammanfattar stegen för att konfigurera Configuration Manager-integrering med logganalys.  

1. Registrera Configuration Manager som en webbprogram och/eller webb-API-app i Azure-portalen och kontrollera att du använder klient-ID och klientens hemliga nyckel från registrering från Azure Active Directory. Se [använda portalen för att skapa Active Directory applikationen eller tjänsten säkerhetsobjekt som kan komma åt resurser](../azure-resource-manager/resource-group-create-service-principal-portal.md) detaljerad information om hur du utför det här steget.
2. I Azure-portalen [bevilja Configuration Manager (registrerade webbappen) med behörighet att komma åt logganalys](#grant-configuration-manager-with-permissions-to-log-analytics).
3. I Configuration Manager [lägga till en anslutning med hjälp av guiden Lägg till OMS-anslutning](#add-an-oms-connection-to-configuration-manager).
4. I Configuration Manager [uppdatera anslutningsegenskaperna](#update-oms-connection-properties) om den hemliga nyckeln lösenord eller klienten aldrig upphör att gälla eller går förlorad.
5. [Hämta och installera Microsoft Monitoring Agent](#download-and-install-the-agent) på datorn som kör Configuration Manager service anslutning platssystemrollen. Agenten skickar data för Configuration Manager till logganalys-arbetsytan.
6. I Log Analytics [importera samlingar från Configuration Manager](#import-collections) som datorgrupper.
7. Visa data från Configuration Manager som i Log Analytics [datorgrupper](log-analytics-computer-groups.md).

Du kan läsa mer om hur du ansluter Configuration Manager till OMS på [synkronisera data från Configuration Manager till Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Bevilja behörigheter till logganalys Configuration Manager
I följande procedur ger den *deltagare* roll i logganalys-arbetsytan till AD-program och tjänstens huvudnamn som du skapade tidigare för Configuration Manager.  Om du inte redan har en arbetsyta finns [skapa en arbetsyta i Azure Log Analytics](log-analytics-quick-create-workspace.md) innan du fortsätter.  Detta gör att Configuration Manager för att autentisera och ansluta till logganalys-arbetsytan.  

> [!NOTE]
> Du måste ange behörigheter i logganalys för Configuration Manager. I annat fall visas ett felmeddelande när du använder guiden för konfiguration av i Configuration Manager.
>

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. I listan över logganalys arbetsytor, väljer du arbetsytan för att ändra.
3. I den vänstra rutan, Välj **Access Control (IAM)**.
4. Access Control-sidan klickar du på **Lägg till** och **lägga till behörigheter** visas.
5. I den **lägga till behörigheter** rutan under den **rollen** listrutan väljer den **deltagare** roll.  
6. Under den **bevilja åtkomst till** listrutan, Välj den Configuration Manager-program som skapats i AD och klicka sedan på **OK**.  

## <a name="download-and-install-the-agent"></a>Hämta och installera agenten
Läs artikeln [ansluta Windows-datorer i Azure logganalys-tjänsten](log-analytics-agent-windows.md) att förstå metoderna som är tillgängliga för att installera Microsoft Monitoring Agent på den dator där Configuration Manager-tjänsten anslutningen platssystemroll.  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Lägg till en OMS-anslutning till Configuration Manager
Om du vill lägga till en OMS-anslutning, Configuration Manager-miljön måste ha en [tjänstanslutningspunkt](https://technet.microsoft.com/library/mt627781.aspx) konfigurerats för online-läge.

1. I den **Administration** arbetsytan av Configuration Manager, Välj **OMS Connector**. Då öppnas den **guiden för Lägg till OMS-anslutning**. Välj **Nästa**.
2. På den **allmänna** skärmen, bekräfta att du har gjort följande åtgärder och att du har mer detaljer för varje objekt och sedan välja **nästa**.

   1. I Azure-portalen har registrerat Configuration Manager som en webbprogram och/eller webb-API-app och att du har den [klient-ID från registreringen](../active-directory/active-directory-integrating-applications.md).
   2. Du har skapat en app hemlig nyckel för appen registrerade i Azure Active Directory i Azure-portalen.  
   3. Du har angett registrerade webbprogrammet med behörighet att komma åt OMS i Azure-portalen.  
      ![Anslutningen till OMS guiden allmänna sida](./media/log-analytics-sccm/sccm-console-general01.png)
3. På den **Azure Active Directory** skärmen, konfigurera inställningarna för anslutning till logganalys genom att tillhandahålla ditt **klient**, **klient-ID**, och **klienten Hemlig nyckel**och välj **nästa**.  
   ![Anslutningen till OMS guiden Azure Active Directory-sida](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Om du gör andra procedurer, sedan informationen på den **OMS anslutningskonfiguration** skärm visas automatiskt på den här sidan. Information om vilka inställningar som ska visas för dina **Azure-prenumeration**, **Azure-resursgrupp**, och **Operations Management Suite-arbetsyta**.  
   ![Anslutning till OMS guiden OMS-anslutningssidan](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. Guiden ansluter till Log Analytics-tjänsten med hjälp av information som du har indata. Välj enhetssamlingar som du vill synkronisera med tjänsten och klicka sedan på **Lägg till**.  
   ![Välj samlingar](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Kontrollera inställningarna på den **sammanfattning** skärmen och väljer sedan **nästa**. Den **förlopp** skärmen visar anslutningsstatus för och sedan ska **Slutför**.

> [!NOTE]
> Du måste ansluta den översta nivån i hierarkin till logganalys. Om du ansluter en fristående primär plats till logganalys och sedan lägga till en central administrationsplats i din miljö kan behöva du ta bort och återskapa anslutningen i den nya hierarkin.
>
>

När du har länkat Configuration Manager till logganalys kan du lägga till eller ta bort samlingar och visa egenskaperna för anslutningen.

## <a name="update-log-analytics-connection-properties"></a>Uppdatera logganalys anslutningsegenskaper
Om en hemlig nyckel lösenord eller klienten aldrig upphör att gälla eller tappas bort, måste du uppdatera anslutningsegenskaperna logganalys.

1. I Configuration Manager navigerar du till **molntjänster**och välj **OMS Connector** att öppna den **OMS anslutningsegenskaper** sidan.
2. Klicka på den här sidan i **Azure Active Directory** att visa din **klient**, **klient-ID**, **klientens hemliga nyckel förfallodatum**. **Kontrollera** din **klientens hemliga nyckel** om det har gått ut.

## <a name="import-collections"></a>Importera samlingar
När du har lagt till en OMS-anslutning till Configuration Manager och har installerat agenten på datorn som kör Configuration Manager-tjänst-anslutningen platssystemsroll, nästa steg är att importera samlingar från Configuration Manager i logganalys som datorgrupper.

När du har slutfört inledande konfiguration för att importera enhetssamlingar från hierarkin hämtas medlemskapsinformation samling var 3: e timme om du vill behålla det aktuella medlemskap. Du kan välja att inaktivera det när som helst.

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Välj arbetsytan Configuration Manager har registrerats med i listan över logganalys arbetsytor.  
3. Välj **Avancerade inställningar**.<br><br> ![Avancerade inställningar i Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Välj **datorgrupper** och välj sedan **SCCM**.  
5. Välj **Import Configuration Manager samlingsmedlemskap** och klicka sedan på **spara**.  
   ![Datorgrupper - SCCM-fliken](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visa data från Configuration Manager
När du har lagt till en OMS-anslutning till Configuration Manager och har installerat agenten på datorn som kör Configuration Manager service anslutning platssystemrollen, skickas data från agenten till logganalys. Configuration Manager-samlingar visas som i Log Analytics [datorgrupper](log-analytics-computer-groups.md). Du kan visa grupper från den **Configuration Manager** sidan **Settings\Computer grupper**.

När samlingarna som har importerats kan du se hur många datorer med samlingsmedlemskap har identifierats. Du kan också se antalet samlingar som har importerats.

![Datorgrupper - SCCM-fliken](./media/log-analytics-sccm/sccm-computer-groups02.png)

När du klickar på antingen en öppnas sökning och visar antingen alla importerade grupper eller alla datorer som ingår i varje grupp. Med hjälp av [loggen Sök](log-analytics-log-searches.md), kan du starta djupgående analys av data för Configuration Manager.

## <a name="next-steps"></a>Nästa steg
* Använd [loggen Sök](log-analytics-log-searches.md) att visa detaljerad information om Configuration Manager-data.
