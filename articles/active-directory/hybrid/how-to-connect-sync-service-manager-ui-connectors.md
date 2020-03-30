---
title: Kopplingar i Azure AD-synkroniseringstjänsthanterarens användargränssnitt | Microsoft Dokument"
description: Förstå fliken Kopplingar i Synkroniseringstjänsthanteraren för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae932191c7b76590ea217386dfd729add5566f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261052"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Använda kopplingar med Azure AD Connect Sync Service Manager

![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Fliken Kontakter används för att hantera alla system som synkroniseringsmotorn är ansluten till.

## <a name="connector-actions"></a>Anslutningsåtgärder
| Åtgärd | Kommentar |
| --- | --- |
| Skapa |Använd inte. Använd installationsguiden för att ansluta till ytterligare AD-skogar. |
| Egenskaper |Används för domän- och organisationsenhetsfiltrering. |
| [Ta bort](#delete) |Används för att antingen ta bort data i anslutningsutrymmet eller för att ta bort anslutningen till en skog. |
| [Konfigurera körningsprofiler](#configure-run-profiles) |Med undantag för domänfiltrering, inget att konfigurera här. Du kan använda den här åtgärden för att se redan konfigurerade körningsprofiler. |
| Kör |Används för att starta en engångskörning av en profil. |
| Stoppa |Stoppar en koppling som för närvarande kör en profil. |
| Exportera koppling |Använd inte. |
| Importera koppling |Använd inte. |
| Uppdatera koppling |Använd inte. |
| Uppdatera schema |Uppdaterar det cachelagrade schemat. Det är att föredra att använda alternativet i installationsguiden i stället, eftersom det också uppdaterar synkroniseringsregler. |
| [Utrymme för sökkoppling](#search-connector-space) |Används för att hitta objekt och för att följa ett objekt och dess data genom systemet. |

### <a name="delete"></a>Ta bort
Borttagningsåtgärden används för två olika saker.  
![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Alternativet **Ta bort anslutningsutrymme** tar bara bort alla data, men behåller konfigurationen.

Alternativet **Ta bort anslutnings- och anslutningsutrymme** tar bort data och konfigurationen. Det här alternativet används när du inte längre vill ansluta till en skog.

Båda alternativen synkroniserar alla objekt och uppdaterar metaversumobjekten. Den här åtgärden är en tidskrävande åtgärd.

### <a name="configure-run-profiles"></a>Konfigurera körningsprofiler
Med det här alternativet kan du se de körningsprofiler som konfigurerats för en koppling.

![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Utrymme för sökkoppling
Åtgärden sökkopplingsutrymme är användbar för att hitta objekt och felsöka dataproblem.

![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Börja med att välja ett **scope**. Du kan söka baserat på data (RDN, DN, Anchor, Sub-Tree) eller objektets tillstånd (alla andra alternativ).  
![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Om du till exempel gör en subträdssökning får du alla objekt i en organisationsenhet.  
![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Från det här rutnätet kan du markera ett objekt, välja **egenskaper**och [följa det](tshoot-connect-object-not-syncing.md) från källkopplingsutrymmet, genom metaversumet och till målkopplingsutrymmet.

### <a name="changing-the-ad-ds-account-password"></a>Ändra AD DS-kontolösenordet
Om du ändrar lösenordet för kontot kan synkroniseringstjänsten inte längre importera/exportera ändringar till lokala AD.   Du kan se följande:

- Import-/exportsteget för AD-kopplingen misslyckas med felet "no-start-credentials".
- Under Windows Loggboken innehåller programhändelseloggen ett fel med händelse-ID 6000 och meddelandet "Hanteringsagenten "contoso.com" kunde inte köras eftersom autentiseringsuppgifterna var ogiltiga.

LÃ¶s problemet genom att uppdatera AD DS-användarkontot med fÃ¶1 er:


1. Starta synkroniseringstjänsthanteraren (START → Synkroniseringstjänst).
</br>![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Gå till fliken **Kontakter.**
3. Välj AD Connector som är konfigurerad för att använda AD DS-kontot.
4. Välj **Egenskaper**under Åtgärder .
5. Välj Anslut till Active Directory Forest i popup-dialogrutan:
6. Skogsnamnet anger motsvarande lokalt AD.
7. Användarnamnet anger det AD DS-konto som används för synkronisering.
8. Ange det nya lösenordet för AD DS-kontot i textrutan ![Lösenord Azure AD Connect Sync Encryption Key Utility](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Klicka på OK om du vill spara det nya lösenordet och starta om synkroniseringstjänsten för att ta bort det gamla lösenordet från minnescachen.



## <a name="next-steps"></a>Nästa steg
Läs mer om synkroniseringskonfigurationen för [Azure AD Connect.](how-to-connect-sync-whatis.md)

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
