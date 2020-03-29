---
title: Azure AD Connect och användarsekretess | Microsoft-dokument
description: I det här dokumentet beskrivs hur du skaffar GDPR-kompatibilitet med Azure AD Connect.
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
ms.openlocfilehash: 6f5d3125b7b77e8ce7a943f640c44615049ab160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455796"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Användarsekretess och Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Den här artikeln behandlar Azure AD Connect och användarnas sekretess.  Information om Azure AD Connect Health och användarnas sekretess finns i artikeln [här](reference-connect-health-user-privacy.md).

Förbättra användarnas sekretess för Azure AD Connect-installationer på två sätt:

1.  På begäran extrahera data för en person och ta bort data från den personen från
2.  Se till att inga data lagras längre än 48 timmar.

Azure AD Connect-teamet rekommenderar det andra alternativet eftersom det är mycket enklare att implementera och underhålla.

En Azure AD Connect-synkroniseringsserver lagrar följande användarsekretessdata:
1.  Data om en person i **Azure AD Connect-databasen**
2.  Data i **Windows-händelseloggfilerna** som kan innehålla information om en person
3.  Data i **installationsloggfilerna** för Azure AD Connect som kan innehålla om en person

Azure AD Connect-kunder bör använda följande riktlinjer när användardata tas bort:
1.  Ta bort innehållet i mappen som innehåller Azure AD Connect-installationsloggfilerna regelbundet – minst var 48:e timme
2.  Den här produkten kan också skapa händelseloggar.  Mer information om loggloggar finns i [dokumentationen här](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Data om en person tas automatiskt bort från Azure AD Connect-databasen när den personens data tas bort från källsystemet där de kommer från. Ingen specifik åtgärd från administratörer krävs för att vara GDPR-kompatibel.  Det kräver dock att Azure AD Connect-data synkroniseras med datakällan minst varannan dag.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Ta bort innehållet i installationsloggmappen i Azure AD Connect
Kontrollera och ta bort innehållet i mappen **c:\programdata\aadconnect** regelbundet – med undantag för filen **PersistedState.Xml.** Den här filen upprätthåller tillståndet för den tidigare installationen av Azure A Connect och används när en uppgraderingsinstallation utförs. Den här filen innehåller inga data om en person och bör inte tas bort.

>[!IMPORTANT]
>Ta inte bort filen PersistedState.xml.  Den här filen innehåller ingen användarinformation och behåller tillståndet för den föregående installationen.

Du kan antingen granska och ta bort dessa filer med Utforskaren eller använda ett skript som följande för att utföra nödvändiga åtgärder:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Schemalägg skriptet så att det körs var 48:e timme
Följ följande steg för att schemalägga skriptet så att det körs var 48:e timme.

1.  Spara skriptet i en fil med tillägget **&#46;PS1**och öppna sedan Kontrollpanelen och klicka på **System och säkerhet**.
    ![System](./media/reference-connect-user-privacy/gdpr2.png)

2.  Klicka på **Schemalägg aktiviteter**under rubriken Administrationsverktyg .
    ![Aktivitet](./media/reference-connect-user-privacy/gdpr3.png)
3.  I Schemaläggaren högerklickar du på **Schemabiblioteket** och klickar på **Skapa grundläggande aktivitet...**
4.  Ange namnet på den nya uppgiften och klicka på **Nästa**.
5.  Välj **Dagligen** för aktivitetsutlösaren och klicka på **Nästa**.
6.  Ange upprepningen till **2 dagar** och klicka på **Nästa**.
7.  Välj **Starta ett program** som åtgärd och klicka på **Nästa**.
8.  Skriv **PowerShell** i rutan för programmet/skriptet och i rutan **Lägg till argument (valfritt)** anger du den fullständiga sökvägen till skriptet som du skapade tidigare och klickar sedan på **Nästa**.
9.  På nästa skärm visas en sammanfattning av den uppgift som du ska skapa. Verifiera värdena och klicka på **Slutför** för att skapa uppgiften.



## <a name="next-steps"></a>Nästa steg
* [Läs Microsofts sekretesspolicy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
* [Azure AD Connect-hälsa och användarsekretess](reference-connect-health-user-privacy.md)
