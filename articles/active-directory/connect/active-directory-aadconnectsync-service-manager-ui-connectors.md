---
title: "Kopplingar i Azure AD Synchronization Service Manager-Gränssnittet | Microsoft Docs"
description: "Förstå fliken kopplingar i hanteraren för synkroniseringstjänsten för Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c0fae4b1755ca95466eeffb5ce61c1c7855d7381
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Med hjälp av anslutningar med Azure AD Connect Sync Service Manager

![Synkronisering av Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Fliken kopplingar används för att hantera alla system Synkroniseringsmotorn är ansluten till.

## <a name="connector-actions"></a>Åtgärder för kopplingen
| Åtgärd | Kommentar |
| --- | --- |
| Skapa |Använd inte. Använd installationsguiden för att ansluta till ytterligare AD-skogar. |
| Egenskaper |Används för domän- och organisationsenhetsfiltrering. |
| [Ta bort](#delete) |För att ta bort data i anslutningsplatsen eller ta bort anslutningen till en skog. |
| [Konfigurera körningsprofiler](#configure-run-profiles) |Förutom domän filtrering, inget att konfigurera här. Du kan använda den här åtgärden för att se redan konfigurerade körning av profiler. |
| Kör |Används för att starta en oneoff körning av en profil. |
| Stoppa |Stoppar en koppling som körs på en profil. |
| Exportera koppling |Använd inte. |
| Importera koppling |Använd inte. |
| Uppdatera anslutningen |Använd inte. |
| Uppdatera Schema |Uppdaterar cachelagrade schemat. Det är att föredra att använda alternativet i installationsguiden i stället eftersom som också uppdateringar synkroniseras regler. |
| [Söka Anslutarplats](#search-connector-space) |Används för att söka efter objekt och [följer ett objekt och dess data genom systemet](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Ta bort
Åtgärden ta bort används för två olika saker.  
![Synkronisering av Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Alternativet **ta bort anslutningsplatsen endast** tar bort alla data, men behålla konfigurationen.

Alternativet **ta bort kopplingen och connector** tar bort data och konfiguration. Det här alternativet används när du inte vill ansluta till en skog längre.

Båda alternativen synkronisera alla objekt och uppdatera metaversum-objekt. Den här åtgärden är en tidskrävande åtgärd.

### <a name="configure-run-profiles"></a>Konfigurera körningsprofiler
Det här alternativet kan du se körningsprofiler som konfigurerats för en koppling.

![Synkronisering av Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Söka Anslutarplats
Sökåtgärd connector utrymme är användbar för att söka efter objekt och felsöka problem.

![Synkronisering av Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Starta genom att välja en **omfång**. Du kan söka baserat på data (RDN DN-fästpunkt, underträd) eller läget för objektet (alla andra alternativ).  
![Synkronisering av Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Om du till exempel göra en underträd sökning får du alla objekt i en Organisationsenhet.  
![Synkronisering av Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
Från den här rutnät som du kan välja ett objekt, Välj **egenskaper**, och [följa den](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) från anslutningsplatsen källa via metaversum och att målet anslutningsplatsen.

### <a name="changing-the-ad-ds-account-password"></a>Ändra lösenordet för AD DS
Om du ändrar lösenordet för synkroniseringstjänsten inte längre att kunna importera och exportera ändringar i lokala AD.   Du kan se följande:

- Importera och exportera steget för AD-anslutningen misslyckas med felet ”inga-start-autentiseringsuppgifter”.
- Under Windows Loggboken innehåller programhändelseloggen ett fel med händelse-ID 6000 och meddelandet ”hanteringsagenten” contoso.com ”kunde inte köras eftersom autentiseringsuppgifterna var ogiltiga”.

Lös problemet genom att uppdatera AD DS-användarkonto med hjälp av följande:


1. Starta hanteraren för synkroniseringstjänsten (START → synkroniseringstjänsten).
</br>![Synkronisering av Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)
2. Gå till den **kopplingar** fliken.
3. Välj den AD-anslutning som är konfigurerat för att använda AD DS-konto.
4. Välj under åtgärder, **egenskaper**.
5. Välj Anslut till Active Directory-skog i popup-fönstret:
6. Skogens namn anger det motsvarande lokalt AD.
7. Användarnamnet anger AD DS-konto som används för synkronisering.
8. Ange det nya lösenordet för AD DS-konto i lösenordsrutan ![Azure AD Connect Sync kryptering nyckeln Utility](media/active-directory-aadconnectsync-encryption-key/key6.png)
9. Klicka på OK för att spara det nya lösenordet och starta om synkroniseringstjänsten för att ta bort det gamla lösenordet från cacheminnet.



## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
