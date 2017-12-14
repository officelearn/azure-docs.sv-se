---
title: Ansluta Configuration Manager till Log Analytics | Microsoft Docs
description: "Den här artikeln visar stegen för att ansluta Configuration Manager till logganalys och börja analysera data."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
ms.openlocfilehash: 6785bfcefb09fa6135ba451fafa76efc8c2e6c76
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Ansluta Configuration Manager till logganalys
Du kan ansluta till System Center Configuration Manager till logganalys i OMS att synkronisera enheten samlingsdata. Detta gör data från Configuration Manager-hierarki finns i OMS.

## <a name="prerequisites"></a>Krav

Logganalys stöder System Center Configuration Manager aktuell gren, version 1606 och högre.  

## <a name="configuration-overview"></a>Konfigurationsöversikt
Följande steg sammanfattar processen för att ansluta Configuration Manager till logganalys.  

1. Registrera Configuration Manager som en webbprogram och/eller webb-API-app i Azure-hanteringsportalen och kontrollera att du använder klient-ID och klientens hemliga nyckel från registrering från Azure Active Directory. Se [använda portalen för att skapa Active Directory applikationen eller tjänsten säkerhetsobjekt som kan komma åt resurser](../azure-resource-manager/resource-group-create-service-principal-portal.md) detaljerad information om hur du utför det här steget.
2. I Azure-hanteringsportalen [ange Configuration Manager (registrerade webbappen) med behörighet att komma åt OMS](#provide-configuration-manager-with-permissions-to-oms).
3. I Configuration Manager [lägga till en anslutning med hjälp av guiden Lägg till OMS-anslutning](#add-an-oms-connection-to-configuration-manager).
4. I Configuration Manager [uppdatera anslutningsegenskaperna](#update-oms-connection-properties) om den hemliga nyckeln lösenord eller klienten aldrig upphör att gälla eller går förlorad.
5. Med information från OMS-portalen [ladda ned och installera Microsoft Monitoring Agent](#download-and-install-the-agent) på datorn som kör Configuration Manager service anslutning platssystemrollen. Agenten skickar data för Configuration Manager till OMS.
6. I Log Analytics [importera samlingar från Configuration Manager](#import-collections) som datorgrupper.
7. Visa data från Configuration Manager som i Log Analytics [datorgrupper](log-analytics-computer-groups.md).

Du kan läsa mer om hur du ansluter Configuration Manager till OMS på [synkronisera data från Configuration Manager till Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Ge behörigheter Configuration Manager till OMS
Följande procedur innehåller Azure-hanteringsportalen med behörighet att komma åt OMS. Mer specifikt måste du bevilja den *deltagarrollen* till användare i resursgruppen för att tillåta Azure-hanteringsportalen att ansluta Configuration Manager till OMS.

> [!NOTE]
> Du måste ange behörigheter i OMS för Configuration Manager. I annat fall visas ett felmeddelande när du använder guiden för konfiguration av i Configuration Manager.
>
>

1. Öppna den [Azure-portalen](https://portal.azure.com/) och på **Bläddra** > **logganalys (OMS)** att öppna logganalys (OMS).  
2. På **logganalys (OMS)**, klickar du på **Lägg till** att öppna **OMS-arbetsytan**.  
   ![OMS](./media/log-analytics-sccm/sccm-azure01.png)
3. På **OMS-arbetsytan**, ange följande information och klicka sedan på **OK**.

   * **OMS-arbetsyta**
   * **Prenumeration**
   * **Resursgrupp**
   * **Plats**
   * **prisnivå**  
     ![OMS](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > Exemplet ovan skapar en ny resursgrupp. Resursgruppens namn används bara för att ge behörigheter till OMS-arbetsytan i det här exemplet Configuration Manager.
     >
     >
4. Klicka på **Bläddra** > **resursgrupper** att öppna **resursgrupper**.
5. I **resursgrupper**, klickar du på den resursgrupp som du skapade ovan för att öppna den &lt;resursgruppens namn&gt; inställningar.  
   ![gruppen resursinställningar](./media/log-analytics-sccm/sccm-azure03.png)
6. I &lt;resursgruppnamn&gt; klickar du på åtkomstkontroll (IAM) för att öppna &lt;resursgruppens namn&gt; användare.  
   ![resursgruppen användare](./media/log-analytics-sccm/sccm-azure04.png)  
7. I &lt;resursgruppnamn&gt; klickar du på **Lägg till** att öppna **Lägg till åtkomst**.
8. I **Lägg till åtkomst**, klickar du på **Välj en roll**, och välj sedan den **deltagare** roll.  
   ![Välj en roll](./media/log-analytics-sccm/sccm-azure05.png)  
9. Klicka på **lägga till användare**, Välj den Configuration Manager-användaren, klicka på **Välj**, och klicka sedan på **OK**.  
   ![Lägg till användare](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Lägg till en OMS-anslutning till Configuration Manager
Om du vill lägga till en OMS-anslutning, Configuration Manager-miljön måste ha en [tjänstanslutningspunkt](https://technet.microsoft.com/library/mt627781.aspx) konfigurerats för online-läge.

1. I den **Administration** arbetsytan av Configuration Manager, Välj **OMS Connector**. Då öppnas den **guiden för Lägg till OMS-anslutning**. Välj **nästa**.
2. På den **allmänna** skärmen, bekräfta att du har gjort följande åtgärder och att du har mer detaljer för varje objekt och sedan välja **nästa**.

   1. Azure-hanteringsportalen du har registrerat Configuration Manager som en webbprogram och/eller webb-API-app och som du har den [klient-ID från registreringen](../active-directory/active-directory-integrating-applications.md).
   2. Du har skapat en app hemlig nyckel för appen registrerade i Azure Active Directory i Azure-hanteringsportalen.  
   3. Du har angett registrerade webbprogrammet med behörighet att komma åt OMS i Azure-hanteringsportalen.  
      ![Anslutningen till OMS guiden allmänna sida](./media/log-analytics-sccm/sccm-console-general01.png)
3. På den **Azure Active Directory** skärmen, konfigurera anslutningsinställningar till OMS genom att tillhandahålla ditt **klient**, **klient-ID**, och **klienten hemlig nyckel** och välj **nästa**.  
   ![Anslutningen till OMS guiden Azure Active Directory-sida](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Om du gör andra procedurer, sedan informationen på den **OMS anslutningskonfiguration** skärm visas automatiskt på den här sidan. Information om vilka inställningar som ska visas för dina **Azure-prenumeration**, **Azure-resursgrupp**, och **Operations Management Suite-arbetsyta**.  
   ![Anslutning till OMS guiden OMS-anslutningssidan](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. Guiden ansluter till OMS-tjänsten med hjälp av information som du har indata. Välj enhetssamlingar som du vill synkronisera med OMS och klicka sedan på **Lägg till**.  
   ![Välj samlingar](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Kontrollera inställningarna på den **sammanfattning** skärmen och väljer sedan **nästa**. Den **förlopp** skärmen visar anslutningsstatus för och sedan ska **Slutför**.

> [!NOTE]
> Du måste ansluta OMS till den översta nivån i hierarkin. Om du ansluter OMS till en fristående primär plats och sedan lägga till en central administrationsplats i din miljö, måste du ta bort och återskapa OMS-anslutningen i den nya hierarkin.
>
>

När du har länkat Configuration Manager till OMS kan du lägga till eller ta bort samlingar och visa egenskaperna för OMS-anslutning.

## <a name="update-oms-connection-properties"></a>Uppdatera OMS anslutningsegenskaper
Om en hemlig nyckel lösenord eller klienten aldrig upphör att gälla eller tappas bort, måste du uppdatera anslutningsegenskaperna OMS.

1. I Configuration Manager navigerar du till **molntjänster**och välj **OMS Connector** att öppna den **OMS anslutningsegenskaper** sidan.
2. Klicka på den här sidan i **Azure Active Directory** att visa din **klient**, **klient-ID**, **klientens hemliga nyckel förfallodatum**. **Kontrollera** din **klientens hemliga nyckel** om det har gått ut.

## <a name="download-and-install-the-agent"></a>Hämta och installera agenten
1. I OMS-portalen [hämta installationsfilen för agenten från OMS](log-analytics-windows-agent.md).
2. Använd någon av följande metoder för att installera och konfigurera agenten på datorn som kör Configuration Manager service anslutning platssystemrollen:
   * [Installera agenten med hjälp av installationsprogrammet](log-analytics-windows-agent.md)
   * [Installera agenten med hjälp av kommandoraden](log-analytics-windows-agent.md)
   * [Installera agenten i Azure Automation DSC](log-analytics-windows-agent.md)

## <a name="import-collections"></a>Importera samlingar
När du har lagt till en OMS-anslutning till Configuration Manager och har installerat agenten på datorn som kör Configuration Manager-tjänst-anslutningen platssystemsroll, nästa steg är att importera samlingar från Configuration Manager i OMS som datorgrupper.

Efter importen har aktiverats, hämtas samling medlemskapsinformation var 3: e timme för att hålla samlingsmedlemskap aktuella. Du kan välja att inaktivera import när som helst.

1. I OMS-portalen klickar du på **inställningar**.
2. Klicka på den **datorgrupper** och klicka sedan på den **SCCM** fliken.
3. Välj **Import Configuration Manager samlingsmedlemskap** och klicka sedan på **spara**.  
   ![Datorgrupper - SCCM-fliken](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visa data från Configuration Manager
När du har lagt till en OMS-anslutning till Configuration Manager och har installerat agenten på datorn som kör Configuration Manager service anslutning platssystemrollen, skickas data från agenten till OMS. Configuration Manager-samlingar visas som i OMS, [datorgrupper](log-analytics-computer-groups.md). Du kan visa grupper från den **Configuration Manager** sidan **datorgrupper** i **inställningar**.

När samlingarna som har importerats kan du se hur många datorer med samlingsmedlemskap har identifierats. Du kan också se antalet samlingar som har importerats.

![Datorgrupper - SCCM-fliken](./media/log-analytics-sccm/sccm-computer-groups02.png)

När du klickar på antingen en öppnas sökning och visar antingen alla importerade grupper eller alla datorer som ingår i varje grupp. Med hjälp av [loggen Sök](log-analytics-log-searches.md), kan du starta djupgående analys av data för Configuration Manager.

## <a name="next-steps"></a>Nästa steg
* Använd [loggen Sök](log-analytics-log-searches.md) att visa detaljerad information om Configuration Manager-data.
