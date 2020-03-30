---
title: 'Synkronisering av Azure AD Connect: Förhindra oavsiktliga borttagningar | Microsoft-dokument'
description: I det här avsnittet beskrivs funktionen förhindra oavsiktliga borttagningar (förhindra oavsiktliga borttagningar) i Azure AD Connect.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f3109b4c87e25444629ca25411894eab8a9d56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71827132"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect-synkronisering: Förhindra oavsiktliga borttagningar
I det här avsnittet beskrivs funktionen förhindra oavsiktliga borttagningar (förhindra oavsiktliga borttagningar) i Azure AD Connect.

När du installerar Azure AD Connect aktiveras förhindrande av oavsiktlig borttagning som standard och konfigureras så att en export med mer än 500 borttagningar inte tillåts. Den här funktionen är avsedd att skydda dig från oavsiktliga konfigurationsändringar och ändringar i din lokala katalog som kan påverka många användare och andra objekt.

## <a name="what-is-prevent-accidental-deletes"></a>Vad är förhindra oavsiktliga borttagningar
Vanliga scenarier när många borttagningar visas är:

* Ändringar i [filtrering](how-to-connect-sync-configure-filtering.md) där en hel [organisationsenhet](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) eller [domän](how-to-connect-sync-configure-filtering.md#domain-based-filtering) inte är vald.
* Alla objekt i en organisationsenhet tas bort.
* En organisationsenhet ges nytt namn så att alla objekt i den anses hamna utanför synkroniseringens omfång.

Standardvärdet för 500 objekt kan ändras `Enable-ADSyncExportDeletionThreshold`med PowerShell med , som är en del av AD Sync-modulen installerad med Azure Active Directory Connect. Du bör konfigurera det här värdet så att det passar organisationens storlek. Eftersom synkroniseringsschemat körs var 30:e minut är värdet antalet borttagningar som visas inom 30 minuter.

Om det finns för många borttagningar som ska mellanlagras för att exporteras till Azure AD, stoppas exporten och du får ett e-postmeddelande som detta:

![Förhindra oavsiktlig radering av e-post](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hej (teknisk kontakt). Vid (tillfället) upptäckte tjänsten Identitetssynkronisering att antalet borttagningar överskred det konfigurerade tröskelvärdet för borttagning för (organisationsnamn). Totalt (antal) objekt skickades för borttagning i den här identitetssynkroniseringskörningen. Detta uppfyllde eller överskred det konfigurerade tröskelvärdet för borttagning av (antal) objekt. Vi vill att du bekräftar att dessa strykningar bör behandlas innan vi fortsätter. Se förhindra oavsiktliga borttagningar för mer information om felet i det här e-postmeddelandet.*
>
> 

Du kan också `stopped-deletion-threshold-exceeded` se status när du tittar i användargränssnittet för **synkroniseringstjänsthanteraren** för profilen Exportera.
![Förhindra att synkroniseringstjänsthanterarens användargränssnitt tas bort oavsiktligt](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Om detta var oväntat, sedan undersöka och vidta korrigerande åtgärder. Så här ser du vilka objekt som ska tas bort:

1. Starta **synkroniseringstjänsten** från Start-menyn.
2. Gå till **Kopplingar**.
3. Välj anslutningsappen med typen **Azure Active Directory**.
4. Under **Åtgärder** till höger väljer du **Sökkopplingsutrymme**.
5. I popup-programmet under **Scope**väljer du **Frånkopplad sedan** och väljer en tid tidigare. Klicka på **Sök**. Den här sidan innehåller en vy över alla objekt som ska tas bort. Genom att klicka på varje objekt kan du få ytterligare information om objektet. Du kan också klicka på **Kolumninställning** om du vill lägga till ytterligare attribut som ska visas i rutnätet.

![Utrymme för sökkoppling](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Om du inte är säker på att alla bortgångar önskas, och vill gå en säkrare väg. Du kan använda PowerShell-cmdlet: `Enable-ADSyncExportDeletionThreshold` för att ange ett nytt tröskelvärde i stället för att inaktivera tröskelvärdet som kan tillåta oönskade borttagningar. 

## <a name="if-all-deletes-are-desired"></a>Om alla borttagningar önskas
Om alla borttagningar önskas gör du följande:

1. Om du vill hämta det aktuella tröskelvärdet för borttagning kör du PowerShell-cmdleten `Get-ADSyncExportDeletionThreshold`. Ange ett globalt Azure AD-administratörskonto och lösenord. Standardvärdet är 500.
2. Om du tillfälligt vill inaktivera det här skyddet och låta dessa `Disable-ADSyncExportDeletionThreshold`borttagningar gå igenom kör du PowerShell-cmdlet: . Ange ett globalt Azure AD-administratörskonto och lösenord.
   ![Autentiseringsuppgifter](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. När Azure Active Directory Connector fortfarande är markerat väljer du åtgärden **Kör** och väljer **Exportera**.
4. Om du vill återaktivera skyddet kör du `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`PowerShell-cmdlet: . Ersätt 500 med det värde du lade märke till när du hämtade det aktuella borttagningströskeln. Ange ett globalt Azure AD-administratörskonto och lösenord.

## <a name="next-steps"></a>Nästa steg
**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
