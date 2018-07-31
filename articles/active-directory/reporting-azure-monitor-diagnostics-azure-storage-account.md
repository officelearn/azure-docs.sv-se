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
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240290"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>Självstudie: Arkivera Azure Active Directory-loggar till ett Azure-lagringskonto (förhandsversion)

I den här självstudien får du lära dig hur du konfigurerar Azure Monitor-diagnostikinställningar för att dirigera Azure Active Directory-loggar till ett Azure-lagringskonto.

## <a name="prerequisites"></a>Nödvändiga komponenter 

Du behöver:

* En Azure-prenumeration med ett Azure-lagringskonto. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure Active Directory-klientorganisation.
* En användare som är global administratör eller säkerhetsadministratör för den klientorganisationen.

## <a name="archive-logs-to-an-azure-storage-account"></a>Arkivera loggar till ett Azure-lagringskonto

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Klicka på **Azure Active Directory** -> **Aktivitet** -> **Spårningsloggar**. 
3. Klicka på **Exportera inställningar** för att öppna bladet Diagnostikinställningar. Klicka på **Redigera inställning** om du vill ändra befintliga inställningar eller klicka på **Lägg till diagnostikinställning** om du vill lägga till en ny. Du kan ha högst tre inställningar. 
    ![Exportera inställningar](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "Exportera inställningar")

4. Lägg till ett eget namn för inställningen för att påminna dig om dess syfte. Till exempel: ”Skicka till Azure-lagringskonto”. 
5. Markera kryssrutan **Arkivera till ett lagringskonto** och klicka på **Lagringskonto** för att välja Azure-lagringskontot. 
6. Välj en Azure-prenumeration och det lagringskonto som du vill dirigera loggarna till och klicka på **OK** att stänga konfigurationen.
7. Markera kryssrutan **AuditLogs** (Spårningsloggar) för att skicka spårningsloggar till lagringskontot. 
8. Markera kryssrutan **SignInLogs** (Inloggningsloggar) för att skicka inloggningsloggar till lagringskontot.
9. Använd skjutreglaget för att ange kvarhållning för dina loggdata. Det här värdet är ”0” som standard, och loggar behålls i lagringskontot på obestämd tid. Annars kan du ange ett värde så rensas händelser som är äldre än det valda antalet dagar automatiskt.
10. Klicka på **Spara** för att spara inställningen.
    ![Diagnostikinställningar](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "Diagnostikinställningar")

11. Efter cirka 15 minuter kontrollerar du att loggarna skickas till ditt lagringskonto. Gå till Azure-portalen, klicka på **Lagringskonton**, välj det lagringskonto som du använde tidigare och klicka på **Blobar**. 
12. För **Spårningsloggar** klickar du på **insights-log-audit**. För **Inloggningsloggar** klickar du på **insights-logs-signin**.
    ![Lagringskonto](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "Lagringskonto")

## <a name="next-steps"></a>Nästa steg

* [Tolka schema för spårningsloggar i Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Vanliga frågor och kända problem](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)