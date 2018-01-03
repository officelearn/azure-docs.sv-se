---
title: "Azure Active Directory-verktyget om villkorlig åtkomst - preview | Microsoft Docs"
description: "Lär dig hur du kan testa konfigurationen av din Azure Active Directory-principer för villkorlig åtkomst."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/21/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: de6b3dcd77132154e583d7333983d6745c4aa3bd
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>Azure Active Directory-verktyget om villkorlig åtkomst - förhandsgranskning

[Villkorlig åtkomst](active-directory-conditional-access-azure-portal.md) är en funktion av Azure Active Directory (AD Azure) som gör det möjligt att styra hur behöriga användare har åtkomst till dina molnappar. Hur vet du vad som händer formuläret principerna för villkorlig åtkomst i din miljö? Du kan använda för att besvara frågan i **villkorlig åtkomst om verktyget**.

Den här artikeln förklarar hur du kan använda det här verktyget för att testa din principer för villkorlig åtkomst.

## <a name="what-it-is"></a>Vad det är

Den **villkorlig åtkomst till vilka om principen verktyget** tillåter dig att förstå effekten av dina principer för villkorlig åtkomst på din miljö. I stället för att testa köra dina principer genom att utföra flera inloggningar manuellt, kan det här verktyget du utvärdera en simulerad inloggning för en användare. Simuleringen beräknar effekt detta inloggning har på dina principer och genererar en simuleringsrapport. Rapporten inte bara innehåller den tillämpa villkorliga åtkomstprinciper utan även [klassiska principer](active-directory-conditional-access-migration.md#classic-policies) om de finns.    

Nyheter avgöra om verktyg ger också ett sätt att snabbt principer som gäller för en viss användare. Du kan använda informationen, till exempel om du behöver felsöka problem.  

## <a name="how-it-works"></a>Hur det fungerar

I den **villkorlig åtkomst om verktyget**, måste du först konfigurera inställningarna för inloggningen scenario som du vill simulera. Inställningarna omfattar:

- Användaren som du vill testa 

- Molnappar som användaren försöker komma åt

- Villkor under vilka åtkomst till den konfigurerar moln appar utförs
     
Som ett nästa steg kan du initiera en simulering kör som utvärderar dina inställningar. Principer som är aktiverade är en del av en utvärdering körs.


När utvärderingen har slutförts, skapas en rapport över berörda principer.


## <a name="running-the-tool"></a>Kör verktyget

Du hittar den **vad händer om** verktyget på den  **[villkorlig åtkomst - principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)**  sida i Azure-portalen.

Starta verktyget i verktygsfältet på listan med principer, klicka på **vad händer om**.

![Tänk om](./media/active-directory-conditional-access-whatif/01.png)

Innan du kan köra en utvärdering, måste du konfigurera inställningarna.

## <a name="settings"></a>Inställningar

Det här avsnittet ger information om inställningarna för simuleringen kör.

![Tänk om](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>Användare

Du kan bara välja en användare. Detta är bara obligatoriskt fält.

### <a name="cloud-apps"></a>Molnappar

Standardvärdet för den här inställningen är **alla molnappar**. Standardinställningen utför en utvärdering av alla tillgängliga principer i din miljö. Du kan begränsa omfånget till principer som påverkar specifika molnappar.


### <a name="ip-address"></a>IP-adress

IP-adressen är en enskild IPv4-adress för att efterlikna den [plats villkoret](active-directory-conditional-access-azure-portal.md#locations). Adressen representerar Internetuppkopplad adressen till enheten som används av dina användare för att logga in. Du kan verifiera IP-adressen för en enhet med, till exempel Gå till [vad är IP-adressen](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Enhetsplattformar

Den här inställningen efterliknar den [enheten plattformar villkoret](active-directory-conditional-access-azure-portal.md#device-platforms) och representerar motsvarigheten **alla plattformar (inklusive stöds inte)**. 
### <a name="client-apps"></a>Klientappar

Den här inställningen efterliknar den [klienten appar villkoret](active-directory-conditional-access-azure-portal.md#client-apps).
Som standard, vilket medför en utvärdering av alla principer med **webbläsare** eller **mobilappar och skrivbordsklienter** antingen individuellt eller båda markerad. Dessutom upptäcks principer som framtvinga **Exchange ActiveSync (EAS)**. Du kan begränsa den här inställningen genom att välja:

- **Webbläsaren** att utvärdera alla principer med minst **webbläsare** valda. 

- **Mobilappar och skrivbordsklienter** att utvärdera alla principer med minst **mobilappar och skrivbordsklienter** valda. 


### <a name="sign-in-risk"></a>Inloggningsrisk

Den här inställningen efterliknar den [inloggning risk villkoret](active-directory-conditional-access-azure-portal.md#sign-in-risk).   


## <a name="evaluation"></a>Utvärdering 

Du kan starta en utvärdering genom att klicka på **vad händer om**. Utvärderingsresultat av den ger dig en rapport som består av: 

![Tänk om](./media/active-directory-conditional-access-whatif/03.png)

- En indikator om klassiska principer finns i din miljö
- Principer som gäller för din användare
- Principer som inte gäller för din användare


Om [klassiska principer](active-directory-conditional-access-migration.md#classic-policies) finns för de valda molnapparna, visas en indikator för dig. Genom att klicka på indikatorn, omdirigeras till sidan klassiska principer. På sidan klassiska principer kan du migrera en princip för klassiska eller bara inaktivera den. Du kan återgå till din utvärderingsresultat genom att stänga den här sidan.

I listan över principer som gäller för din valda användare, du kan också hitta en lista över [bevilja kontroller](active-directory-conditional-access-controls.md#grant-controls) och [session](active-directory-conditional-access-controls.md#session-controls) kontroller som användarna måste uppfylla.

I listan över principer som inte gäller för din användare kan och också hitta skälen varför inte gäller dessa principer. För varje listad princip representerar orsaken till det första villkoret som inte uppfylldes. En möjlig orsak till en princip som inte tillämpas är en inaktiverad princip, eftersom de ytterligare inte utvärderas.   



## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 

- Om du vill migrera klassiska principer [migrera klassiska principer i Azure-portalen](active-directory-conditional-access-migration.md)  
