---
title: Azure AD Connect och användaren sekretess | Microsoft Docs
description: Det här dokumentet beskriver hur du skaffar GDPR-kompatibilitet med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74cdbd704f589ca4c045f672bbc49fa4460003a0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175854"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Användarsekretess och Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Den här artikeln innehåller Azure AD Connect och användaren sekretess.  Information om Azure AD Connect Health och användaren sekretess finns i artikeln [här](reference-connect-health-user-privacy.md).

Förbättra användarnas integritet för Azure AD Connect-installationer på två sätt:

1.  På begäran kan extrahera data för en person och ta bort data från den personen från installationerna
2.  Se till att inga data inte returneras efter 48 timmar.

Azure AD Connect-teamet rekommenderar det andra alternativet eftersom det är mycket enklare att implementera och underhålla.

En Azure AD Connect-synkroniseringsserver lagrar följande sekretess användardata:
1.  Data om en person i den **Azure AD Connect-databas**
2.  Data i den **Windows-händelseloggen** filer som kan innehålla information om en person
3.  Data i den **Azure AD Connect installationsloggfiler** som kan innehålla om en person

Azure AD Connect-kunder använder följande riktlinjer när du tar bort användardata:
1.  Ta bort innehållet i den mapp som innehåller Azure AD Connect installationsloggfilerna regelbundet – minst var 48 timmar
2.  Den här produkten kan också skapa händelseloggar.  Mer information om händelseloggar loggar finns i [dokumentationen här](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Data om en person tas automatiskt bort från Azure AD Connect-databasen när den personen data tas bort från källsystemet ursprung från. Ingen specifik åtgärd från administratörer krävs för att vara GDPR-kompatibel.  Det kräver dock att Azure AD Connect-data är synkroniserad med din datakälla minst varannan dag.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Ta bort mappinnehåll för Azure AD Connect-installationen log-fil
Kontrollera regelbundet och ta bort innehållet i **c:\programdata\aadconnect** mapp – undantag för den **PersistedState.Xml** fil. Den här filen underhåller tillståndet för den tidigare installationen av Azure A Connect och används när en uppgraderingsinstallation utförs. Den här filen innehåller inte några data om en person och bör inte tas bort.

>[!IMPORTANT]
>Ta inte bort filen PersistedState.xml.  Den här filen innehåller ingen användarinformation och underhåller tillståndet för den tidigare installationen.

Du kan granska och ta bort dessa filer med hjälp av Windows Explorer eller du kan använda ett skript som liknar följande för att utföra åtgärderna som krävs:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Schemalägga det här skriptet ska köras varje 48 timmar
Använd följande steg för att schemalägga skriptet att köra varje 48 timmar.

1.  Spara skriptet i en fil med filnamnstillägget  **&#46;PS1**, öppna Kontrollpanelen och klicka på **system och säkerhet**.
    ![System](./media/reference-connect-user-privacy/gdpr2.png)

2.  Under rubriken Administrationsverktyg klickar du på **schema aktiviteter**.
    ![Aktivitet](./media/reference-connect-user-privacy/gdpr3.png)
3.  I Schemaläggaren, högerklicka på **schema Uppgiftsbibliotek** och klicka på **Skapa standardaktivitet...**
4.  Ange namnet på den nya aktiviteten och klicka på **nästa**.
5.  Välj **dagliga** för aktivitetsutlösare och klicka på **nästa**.
6.  Ställa in återkommande **2 dagar** och klicka på **nästa**.
7.  Välj **programstart** som åtgärden och klicka på **nästa**.
8.  Typ **PowerShell** i rutan för Program eller skript och i rutan **lägga till argument (valfritt)**, ange den fullständiga sökvägen till det skript som du skapade tidigare och sedan på **nästa**.
9.  Nästa skärm visar en sammanfattning av uppgiften som du håller på att skapa. Kontrollera värdena och klicka på **Slutför** att skapa uppgiften.



## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy principen på Säkerhetscenter](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health och Användarsekretess](reference-connect-health-user-privacy.md)
