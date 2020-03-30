---
title: Hantera Azure Analysis Services | Microsoft-dokument
description: I den här artikeln beskrivs de verktyg som används för att hantera administrations- och hanteringsuppgifter för en Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28d7b2955c84833841760e441cd2919181e22bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572802"
---
# <a name="manage-analysis-services"></a>Manage Analysis Services
När du har skapat en Analysis Services-server i Azure kan det finnas vissa administrations- och hanteringsuppgifter som du måste utföra direkt eller någon gång på vägen. Kör till exempel bearbetning till uppdateringsdata, styr vem som kan komma åt modellerna på servern eller övervaka serverns hälsotillstånd. Vissa hanteringsuppgifter kan bara utföras i Azure-portalen, andra i SQL Server Management Studio (SSMS) och vissa uppgifter kan göras i antingen.

## <a name="azure-portal"></a>Azure Portal
[Azure-portalen](https://portal.azure.com/) är där du kan skapa och ta bort servrar, övervaka serverresurser, ändra storlek och hantera vem som har åtkomst till dina servrar.  Om du har problem kan du också skicka en supportbegäran.

![Hämta servernamnet i Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Att ansluta till servern i Azure är precis som att ansluta till en serverinstans i din egen organisation. Från SSMS kan du utföra många av samma uppgifter, till exempel bearbeta data eller skapa ett bearbetningsskript, hantera roller och använda PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Ladda ned och installera SSMS
Om du vill ha alla de senaste funktionerna och den smidigaste upplevelsen när du ansluter till din Azure Analysis Services-server bör du se till att du använder den senaste versionen av SSMS. 

[Ladda ner SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Så här ansluter du till SSMS
 När du använder SSMS, innan du ansluter till servern första gången, kontrollerar du att ditt användarnamn ingår i gruppen Administration av analystjänster. Mer information finns i [Serveradministratörer och databasanvändare](#server-administrators-and-database-users) senare i den här artikeln.

1. Innan du ansluter måste du hämta servernamnet. Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
    ![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. I SSMS > **Object Explorer** klickar du på **Anslut** > **Analysis Services**.
3. Klistra in servernamnet i dialogrutan **Anslut till server** och välj sedan någon av följande autentiseringstyper i **autentisering:**   
    > [!NOTE]
    > Autentiseringstyp, **Active Directory - Universal med MFA-stöd**, rekommenderas.

    > [!NOTE]
    > Om du loggar in med ett Microsoft-konto, Live ID, Yahoo, Gmail, etc., lämna lösenordsfältet tomt. Du uppmanas att ange ett lösenord när du har klickat på Anslut.

    **Windows-autentisering** för att använda din Windows-domän\användarnamn och lösenordsuppgifter.

    **Active Directory lösenordsautentisering** för att använda ett organisationskonto. Till exempel när du ansluter från en dator som inte är ansluten till en domän.

    **Active Directory - Universal med MFA-stöd** för att använda [icke-interaktiv autentisering eller multifaktorautentisering](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Ansluta i SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Serveradministratörer och databasanvändare
I Azure Analysis Services finns det två typer av användare, serveradministratörer och databasanvändare. Båda typerna av användare måste finnas i din Azure Active Directory och måste anges av organisations-e-postadress eller UPN. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Felsöka anslutningsproblem
När du ansluter med SSMS kan du behöva rensa inloggningscachen om du stöter på problem. Ingenting cachelagras till skiva. Om du vill rensa cacheminnet stänger du och startar om anslutningsprocessen. 

## <a name="next-steps"></a>Nästa steg
Om du inte redan har distribuerat en tabellmodell till den nya servern är det nu dags. Läs mer i [Distribuera till Azure Analysis Services](analysis-services-deploy.md).

Om du har distribuerat en modell till servern är du redo att ansluta till den med en klient eller webbläsare. Mer information finns i [Hämta data från Azure Analysis Services-servern](analysis-services-connect.md).

