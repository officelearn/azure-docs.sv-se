---
title: Verktyget What If för villkorlig åtkomst – Azure Active Directory
description: Lär dig hur du kan förstå effekten av dina principer för villkorlig åtkomst i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc87b434664ba12cefeb233972e749f631d8a2b4
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620689"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Felsöka med hjälp av What If-verktyget i villkorlig åtkomst

[Villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) är en funktion i Azure Active Directory (Azure AD) som gör att du kan styra hur auktoriserade användare får åtkomst till dina molnappar. Hur vet du vad du kan förväntar dig från principer för villkorlig åtkomst i din miljö? Om du vill besvara den här frågan kan du använda **verktyget för villkorlig åtkomst what if**.

Den här artikeln förklarar hur du kan använda det här verktyget för att testa dina principer för villkorlig åtkomst.

## <a name="what-it-is"></a>Vad det är

Med **verktyget för what if princip för villkorlig åtkomst** kan du förstå effekten av dina principer för villkorlig åtkomst på din miljö. I stället för att testa att köra dina principer genom att utföra flera inloggningar manuellt kan du använda det här verktyget för att utvärdera en simulerad inloggning av en användare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport. Rapporten visar inte bara tillämpade principer för villkorlig åtkomst, utan även [klassiska principer](policy-migration.md#classic-policies) om de finns.    

**What If** -verktyget är ett sätt att snabbt fastställa de principer som gäller för en speciell användare. Du kan använda informationen, till exempel om du behöver felsöka ett problem.    

## <a name="how-it-works"></a>Hur det fungerar

I **What If verktyget för villkorlig åtkomst**måste du först konfigurera inställningarna för det inloggnings scenario som du vill simulera. Inställningarna omfattar:

- Den användare som du vill testa 
- De molnappar som användaren försöker få åtkomst till
- De villkor under vilka åtkomst till apparna för att konfigurera molnet utförs
     
Som nästa steg kan du starta en simulerings körning som utvärderar dina inställningar. Endast principer som är aktiverade är en del av en utvärderings körning.

När utvärderingen är färdig genererar verktyget en rapport över de berörda principerna.

## <a name="running-the-tool"></a>Köra verktyget

Du hittar **What If** -verktyget på sidan **[villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** i Azure Portal.

Starta verktyget genom att klicka på **What If**i verktygsfältet överst i listan över principer.

![WhatIf](./media/what-if-tool/01.png)

Innan du kan köra en utvärdering måste du konfigurera inställningarna.

## <a name="settings"></a>Inställningar

Det här avsnittet innehåller information om inställningarna för simulerings körning.

![WhatIf](./media/what-if-tool/02.png)

### <a name="user"></a>Användare

Du kan bara välja en användare. Detta är det enda obligatoriska fältet.

### <a name="cloud-apps"></a>Molnappar

Standardinställningen för den här inställningen är **alla molnappar**. Standardinställningen utför en utvärdering av alla tillgängliga principer i din miljö. Du kan begränsa omfattningen till principer som påverkar specifika molnappar.

### <a name="ip-address"></a>IP-adress

IP-adressen är en enskild IPv4-adress för att efterlikna [plats villkoret](location-condition.md). Adressen representerar Internet adressen till den enhet som används av din användare för att logga in. Du kan kontrol lera IP-adressen för en enhet genom att t. ex. Navigera till [Vad är min IP-adress](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Enhets plattformar

Den här inställningen imiterar [villkoret för enhets plattformar](concept-conditional-access-conditions.md#device-platforms) och motsvarar **alla plattformar (inklusive ej stödda)** . 

### <a name="client-apps"></a>Klient program

Med den här inställningen imiteras [villkoret för klient program](concept-conditional-access-conditions.md#client-apps-preview).
Som standard gör den här inställningen en utvärdering av alla principer som har **webbläsare** eller **mobilappar och skriv bords klienter,** antingen individuellt eller båda markerade. Den identifierar också principer som tillämpar **Exchange ActiveSync (EAS)** . Du kan begränsa den här inställningen genom att välja:

- **Webbläsare** för att utvärdera alla principer som har minst **webbläsare** vald. 
- **Mobilappar och skriv bords klienter** för att utvärdera alla principer som har minst **mobila appar och skriv bords klienter** valda. 

### <a name="sign-in-risk"></a>Inloggnings risk

Den här inställningen imiterar [inloggnings risk villkoret](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Version 

Du startar en utvärdering genom att klicka på **What If**. Utvärderings resultatet ger dig en rapport som består av: 

![WhatIf](./media/what-if-tool/03.png)

- En indikator om de klassiska principerna finns i din miljö
- Principer som gäller för din användare
- Principer som inte gäller för din användare

Om det finns [klassiska principer](policy-migration.md#classic-policies) för de valda molnappar visas en indikator för dig. Genom att klicka på indikatorn omdirigeras du till sidan klassiska principer. På sidan klassiska principer kan du migrera en klassisk princip eller bara inaktivera den. Du kan återgå till utvärderings resultatet genom att stänga den här sidan.

I listan över principer som gäller för din valda användare kan du också hitta en lista över de [kontroll](concept-conditional-access-grant.md) -och [sessionsnycklar](concept-conditional-access-session.md) som användaren måste uppfylla.

I listan över principer som inte gäller för din användare kan du även se varför dessa principer inte tillämpas. För varje listad princip representerar orsaken det första villkoret som inte uppfylldes. En möjlig orsak till en princip som inte tillämpas är en inaktive rad princip eftersom de inte utvärderas ytterligare.   

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst, se [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö, se [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md). 
- Om du vill migrera klassiska principer, se [migrera klassiska principer i Azure Portal](policy-migration.md)  
