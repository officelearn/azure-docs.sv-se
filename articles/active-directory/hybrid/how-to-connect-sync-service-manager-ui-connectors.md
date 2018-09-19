---
title: Kopplingar i Användargränssnittet för Azure AD hanteraren för synkroniseringstjänsten | Microsoft Docs
description: Förstå fliken för anslutningar i hanteraren för synkroniseringstjänsten för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 592fcd80b80ecbd101dd559aa8baf78a60aaa517
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312629"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Med hjälp av kopplingar med Azure AD Connect Sync Service Manager

![Synkronisering av Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Fliken för anslutningar används för att hantera alla system som Synkroniseringsmotorn är ansluten till.

## <a name="connector-actions"></a>Anslutningsappsåtgärder
| Åtgärd | Kommentar |
| --- | --- |
| Skapa |Använd inte. Använd installationsguiden för att ansluta till ytterligare AD-skogar. |
| Egenskaper |Används för domän- och OU-filtrering. |
| [Ta bort](#delete) |Används för att ta bort data i anslutarplatsen eller att ta bort anslutningen till en skog. |
| [Konfigurera körningsprofiler](#configure-run-profiles) |Förutom domän som filtrering, och inget att konfigurera här. Du kan använda den här åtgärden för att se redan konfigurerade körning av profiler. |
| Kör |Används för att starta en oneoff körning av en profil. |
| Stoppa |Stoppar en koppling som körs på en profil. |
| Exportera Connector |Använd inte. |
| Importera Connector |Använd inte. |
| Uppdatera anslutningsprogrammet |Använd inte. |
| Uppdatera schema |Uppdaterar cachelagrade schemat. Det rekommenderas att använda alternativet i installationsguiden i stället sedan som också uppdateringar synkroniseras regler. |
| [Search Connector Space](#search-connector-space) |Används för att hitta objekt och [följer ett objekt och dess data genom systemet](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Ta bort
Borttagningsåtgärden används för två olika saker.  
![Synkronisering av Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Alternativet **ta bort anslutningsplatsen endast** tar bort alla data, men behålla konfigurationen.

Alternativet **ta bort koppling och connector utrymme** tar bort data och konfigurationen. Det här alternativet används när du inte vill ansluta till en skog längre.

Båda alternativen synkronisera alla objekt och uppdatera metaversum-objekt. Den här åtgärden är en tidskrävande åtgärd.

### <a name="configure-run-profiles"></a>Konfigurera körningsprofiler
Det här alternativet kan du se de körningsprofiler som konfigurerats för en koppling.

![Synkronisering av Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Search Connector Space
Search connector utrymme åtgärden är användbar för att hitta objekt och felsöka problem med.

![Synkronisering av Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Starta genom att välja en **omfång**. Du kan söka baserat på data (RDN DN, fästpunkt, underträd), eller för objektet (alla andra alternativ).  
![Synkronisering av Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Om du till exempel göra en underträd sökning får du alla objekt i en Organisationsenhet.  
![Synkronisering av Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Det här rutnätet som du kan markera ett objekt, Välj **egenskaper**, och [följer den](tshoot-connect-object-not-syncing.md) från anslutningsplatsen källa genom metaversum och till mål-anslutningsplatsen.

### <a name="changing-the-ad-ds-account-password"></a>Ändra AD DS-kontolösenordet
Om du ändrar lösenordet för synkroniseringstjänsten inte längre att kunna importera/exportera ändringar till den lokala AD.   Du kan se följande:

- Import/export-steg för AD-anslutningen misslyckas med felet ”inga-start-autentiseringsuppgifter”.
- Under Windows Loggboken innehåller programhändelseloggen ett fel med händelse-ID 6000 och meddelandet ”hanteringsagenten” contoso.com ”misslyckades att köra eftersom autentiseringsuppgifterna var ogiltiga”.

Lös problemet genom att uppdatera det AD DS-konto med hjälp av följande:


1. Starta hanteraren för synkroniseringstjänsten (START → Synchronization Service).
</br>![Synkronisering av Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Gå till den **kopplingar** fliken.
3. Välj den AD-koppling som är konfigurerat för att använda AD DS-kontot.
4. Välj under åtgärder, **egenskaper**.
5. I popup-, väljer du Anslut till Active Directory-skogar:
6. Skogens namn anger det motsvarande lokalt AD.
7. Användarnamnet anger AD DS-kontot som används för synkroniseringen.
8. Ange det nya lösenordet för AD DS-kontot i textrutan lösenord ![Azure AD Connect Sync kryptering nyckel Utility](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Klicka på OK för att spara det nya lösenordet och starta om synkroniseringstjänsten för att ta bort det gamla lösenordet från cacheminnet.



## <a name="next-steps"></a>Nästa steg
Läs mer om den [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
