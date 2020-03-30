---
title: Verktyget Villkorlig åtkomst vad händer om - Azure Active Directory
description: Läs om hur du kan förstå hur dina principer för villkorlig åtkomst påverkar din miljö.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620689"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Felsöka med verktyget Vad händer i villkorlig åtkomst

[Villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) är en funktion i Azure Active Directory (Azure AD) som gör att du kan styra hur behöriga användare kommer åt dina molnappar. Hur vet du vad du kan förvänta dig av principerna för villkorlig åtkomst i din miljö? Om du vill besvara den här frågan kan du använda **verktyget Villkorlig åtkomst vad händer om**.

I den här artikeln beskrivs hur du kan använda det här verktyget för att testa principerna för villkorlig åtkomst.

## <a name="what-it-is"></a>Vad det är

Med **principverktyget För villkorlig åtkomst vad händer om** kan du förstå hur principerna för villkorlig åtkomst påverkar din miljö. I stället för att testa att köra dina principer genom att utföra flera inloggningar manuellt kan du med det här verktyget utvärdera en simulerad inloggning för en användare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport. Rapporten listar inte bara de tillämpade principerna för villkorlig åtkomst utan även [klassiska principer](policy-migration.md#classic-policies) om sådana finns.    

Verktyget **Vad händer om** är ett sätt att snabbt fastställa de principer som gäller för en viss användare. Du kan till exempel använda informationen om du behöver felsöka ett problem.    

## <a name="how-it-works"></a>Hur det fungerar

I **verktyget Villkorlig åtkomst vad händer om**måste du först konfigurera inställningarna för det inloggningsscenario som du vill simulera. Några exempel på inställningar är:

- Den användare som du vill testa 
- De molnappar som användaren skulle försöka komma åt
- Villkoren för åtkomst till konfigurerar molnappar utförs
     
Som ett nästa steg kan du initiera en simuleringskörning som utvärderar dina inställningar. Endast principer som är aktiverade är en del av en utvärderingskörning.

När utvärderingen är klar genererar verktyget en rapport över de berörda principerna.

## <a name="running-the-tool"></a>Köra verktyget

Du hittar verktyget **Vad händer om** på sidan Villkorlig åtkomst - **[principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** i Azure-portalen.

Om du vill starta verktyget klickar du på **Vad händer om**i verktygsfältet högst upp i listan med principer .

![What If](./media/what-if-tool/01.png)

Innan du kan köra en utvärdering måste du konfigurera inställningarna.

## <a name="settings"></a>Inställningar

Det här avsnittet ger dig information om inställningarna för simuleringskörning.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Användare

Du kan bara välja en användare. Detta är det enda obligatoriska fältet.

### <a name="cloud-apps"></a>Molnappar

Standardinställningen för den här inställningen är **Alla molnappar**. Standardinställningen utför en utvärdering av alla tillgängliga principer i din miljö. Du kan begränsa omfattningen till principer som påverkar specifika molnappar.

### <a name="ip-address"></a>IP-adress

IP-adressen är en enda IPv4-adress för att efterlikna [platsvillkoret](location-condition.md). Adressen representerar internetvänd adress för den enhet som används av användaren för att logga in. Du kan verifiera EN Enhets IP-adress genom att till exempel navigera till [Vad är min IP-adress](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Enhetsplattformar

Den här inställningen härmar [enhetens plattformars tillstånd](concept-conditional-access-conditions.md#device-platforms) och representerar motsvarigheten **till Alla plattformar (inklusive stöds inte)**. 

### <a name="client-apps"></a>Klientappar

Den här inställningen härmar [klientapparnas villkor](concept-conditional-access-conditions.md#client-apps-preview).
Som standard medför den här inställningen en utvärdering av alla principer med **webbläsar-** eller **mobilappar och skrivbordsklienter** antingen individuellt eller båda markerade. Den identifierar också principer som framtvingar **Exchange ActiveSync (EAS)**. Du kan begränsa den här inställningen genom att välja:

- **Webbläsare** för att utvärdera alla principer som har minst **Webbläsare markerad.** 
- **Mobilappar och skrivbordsklienter** för att utvärdera alla principer som har minst **Mobilappar och skrivbordsklienter** valda. 

### <a name="sign-in-risk"></a>Inanmälningsrisk

Den här inställningen härmar [inloggningsriskvillkoret](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Utvärdering 

Du startar en utvärdering genom att klicka på **Vad händer om**. Utvärderingsresultatet ger dig en rapport som består av: 

![What If](./media/what-if-tool/03.png)

- En indikator på om det finns klassiska principer i din miljö
- Principer som gäller för din användare
- Principer som inte gäller för din användare

Om det finns [klassiska principer](policy-migration.md#classic-policies) för de valda molnapparna visas en indikator för dig. Genom att klicka på indikatorn omdirigeras du till sidan med klassiska principer. På sidan med klassiska principer kan du migrera en klassisk princip eller bara inaktivera den. Du kan återgå till utvärderingsresultatet genom att stänga den här sidan.

I listan över principer som gäller för den valda användaren kan du också hitta en lista över [bidragskontroller](concept-conditional-access-grant.md) och [sessionskontroller](concept-conditional-access-session.md) som användaren måste uppfylla.

I listan över principer som inte gäller för din användare kan och även varför dessa principer inte gäller. För varje listad princip representerar orsaken det första villkoret som inte var uppfyllt. En möjlig orsak till en princip som inte tillämpas är en inaktiverad princip eftersom de inte utvärderas ytterligare.   

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst läser du [Kräv MFA för specifika appar med Azure Active Directory Villkorlig åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö läser du [metodtipsen för villkorlig åtkomst i Azure Active Directory](best-practices.md). 
- Om du vill migrera klassiska principer läser du [Migrera klassiska principer i Azure-portalen](policy-migration.md)  
