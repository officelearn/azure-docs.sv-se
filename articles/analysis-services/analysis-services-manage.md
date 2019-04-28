---
title: Hantera Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0bae06d46c2c96ba9dd058e9c2d380379523811c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065207"
---
# <a name="manage-analysis-services"></a>Manage Analysis Services
När du har skapat en Analysis Services-server i Azure, kan det finnas vissa administration och hantering av uppgifter du behöver utföra direkt eller någon gång ned vägen. Till exempel bearbetning för att uppdatera data, styra vem som kan komma åt modeller på servern eller övervaka hälsa för din server. Vissa hanteringsuppgifter kan endast utföras i Azure-portalen, andra i SQL Server Management Studio (SSMS), och vissa aktiviteter kan göras antingen.

## <a name="azure-portal"></a>Azure Portal
[Azure-portalen](https://portal.azure.com/) är där du kan skapa och ta bort servrar, övervaka serverresurser, ändra storlek på och hantera vem som har åtkomst till dina servrar.  Om du har några problem, kan du även skicka en supportförfrågan.

![Hämta servernamnet i Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Ansluta till din server i Azure är precis som ansluter till en server-instans i din organisation. Från SSMS, kan du utföra många av uppgifterna som till exempel bearbeta data eller skapa ett skript för bearbetning, hantera roller och använder PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Ladda ned och installera SSMS
För att få de senaste funktionerna och jämnaste upplevelsen när du ansluter till Azure Analysis Services-servern kan vara säker på att du använder den senaste versionen av SSMS. 

[Ladda ned SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Att ansluta med SSMS
 När du använder SSMS, innan du ansluter till servern för första gången måste du kontrollera ditt användarnamn som ingår i gruppen Analysis Services-administratörer. Mer information finns i [serveradministratörer och databasanvändare](#server-administrators-and-database-users) senare i den här artikeln.

1. Innan du ansluter måste du hämta servernamnet. Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
    ![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. I SSMS > **Object Explorer** klickar du på **Anslut** > **Analysis Services**.
3. I den **Anslut till Server** dialogrutan Klistra in servernamnet, och sedan i **autentisering**, väljer du något av följande autentiseringstyper av:   
    > [!NOTE]
    > Autentiseringstyp, **Active Directory - Universal med stöd för MFA**, rekommenderas.

    > [!NOTE]
    > Lämna lösenordsfältet tomt om du loggar in med en Account, Live ID, Yahoo, Gmail, osv. Du uppmanas ange ett lösenord när du klickar på Anslut.

    **Windows-autentisering** att använda dina Windows-autentiseringsuppgifter i domän\användarnamn och lösenord.

    **Active Directory-lösenordsautentisering** att använda ett organisationskonto. Till exempel ansluta från en icke-domän när domänansluten dator.

    **Active Directory – Universal med stöd för MFA** att använda [icke-interaktivt eller Multi-Factor authentication](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Ansluta i SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Server-administratörer och användare
Det finns två typer av användare, server-administratörer och användare i Azure Analysis Services. Båda typerna av användare måste vara i Azure Active Directory och måste anges med organisationens e-postadress eller UPN. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Felsökning av anslutningsproblem
När du ansluter med hjälp av SSMS, om du stöter på problem, kan du behöva rensa cacheminnet för inloggning. Inget cachelagras skivan. Om du vill rensa cacheminnet, stänga och starta om connect-processen. 

## <a name="next-steps"></a>Nästa steg
Om du inte redan har distribuerat en tabellmodell till den nya servern, nu är ett bra tillfälle. Läs mer i [Distribuera till Azure Analysis Services](analysis-services-deploy.md).

Om du har distribuerat en modell till servern, är du redo att ansluta till den med hjälp av en klient eller en webbläsare. Mer information finns i [hämta data från Azure Analysis Services-servern](analysis-services-connect.md).

