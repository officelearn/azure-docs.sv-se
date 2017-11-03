---
title: "Azure AD Connect-synkronisering: förhindra oavsiktliga borttagningar | Microsoft Docs"
description: "Det här avsnittet beskriver funktionen förhindra oavsiktliga borttagningar (förhindra oavsiktliga borttagningar) i Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: a33fb729cff5007e40820af696cfec823a3ecfde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect-synkronisering: Förhindra oavsiktliga borttagningar
Det här avsnittet beskriver funktionen förhindra oavsiktliga borttagningar (förhindra oavsiktliga borttagningar) i Azure AD Connect.

När du installerar Azure AD Connect förhindra oavsiktliga borttagningar är aktiverad som standard och konfigurerad för att tillåta en export med mer än 500 borttagningar. Den här funktionen är avsedd att skydda dig från oavsiktliga konfigurationsändringar och ändringar till din lokala katalog som påverkar många användare och andra objekt.

## <a name="what-is-prevent-accidental-deletes"></a>Vad är förhindra oavsiktliga borttagningar
Vanliga scenarier när du ser många borttagningar inkluderar:

* Ändringar av [filtrering](active-directory-aadconnectsync-configure-filtering.md) där en hel [OU](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) eller [domän](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) är avmarkerat.
* Alla objekt i en Organisationsenhet tas bort.
* En Organisationsenhet byter namn så att alla objekt i den anses vara utanför omfånget för synkronisering.

Standardvärdet för 500 objekt kan ändras med PowerShell med hjälp av `Enable-ADSyncExportDeletionThreshold`. Du bör konfigurera det här värdet efter storleken på din organisation. Eftersom sync scheduler körs var 30: e minut, är värdet för antal borttagningar visas inom 30 minuter.

Om det finns för många borttagningar mellanlagrad för att exporteras till Azure AD kan sedan exportera stoppar och du får ett e-postmeddelande så här:

![Förhindra oavsiktliga borttagningar e-post](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Hello (tekniska kontakt). Kl upptäckte synkroniseringstjänsten identitet att antalet borttagningar överskridit den konfigurerade borttagning tröskeln för (organisationsnamn). Totalt (nummer) objekt har skickats för borttagning i den här identitetssynkronisering kör. Detta uppnåtts eller överskridits tröskelvärdet konfigurerade borttagning av objekt (nummer). Vi måste du bekräfta att borttagningarna ska bearbetas innan vi fortsätter. Se förhindra oavsiktliga borttagningar mer information om fel som visas i e-postmeddelandet.*
>
> 

Du kan också se status `stopped-deletion-threshold-exceeded` när du tittar den **Synchronization Service Manager** Användargränssnittet för Export-profilen.
![Förhindra oavsiktliga borttagningar Sync Service Manager UI](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Om det var oväntat kan undersöka och vidta åtgärder. Om du vill se vilka objekt som håller på att tas bort, gör du följande:

1. Starta **synkroniseringstjänsten** från Start-menyn.
2. Gå till **kopplingar**.
3. Välj kopplingen med typen **Azure Active Directory**.
4. Under **åtgärder** till höger, Välj **söka Anslutarplats**.
5. I popup-fönstret under **omfång**väljer **kopplas från eftersom** och välj en gång tidigare. Klicka på **Sök**. Den här sidan innehåller en vy över alla objekt som kommer att tas bort. Genom att klicka på varje objekt, kan du få ytterligare information om objektet. Du kan också klicka på **kolumnen inställningen** att lägga till ytterligare attribut som ska visas i rutnätet.

![Söka Anslutarplats](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Om alla borttagningar önskas, gör du följande:

1. Kör PowerShell-cmdlet för att hämta det aktuella tröskelvärdet för borttagning `Get-ADSyncExportDeletionThreshold`. Ange en Global administratör för Azure AD-konto och lösenord. Standardvärdet är 500.
2. Att tillfälligt inaktivera skyddet och låta de borttagningarna gå igenom, Kör PowerShell-cmdlet: `Disable-ADSyncExportDeletionThreshold`. Ange en Global administratör för Azure AD-konto och lösenord.
   ![Autentiseringsuppgifter](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Med Azure Active Directory-kopplingen fortfarande markerat, Välj åtgärden som **kör** och välj **exportera**.
4. Om du vill återaktivera skydd, Kör PowerShell-cmdlet: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Ersätt 500 med värdet som du har lagt märke vid hämtning av det aktuella tröskelvärdet för borttagning. Ange en Global administratör för Azure AD-konto och lösenord.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
