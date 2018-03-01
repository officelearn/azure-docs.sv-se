---
title: "Azure AD Connect och allmänna Data Protection förordning | Microsoft Docs"
description: "Det här dokumentet beskriver hur du skaffar BNPR kompatibilitet med Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c3956dd379961b119f65bdebe1f5a8038c4fa8f0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect"></a>BNPR efterlevnad och Azure AD Connect 

I kan 2018, Europeiska sekretesslagstiftning den [allmänna Data Protection förordning (BNPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm), upphör att gälla. BNPR inför nya regler på företag, myndigheter, icke-vinst och andra organisationer att erbjudande varor och tjänster till personer i Europeiska unionen (EU), eller att samla in och analysera data som är knutna till Europa boende. BNPR gäller oavsett där du befinner dig. 

Microsoftprodukter och tjänster finns i dag som hjälper dig att uppfylla kraven BNPR. Läs mer om Microsoft Privacy-policy på [Säkerhetscenter](https://www.microsoft.com/trustcenter)

>[!NOTE] 
>Den här artikeln handlar om Azure AD Connect och BNPR kompatibilitet.  Information om Azure AD Connect Health och BNPR kompatibilitet finns i artikeln [här](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

Allmänna Data Protection förordning kompatibilitet för Azure AD Connect installationer kan nås på två sätt:

1.  Extrahera data för en person på begäran, och ta bort data från den personen från installationer
2.  Se till att inga data sparas 48 timmar.

Azure AD Connect-teamet rekommenderar det andra alternativet eftersom det är mycket enklare att implementera och underhålla.

En Azure AD Connect sync-servern lagrar följande data som ingår i omfånget för BNPR kompatibilitet:
1.  Data om en person i den **Azure AD Connect-databas**
2.  Data i den **Windows-händelseloggen** filer som kan innehålla information om en person
3.  Data i den **Azure AD Connect installationsloggfiler** som kan innehålla om en person

Azure AD Connect-kunder bör ska BNPR kompatibla, Använd följande riktlinjer:
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

1.  Spara skriptet i en fil med tillägget **& #46. Ps1**, öppna Kontrollpanelen och klicka på **system och säkerhet**.
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
- [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
- [Azure AD Connect Health och BNPR](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
