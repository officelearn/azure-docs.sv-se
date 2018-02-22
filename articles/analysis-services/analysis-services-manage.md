---
title: Hantera Azure Analysis Services | Microsoft Docs
description: "Lär dig hur du hanterar en Analysis Services-server i Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 11aa42fbe15dd68dfac128b707fd6bdbfd0b24d1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="manage-analysis-services"></a>Manage Analysis Services
När du har skapat en Analysis Services-server i Azure kan finnas det vissa administrations- och uppgifter som du behöver utföra direkt eller senare ned väg. Till exempel bearbetning för att uppdatera data, styra vem som kan komma åt modeller på servern eller övervaka ditt Servertillstånd. Vissa hanteringsuppgifter kan endast utföras i Azure-portalen andra i SQL Server Management Studio (SSMS), och vissa aktiviteter kan göras antingen i.

## <a name="azure-portal"></a>Azure Portal
[Azure-portalen](http://portal.azure.com/) är där du kan skapa och ta bort servrar, övervaka serverresurser, ändra storlek och hantera vem som har åtkomst till dina servrar.  Om du har några problem, kan du även skicka en supportförfrågan.

![Hämta servernamnet i Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Ansluter till servern i Azure är precis som ansluter till en server-instans i din organisation. Från SSMS, du utföra samma uppgifter, till exempel processdata eller skapa ett skript för bearbetning, hantera roller och använder PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Hämta och installera SSMS
För att få de senaste funktionerna och erfarenhet som jämnaste vid anslutning till Azure Analysis Services-server kan vara att du använder den senaste versionen av SSMS. 

[Hämta SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Att ansluta med SSMS
 När du använder SSMS, innan du ansluter till servern för första gången, kontrollera att ditt användarnamn ingår i gruppen Administratörer för Analysis Services. Läs mer i [serveradministratörer](#server-administrators) senare i den här artikeln.

1. Du måste hämta namnet på servern innan du ansluter. Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
    ![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. I SSMS > **Object Explorer**, klickar du på **Anslut** > **Analysis Services**.
3. I den **Anslut till Server** klistra in i namnet på server och sedan i dialogrutan **autentisering**, väljer du något av följande autentiseringstyper av:
   
    **Windows-autentisering** att använda dina Windows-autentiseringsuppgifter domän\användarnamn och lösenord.

    **Active Directory-lösenordsautentisering** att använda ett organisationskonto. När ansluter från en icke-domän exempelvis anslutna datorn.

    **Active Directory Universal autentisering** att använda [icke-interaktiv eller Multi-Factor authentication](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Ansluta i SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Server-administratörer och användare
Det finns två typer av användare, administratörer och användare i Azure Analysis Services. Båda typerna av användare måste finnas i Azure Active Directory och måste anges med organisationens e-postadress eller UPN. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Felsökning av anslutningsproblem
När du ansluter med hjälp av SSMS, om du stöter på problem kan behöva du rensa cacheminnet för inloggning. Inget cachelagras-skiva. Om du vill rensa cachen, Stäng och starta om processen connect. 

## <a name="next-steps"></a>Nästa steg
Om du inte redan har distribuerat en tabellmodell till den nya servern nu är ett bra tillfälle. Läs mer i [Distribuera till Azure Analysis Services](analysis-services-deploy.md).

Om du har distribuerat en modell till servern, är du redo att ansluta till den med hjälp av en klient eller en webbläsare. Läs mer i [hämta data från Azure Analysis Services-servern](analysis-services-connect.md).

