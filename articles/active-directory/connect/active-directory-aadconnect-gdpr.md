---
title: Azure AD Connect och användaren sekretess | Microsoft Docs
description: Det här dokumentet beskriver hur du skaffar BNPR kompatibilitet med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8e3f81a6480e9de55c8f803e2266c4ac6e33c316
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590546"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Användarnas integritet och Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Den här artikeln handlar om Azure AD Connect och användarens integritet.  Information om Azure AD Connect Health och användaren sekretess finns i artikeln [här](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

Förbättra användarnas integritet för Azure AD Connect installationer på två sätt:

1.  Extrahera data för en person på begäran, och ta bort data från den personen från installationer
2.  Se till att inga data sparas 48 timmar.

Azure AD Connect-teamet rekommenderar det andra alternativet eftersom det är mycket enklare att implementera och underhålla.

En Azure AD Connect sync-servern lagrar följande sekretess användardata:
1.  Data om en person i den **Azure AD Connect-databas**
2.  Data i den **Windows-händelseloggen** filer som kan innehålla information om en person
3.  Data i den **Azure AD Connect installationsloggfiler** som kan innehålla om en person

Azure AD Connect-kunder bör du använda följande riktlinjer när du tar bort användardata:
1.  Ta bort innehållet i den mapp som innehåller Azure AD Connect installationsloggfilerna regelbundet – var 48 timmarna
2.  Den här produkten kan också skapa händelseloggar.  Mer information om händelseloggar loggar finns i [partitionsdokumentation](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Data om en person tas automatiskt bort från Azure AD Connect-databasen när den personen data tas bort från källsystemet ursprung från. Inga särskilda åtgärder från administratörer måste vara kompatibla BNPR.  Dock kräver att Azure AD Connect-data har synkroniserats med din datakälla var två dagar.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Ta bort Azure AD Connect-installationen loggfilens mappinnehåll
Kontrollera regelbundet och ta bort innehållet i **c:\programdata\aadconnect** mapp – förutom för den **PersistedState.Xml** fil. Den här filen bibehåller tillståndet för den tidigare installationen av Azure A Connect och används när en uppgraderingsinstallation utförs. Den här filen innehåller inte några data om en person och får inte tas bort.

>[!IMPORTANT]
>Ta inte bort filen PersistedState.xml.  Den här filen innehåller ingen användarinformation och bibehåller tillståndet för den tidigare installationen.

Du kan granska och ta bort dessa filer med hjälp av Utforskaren eller du kan använda ett skript som följande för att utföra nödvändiga åtgärder:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Schemalägga skriptet ska köras varje 48 timmarna
Använd följande steg om du vill schemalägga skriptet ska köras varje 48 timmar.

1.  Spara skriptet i en fil med tillägget  **&#46;PS1**, öppna Kontrollpanelen och klicka på **system och säkerhet**.
    ![System](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  Under rubriken Administrationsverktyg klickar du på **schemalägga aktiviteter**.
    ![Aktivitet](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  Högerklicka i Schemaläggaren på **schema Uppgiftsbibliotek** och klicka på **Skapa standardaktivitet...**
4.  Ange namnet på den nya aktiviteten och klicka på **nästa**.
5.  Välj **dagliga** för aktivitetslösare och klicka på **nästa**.
6.  Ställa in återkommande **2 dagar** och på **nästa**.
7.  Välj **starta ett program** som åtgärd och klicka på **nästa**.
8.  Typen **PowerShell** i rutan för Program eller skript och i rutan **lägga till argument (valfritt)**, ange den fullständiga sökvägen till det skript som du skapade tidigare och klicka på **nästa**.
9.  På nästa skärm visas en sammanfattning av uppgiften som du ska skapa. Kontrollera värdena och klicka på **Slutför** att skapa uppgiften.



## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy-policy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health och sekretess för alla användare](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
