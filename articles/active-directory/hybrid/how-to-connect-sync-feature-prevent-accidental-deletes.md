---
title: 'Azure AD Connect-synkronisering: Förhindra oavsiktliga borttagningar | Microsoft Docs'
description: Det här avsnittet beskriver funktionen förhindra oavsiktliga borttagningar (förhindra oavsiktliga borttagningar) i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 55f208b6fda85d82456694279cf22537a423782e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495487"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect-synkronisering: Förhindra oavsiktliga borttagningar
Det här avsnittet beskriver funktionen förhindra oavsiktliga borttagningar (förhindra oavsiktliga borttagningar) i Azure AD Connect.

När du installerar Azure AD Connect, förhindra oavsiktliga borttagningar är aktiverat som standard och konfigurerad för att tillåta en export med fler än 500 borttagningar. Den här funktionen är avsedd att skydda dig från oavsiktliga konfigurationsändringar och ändringar i din lokala katalog som kan påverka många användare och andra objekt.

## <a name="what-is-prevent-accidental-deletes"></a>Vad är förhindra oavsiktliga borttagningar
Vanliga scenarier när du ser många borttagningar som omfattar:

* Ändringar i [filtrering](how-to-connect-sync-configure-filtering.md) där en hel [OU](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) eller [domän](how-to-connect-sync-configure-filtering.md#domain-based-filtering) är avmarkerat.
* Alla objekt i en organisationsenhet tas bort.
* En organisationsenhet ges nytt namn så att alla objekt i den anses hamna utanför synkroniseringens omfång.

Standardvärdet för 500 objekt kan ändras med PowerShell med hjälp av `Enable-ADSyncExportDeletionThreshold`, vilket är en del av AD Sync-modulen installeras med Azure Active Directory Connect. Du bör konfigurera det här värdet efter storleken på din organisation. Eftersom sync scheduler körs var 30: e minut, är värdet antalet borttagningar visas inom 30 minuter.

Om det finns för många borttagningar mellanlagrad för att exporteras till Azure AD kan stoppas av exporten och du får ett e-postmeddelande så här:

![Förhindra oavsiktliga borttagningar e-post](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hello (teknisk kontakt). Vid (tidpunkt) upptäckte synkroniseringstjänsten identitet att antal borttagningar överskridit konfigurerade borttagning av tröskelvärdet för (organisationsnamn). Ett antal för (tal) objekt har skickats för borttagning i den här identitetssynkronisering kör. Detta har uppnåtts eller överskridits tröskelvärdet konfigurerade borttagning av objekt (nummer). Du måste intyga att borttagningarna ska bearbetas innan vi fortsätter. Se förhindrar oavsiktliga borttagningar för mer information om felet visas i e-postmeddelandet.*
>
> 

Du kan också se status `stopped-deletion-threshold-exceeded` när du tittar i den **hanteraren för synkroniseringstjänsten** Användargränssnittet för Export-profilen.
![Förhindra oavsiktliga borttagningar Sync Service Manager-UI](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Om det var oväntat kan undersöka och vidta åtgärder. Om du vill se vilka objekt som kommer att tas bort, gör du följande:

1. Starta **synkroniseringstjänsten** från Start-menyn.
2. Gå till **Anslutningsappar**.
3. Välj kopplingen med typen **Azure Active Directory**.
4. Under **åtgärder** till höger, Välj **Search Connector Space**.
5. I popup-fönstret under **omfång**väljer **kopplas från eftersom** och välj en tid i förflutna. Klicka på **Sök**. Den här sidan ger en överblick över alla objekt kommer att tas bort. Genom att klicka på varje objekt, kan du få ytterligare information om objektet. Du kan också klicka på **kolumnen inställningen** att lägga till fler attribut för att vara synliga i rutnätet.

![Search Connector Space](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

Om alla borttagningar önskas, gör du följande:

1. Kör PowerShell-cmdleten för att hämta det aktuella tröskelvärdet för borttagning, `Get-ADSyncExportDeletionThreshold`. Ange en Global administratör för Azure AD-konto och lösenord. Standardvärdet är 500.
2. Att tillfälligt inaktivera skyddet och låta dessa borttagningar gå igenom, Kör PowerShell-cmdlet: `Disable-ADSyncExportDeletionThreshold`. Ange en Global administratör för Azure AD-konto och lösenord.
   ![Autentiseringsuppgifter](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Med den Azure Active Directory-koppling fortfarande markerat, Välj åtgärden **kör** och välj **exportera**.
4. Om du vill återaktivera skyddet, Kör PowerShell-cmdlet: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Ersätt 500 med värdet som du har märkt vid hämtning av det aktuella tröskelvärdet för borttagning. Ange en Global administratör för Azure AD-konto och lösenord.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
