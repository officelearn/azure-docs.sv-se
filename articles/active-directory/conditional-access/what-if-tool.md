---
title: Vad är vad om-verktyget i Azure Active Directory villkorlig åtkomst?
description: Lär dig hur du kan förstå effekten av principer för villkorlig åtkomst på din miljö.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e60bb2f8d1f4b601618455eb8bbe9111132e5241
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081882"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>Vad är vad om-verktyget i Azure Active Directory villkorlig åtkomst?

[Villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) är en funktion i Azure Active Directory (Azure AD) som ger dig möjlighet att styra hur behöriga användare har åtkomst till dina appar i molnet. Hur vet du vad som händer formuläret principerna för villkorlig åtkomst i din miljö? Du kan använda för att besvara den här frågan i **villkorlig åtkomst är konsekvensverktyg**.

Den här artikeln förklarar hur du kan använda det här verktyget för att testa principer för villkorlig åtkomst.

## <a name="what-it-is"></a>Vad det är

Den **what-if för villkorlig åtkomst princip verktyget** tillåter dig att förstå effekten av principer för villkorlig åtkomst på din miljö. Med verktyget kan du utvärdera en simulerad användarinloggning i stället för att testa principerna genom att köra många manuella inloggningar. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport. Rapporten inte bara innehåller det tillämpade villkorliga åtkomstprinciper utan även [klassiska principer](policy-migration.md#classic-policies) om de finns.    

Vad avgöra om verktygen innehåller också ett sätt att snabbt de principer som gäller för en viss användare. Du kan använda informationen, till exempel om du behöver felsöka ett problem.  

## <a name="how-it-works"></a>Hur det fungerar

I den **villkorlig åtkomst är konsekvensverktyg**, måste du först konfigurera inställningarna för inloggning scenario som du vill simulera. Inställningarna omfattar:

- Den användare du vill testa 

- Moln-appar som användaren försöker få åtkomst till

- Villkor under vilka åtkomst till den konfigurerar molnet appar utförs
     
I nästa steg, kan du initiera en simulering kör som utvärderar dina inställningar. Principer som är aktiverade är en del av en utvärdering körs.


När utvärderingen har slutförts, genererar verktyget en rapport över de berörda principerna.



## <a name="running-the-tool"></a>Kör verktyget

Du hittar den **vad händer om** verktyget på den **[villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** i Azure-portalen.

Starta verktyget, i verktygsfältet på listan med principer, klicka på **vad händer om**.

![Tänk om](./media/what-if-tool/01.png)

Innan du kan köra en utvärdering, måste du konfigurera inställningarna.

## <a name="settings"></a>Inställningar

Det här avsnittet innehåller information om inställningarna för simuleringen kör.

![Tänk om](./media/what-if-tool/02.png)


### <a name="user"></a>Användare

Du kan bara välja en användare. Det här är det enda obligatoriska fältet.

### <a name="cloud-apps"></a>Molnappar

Standardvärdet för den här inställningen är **alla molnappar**. Standardinställningen utför en utvärdering av alla tillgängliga principer i din miljö. Du kan begränsa omfattningen för principer som påverkar specifika molnappar.


### <a name="ip-address"></a>IP-adress

IP-adressen är en enskild IPv4-adress för att efterlikna de [platsvillkoret](location-condition.md). Adressen representerar adressen till enheten som används av användaren för att logga in som riktas mot Internet. Du kan kontrollera IP-adressen för en enhet av, till exempel Gå till [vad är Min IP-adress](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Enhetsplattformar

Den här inställningen imiterar den [enheten plattformar villkor](conditions.md#device-platforms) och representerar motsvarigheten **alla plattformar (inklusive stöds inte)**. 
### <a name="client-apps"></a>Klientappar

Den här inställningen imiterar den [klient apps villkor](conditions.md#client-apps).
Som standard den här inställningen gör en utvärdering av alla principer som har **webbläsare** eller **mobilappar och skrivbordsklienter** antingen individuellt eller båda har valt. Dessutom upptäcks principer som framtvinga **Exchange ActiveSync (EAS)**. Du kan begränsa den här inställningen genom att välja:

- **Webbläsaren** att utvärdera alla principer som har minst **webbläsare** valda. 

- **Mobilappar och skrivbordsklienter** att utvärdera alla principer som har minst **mobilappar och skrivbordsklienter** valda. 


### <a name="sign-in-risk"></a>Inloggningsrisk

Den här inställningen imiterar den [inloggningsrisk villkor](conditions.md#sign-in-risk).   


## <a name="evaluation"></a>Utvärdering 

Du startar en utvärdering genom att klicka på **vad händer om**. Utvärderingsresultat av den ger dig en rapport som består av: 

![Tänk om](./media/what-if-tool/03.png)

- Indikerar om klassiska principer finns i din miljö
- Principer som gäller för dina användare
- Principer som inte gäller för användaren


Om [klassiska principer](policy-migration.md#classic-policies) finns för de valda molnapparna, visas en indikator för dig. Genom att klicka på indikatorn, omdirigeras du till sidan klassiska principer. På sidan klassiska principer kan du migrera den klassiska principen eller bara inaktivera den. Du kan gå tillbaka till din utvärderingsresultat genom att stänga den här sidan.

På listan över principer som gäller för din valda användare, kan du också hitta en lista över [bevilja kontroller](controls.md#grant-controls) och [session](controls.md#session-controls) kontroller som användaren måste uppfylla.

I listan med principer som inte gäller för dina användare kan och också hitta orsaker varför dessa principer inte gäller. För varje listad princip representerar orsaken till det första villkoret som inte uppfylldes. En möjlig orsak till en princip som inte tillämpas är en inaktiverad princip, eftersom de ytterligare inte utvärderas.   



## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md).

- Om du är redo att konfigurera principer för villkorsstyrd åtkomst för din miljö kan du läsa sidan om [metodtips för villkorsstyrd åtkomst i Azure Active Directory](best-practices.md). 

- Om du vill migrera klassiska principer finns i [migrera klassiska principer i Azure portal](policy-migration.md)  
