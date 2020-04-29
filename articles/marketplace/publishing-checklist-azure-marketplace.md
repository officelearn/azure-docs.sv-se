---
title: Publicera check lista för Azure Marketplace | Azure
description: Publicera check lista för Azure Marketplace med Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: dsindona
ms.openlocfilehash: 0409933a3cd4bb535427cf61bd06207d53d5a96e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387724"
---
# <a name="publishing-checklist-for-azure-marketplace"></a>Publicera checklista för Azure Marketplace    
Granska nödvändiga komponenter innan du startar publicerings processen.  

Följande artefakter krävs för att slutföra publicerings arbets flödet skapa erbjudande i Cloud Partner Portal.  

## <a name="checklist"></a>Checklista  

| Listtyp | Erbjudandetyp | Publicerar artefakt |   
|:--- |:--- |:--- |  
| Alla | Alla | <table> <tr><th>Butik-information</th></tr> <tr><td>Erbjudande namn (200 tecken)</td></tr> <tr><td>Beskrivning (2 000 tecken)</td></tr> <tr><td>MPN-ID</td></tr> <tr><td>Tillgänglighet för land/region</td></tr> <tr><td>Lämpliga branscher, kategorier och Sök Nyckelord</td></tr> <tr><td>Skärm bilder (1280x720; max 5)</td></tr> <tr><td>Marknadsförings dokument (max 3)</td></tr> <tr><td>Lead-mål</td></tr> <tr><td>Produkt översikts video (valfritt)</td></tr> </table> <table> <tr><th>Contacts</th></tr> <tr><td>Kontakt information (support, teknik, kommersiell)</td></tr> </table> <table> <tr><th>Teknisk information</th></tr> <tr><td>URL till Användningsvillkor och sekretess policy</td></tr> </table> <table> <tr><th>Test Drive</th></tr> <tr><td>Azure-resursgruppsnamn</td></tr> </table> |  
| Alla | Virtuell dator | <table> <tr><th>Teknisk information</th></tr> <tr><td>Support-URL</td></tr> </table> |
| Visa lista | Konsult tjänst | <table> <tr><th>Butik-information</th></tr> <tr><td>Varaktighet för åtagandet</td></tr> <tr><td>Företags logo typer (48x48, 216x216)</td></tr> </table> |  
| Utvärdering | Alla | <table> <tr><th>Teknisk information</th></tr> <tr><td>Utvärderings-URL</td></tr> <tr><td>Språk som stöds</td></tr> <tr><td>Versions nummer för app</td></tr> <tr><td>Lanserings datum för app</td></tr> <tr><td>Support-URL</td></tr> </table> |  
| Utvärdering | Test enhet | <table> <tr><th>Test Drive</th></tr> <tr><td>Beskrivning</td></tr> <tr><td>Varaktighet</td></tr> <tr><td>Användar handbok</td></tr> <tr><td>Video om Testkör enhet (max 1)</td></tr> <tr><td>Tillgänglighet för land/region för test enhet</td></tr> <tr><td>ID för Azure-prenumeration</td></tr> <tr><td>Azure AD-klient-ID</td></tr> <tr><td>ID för Azure AD-App</td></tr> <tr><td>Azure AD-App-nyckel</td></tr> </table> |  
| Transact | Virtuell dator | <table> <tr><th>Butiker</th></tr> <tr><td>Rubrik (50 tecken)</td></tr> <tr><td>Sammanfattning (200 tecken)</td></tr> <tr><td>Lång Sammanfattning (256 tecken)</td></tr> <tr><td>HTML-baserad Beskrivning (3 000 tecken)</td></tr> <tr><td>Företags logo typer (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Information om operativ system</td></tr> <tr><td>Portar som används</td></tr> <tr><td>Protokoll som används</td></tr> <tr><td>Disk version för varje virtuell hård disk som används</td></tr> <tr><td>SAS-URL för varje virtuell hård disk som används</td></tr> </table> |  
| Transact | Azure Apps: lösnings mal len | <table> <tr><th>Butiker</th></tr> <tr><td>Rubrik (50 tecken)</td></tr> <tr><td>Sammanfattning (200 tecken)</td></tr> <tr><td>Lång Sammanfattning (256 tecken)</td></tr> <tr><td>HTML-baserad Beskrivning (3 000 tecken)</td></tr> <tr><td>Företags logo typer (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Versionsnummer</td></tr> <tr><td>Paketfil som innehåller<ul> <li>alla mallfiler</li> <li>createUIDefinition-fil</li> </ul> </td></tr> </table> |  
| Transact | Azure Apps: hanterad app | <table> <tr><th>Butiker</th></tr> <tr><td>Rubrik (50 tecken)</td></tr> <tr><td>Sammanfattning (200 tecken)</td></tr> <tr><td>Lång Sammanfattning (256 tecken)</td></tr> <tr><td>HTML-baserad Beskrivning (3 000 tecken)</td></tr> <tr><td>Företags logo typer (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Versionsnummer</td></tr> <tr><td>Paketfil som innehåller<ul> <li>alla mallfiler</li> <li>createUIDefinition-fil</li> </ul> </td></tr> </table> |  
| Transact | Container | <table> <tr><th>Butiker</th></tr> <tr><td>Rubrik (50 tecken)</td></tr> <tr><td>Sammanfattning (200 tecken)</td></tr> <tr><td>Lång Sammanfattning (256 tecken)</td></tr> <tr><td>HTML-baserad Beskrivning (3 000 tecken)</td></tr> <tr><td>Företags logo typer (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> <table> <tr><th>SKU</th></tr> <tr><td>Azure Container Registry (ACR) information om avbildnings lagrings plats: prenumerations-ID</td></tr> <tr><td>Information om ACR-avbildnings lagring: resurs grupp namn</td></tr> <tr><td>Information om ACR-avbildnings lagring: register namn</td></tr> <tr><td>Information om ACR-avbildnings lagring: databas namn</td></tr> <tr><td>Information om ACR-avbildnings lagring: användar namn</td></tr> <tr><td>Information om ACR-avbildnings lagring: lösen ord</td></tr> <tr><td>Information om ACR-avbildnings lagring: bildtaggar (valfritt)</td></tr> </table> |  
| Transact | SaaS-app | <table> <tr><th>Butiker</th></tr> <tr><td>Rubrik (50 tecken)</td></tr> <tr><td>Sammanfattning (200 tecken)</td></tr> <tr><td>Lång Sammanfattning (256 tecken)</td></tr> <tr><td>HTML-baserad Beskrivning (3 000 tecken)</td></tr> <tr><td>Företags logo typer (40x40, 90x90, 115x115, 255x115, 815x290)</td></tr> </table> |  

## <a name="next-steps"></a>Nästa steg
*   Besök [Azure Marketplace-och AppSource Publisher-guide](./marketplace-publishers-guide.md) sidan.  
 
---
