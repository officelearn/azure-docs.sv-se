---
title: Självstudie – Arkivera Azure Active Directory-loggar till ett Azure-lagringskonto (förhandsversion) | Microsoft Docs
description: Lär dig hur du konfigurerar Azure-diagnostik för att skicka Azure Active Directory-loggar till ett lagringskonto (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: cf69fdf87f1ce64bd78478f07e575d7ae75c16e0
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394262"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account-preview"></a>Självstudie: Arkivera Azure AD-loggar till ett Azure-lagringskonto (förhandsversion)

I den här självstudien får du lära dig hur du konfigurerar Azure Monitor-diagnostikinställningar för att dirigera Azure Active Directory-loggar (Azure AD) till ett Azure-lagringskonto.

## <a name="prerequisites"></a>Nödvändiga komponenter 

Om du vill använda den här funktionen behöver du:

* En Azure-prenumeration med ett Azure-lagringskonto. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure AD-klientorganisation.
* En användare som är *global administratör* eller *säkerhetsadministratör* för Azure AD-klientorganisationen.

## <a name="archive-logs-to-an-azure-storage-account"></a>Arkivera loggar till ett Azure-lagringskonto

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **Azure Active Directory** > **Aktivitet** > **Spårningsloggar**. 

3. Välj **Exportinställningar**. 

4. I fönsterrutan **Diagnostikinställningar** gör du något av följande:
    * Om du vill ändra befintliga inställningar väljer du **Redigera inställning**.
    * Om du vill lägga till nya inställningar väljer du **Add diagnostics setting** (Lägg till diagnostikinställning).  
      Du kan ha upp till tre inställningar. 

    ![Exportinställningar](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Ange ett eget namn för inställningen som påminner dig om dess syfte (exempelvis *Skicka till Azure-lagringskonto*). 

6. Markera kryssrutan **Arkivera till ett lagringskonto** och välj sedan **Lagringskonto**. 

7. Välj den Azure-prenumeration och det lagringskonto som du vill dirigera loggarna till.
 
8. Välj **OK** för att avsluta konfigurationen.

9. Gör endera eller båda av följande:
    * För att skicka spårningsloggar till lagringskontot markerar du kryssrutan **AuditLogs** (Spårningsloggar). 
    * För att skicka inloggningsloggar till lagringskontot markerar du kryssrutan **SignInLogs** (Inloggningsloggar).

10. Använd skjutreglaget för att ange kvarhållningen av dina loggdata. Som standard är det här värdet *0*, vilket innebär att loggar behålls i lagringskontot på obestämd tid. Om du anger ett annat värde rensas händelser som är äldre än det valda antalet dagar automatiskt.

11. Spara inställningen genom att välja **Spara**.

    ![Diagnostikinställningar](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Efter cirka 15 minuter kontrollerar du att loggarna skickas till ditt lagringskonto. Gå till [Azure-portalen](https://portal.azure.com), välj **Lagringskonton**, välj det lagringskonto som du använde tidigare och välj sedan **Blobar**. 

13. För **Spårningsloggar** väljer du **insights-log-audit**. För **Inloggningsloggar** väljer du **insights-logs-signin**.
    ![Lagringskonto](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Nästa steg

* [Tolka schema för spårningsloggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
