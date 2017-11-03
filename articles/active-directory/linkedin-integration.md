---
title: Konfigurera LinkedIn-integrering i Azure AD | Microsoft Docs
description: "Beskriver hur du aktiverar eller inaktiverar LinkedIn-integrering för Microsoft-appar i Azure Active Directory."
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 48b26bfcce67ce915c404a0ab2ac0f399c3b821d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Aktivera LinkedIn-integrering i Azure Active Directory
Aktivera LinkedIn-integrering kan användarna åtkomst till både offentliga LinkedIn-data och, om företaget väljer att deras personliga LinkedIn-nätverket från Microsoft-appar. Varje användare kan välja separat ansluta sitt arbetskonto till deras LinkedIn-konto.

### <a name="linkedin-integration-from-your-end-users-perspective"></a>LinkedIn integration från dina slutanvändare perspektiv
När slutanvändare i din organisation ansluter sina LinkedIn-konton till sina arbetskonton, är de lättare identifiera de personer som de arbetar med inom och utanför organisationen. Ansluta de två kontona kan användarens LinkedIn-anslutningar och data för användarprofiler som ska användas i Microsoft-appar som din organisation, men de kan alltid välja genom att ta bort behörigheten för LinkedIn att dela data. Integrationen använder också offentligt tillgängliga profilinformation och användarna kan välja om du vill dela sina offentliga profil- och med Microsoft-program via LinkedIn sekretessinställningar.

>[!NOTE]
> Aktiverar LinkedIn-integrering i Azure AD kan appar och -tjänster åtkomst till vissa av dina slutanvändare information. Läs den [sekretesspolicy för Microsoft](https://privacy.microsoft.com/privacystatement/) lära dig mer om överväganden för sekretess när du aktiverar LinkedIn-integrering i Azure AD. 

## <a name="enable-linkedin-integration"></a>Aktivera LinkedIn-integrering
LinkedIn-integrering för företag är aktiverad som standard i Azure AD. Så, när den här funktionen görs tillgänglig för din klient, alla användare i din organisation kan se LinkedIn-funktioner och -profiler. Om du aktiverar LinkedIn-integrering kan användare i din organisation använder LinkedIn-funktioner i Microsoft-tjänster, till exempel visa profiler när du får ett e-postmeddelande i Outlook. Inaktiverar den här funktionen förhindrar åtkomst till LinkedIn-funktioner och stoppar användaranslutningar för kontot och Datadelning mellan LinkedIn och din organisation via Microsoft-tjänster.

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig för gå-lokala och statliga myndigheter innehavare. Dessutom Azure AD uppdaterar tjänsten, till exempel LinkedIn integrationsmöjligheter distribueras inte till alla Azure-klienter på samma gång. Du kan inte aktivera LinkedIn-integrering med Azure AD förrän den här funktionen har återställts till din Azure-klient.

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för katalogen.
2. Välj **användare och grupper**.
3. På den **användare och grupper** bladet väljer **användarinställningar**.
4. Under **LinkedIn Integration**, Välj Ja eller Nej för att aktivera eller inaktivera LinkedIn-integration.
   ![Aktivera LinkedIn-integrering](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>Läs mer 
* [LinkedIn information och funktioner i Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn hjälp och support](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Nästa steg
Du kan använda följande länk för att aktivera eller inaktivera LinkedIn-integrering med Azure AD från Azure portal:

[Konfigurera LinkedIn-integrering](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 