---
title: Azure AD Connect – hantera AD FS-förtroende med Azure AD med Azure AD Connect | Microsoft-dokument
description: Operativ information om Azure AD-förtroendehantering av Azure AD connect.
keywords: AD FS, ADFS, AD FS-hantering, AAD Connect, Connect, Azure AD, trust, AAD, anspråk, anspråk, anspråksregler, utfärdande, transformering, regler, säkerhetskopiering, återställning
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3e521fb7668305ce511aaddd63ed2cce8dfed0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331723"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Hantera AD FS-förtroende med Azure AD med hjälp av Azure AD Connect

## <a name="overview"></a>Översikt

Azure AD Connect kan hantera federation mellan lokala Active Directory Federation Service (AD FS) och Azure AD. Den här artikeln innehåller en översikt över:

* De olika inställningarna som konfigurerats för förtroendet från Azure AD Connect
* Reglerna för utfärdandetransformering (anspråksregler) som fastställts av Azure AD Connect
* Så här säkerhetskopierar och återställer du anspråksreglerna mellan uppgraderingar och konfigurationsuppdateringar. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Inställningar som styrs av Azure AD Connect

Azure AD Connect hanterar **endast** inställningar relaterade till Azure AD-förtroende. Azure AD Connect ändrar inga inställningar för andra förlitande part förtroenden i AD FS. Följande tabell anger inställningar som styrs av Azure AD Connect.

| Inställning | Beskrivning |
| :--- | :--- |
| Certifikat för signering av token | Azure AD Connect kan användas för att återställa och återskapa förtroendet med Azure AD. Azure AD Connect gör en engångsföreteelse av tokensigneringscertifikat för AD FS och uppdaterar azure AD-domänfederationsinställningarna.|
| Algoritm för tokensignering | Microsoft rekommenderar att du använder SHA-256 som tokensigneringsalgoritm. Azure AD Connect kan identifiera om tokensigneringsalgoritmen är inställd på ett värde som är mindre säkert än SHA-256. Den uppdateras till SHA-256 i nästa möjliga konfigurationsåtgärd. Andra förlitande part förtroende måste uppdateras för att använda den nya token signering certifikat. |
| Azure AD-förtroendeidentifierare | Azure AD Connect anger rätt identifierare för Azure AD-förtroendet. AD FS identifierar azure AD-förtroendet unikt med hjälp av identifierarens värde. |
| Slutpunkter för Azure AD | Azure AD Connect ser till att slutpunkterna som konfigurerats för Azure AD-förtroendet alltid är enligt de senaste rekommenderade värdena för återhämtning och prestanda. |
| Regler för omvandling av utfärdande | Det finns antal anspråksregler som behövs för optimal prestanda för funktioner i Azure AD i en federerad inställning. Azure AD Connect ser till att Azure AD-förtroendet alltid är konfigurerat med rätt uppsättning rekommenderade anspråksregler. |
| Alternativ-id | Om synkronisering är konfigurerad för att använda alternativ-id konfigurerar Azure AD Connect AD FS för att utföra autentisering med alternativa id. |
| Uppdatering av automatiska metadata | Förtroende med Azure AD är konfigurerat för automatisk metadatauppdatering. AD FS kontrollerar regelbundet metadata för Azure AD-förtroende och håller det uppdaterat om det ändras på Azure AD-sidan. |
| Integrerad Windows-autentisering (IWA) | Under Hybrid Azure AD-anslutningsåtgärd är IWA aktiverat för enhetsregistrering för att underlätta Hybrid Azure AD-anslutning för enheter på nednivå för enheter på nednivå |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Körningsflöden och federationsinställningar som konfigurerats av Azure AD Connect

Azure AD connect uppdaterar inte alla inställningar för Azure AD-förtroende under konfigurationsflöden. Vilka inställningar som ändras beror på vilken aktivitet eller vilket körningsflöde som körs. I följande tabell visas de inställningar som påverkas i olika körningsflöden.

| Körningsflöde | Inställningar påverkade |
| :--- | :--- |
| Installation av första pass (express) | Inget |
| Installation av första pass (ny AD FS-gård) | En ny AD FS-servergrupp skapas och ett förtroende med Azure AD skapas från grunden. |
| Installation av första pass (befintlig AD FS-servergrupp, befintlig Azure AD-förtroende) | Azure AD-förtroendeidentifierare, regler för utfärdandetransformering, Azure AD-slutpunkter, Alternativ-ID (om nödvändigt), automatisk metadatauppdatering |
| Återställa Azure AD-förtroende | Tokensigneringscertifikat, tokensigneringsalgoritm, Azure AD-förtroendeidentifierare, regler för utfärdandetransformering, Azure AD-slutpunkter, Alternativ-ID (om nödvändigt), automatisk metadatauppdatering |
| Lägg till federationsserver | Inget |
| Lägga till WAP-server | Inget |
| Enhetsalternativ | Regler för utfärdandetransformering, IWA för enhetsregistrering |
| Lägga till federerad domän | Om domänen läggs till för första gången, det vill än, ändras installationen från endomänfederation till federation med flera domäner – Azure AD Connect återskapar förtroendet från grunden. Om förtroendet med Azure AD redan är konfigurerat för flera domäner ändras endast regler för utfärdandetransformering |
| Uppdatera TLS | Inget |

Under alla åtgärder, där alla inställningar ändras, gör Azure AD Connect en säkerhetskopia av de aktuella förtroendeinställningarna på **%ProgramData%\AADConnect\ADFS**

![Azure AD Connect-sida som visar meddelande om befintlig azure AD-förtroendesäkerhetskopiering](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Före version 1.1.873.0 bestod säkerhetskopian av endast regler för utfärdandetransformering och de säkerhetskopierades i guidens spårningsloggfil.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Regler för utfärdandetransformering som fastställts av Azure AD Connect

Azure AD Connect ser till att Azure AD-förtroendet alltid är konfigurerat med rätt uppsättning rekommenderade anspråksregler. Microsoft rekommenderar att du använder Azure AD connect för att hantera ditt Azure AD-förtroende. I det här avsnittet visas de regler för utfärdandetransformering som angetts och deras beskrivning.

| Regelnamn | Beskrivning |
| --- | --- |
| Utfärda UPN | Den här regeln frågar värdet för userprincipalname från och med attributet som konfigurerats i synkroniseringsinställningar för userprincipalname.|
| Fråga objectguid och msdsconsistencyguid för anpassade ImmutableId-anspråk | Den här regeln lägger till ett tillfälligt värde i pipelinen för objectguid- och msdsconsistencyguid-värde om det finns |
| Kontrollera om det finns msdsconsistencyguid | Baserat på om värdet för msdsconsistencyguid existerar eller inte, anger vi en tillfällig flagga för att styra vad som ska användas som ImmutableId |
| Utfärda msdsconsistencyguid som Immutable ID om det finns | Utfärda msdsconsistencyguid som ImmutableId om värdet finns |
| Utfärda objectGuidRule om msdsConsistencyGuid-regeln inte finns | Om värdet för msdsconsistencyguid inte finns, kommer värdet på objectguid att utfärdas som ImmutableId |
| Problemnamnidentifierare | Den här regeln utfärdar värde för namnidentifieraranspråket.|
| Utfärda kontotyp för domänanslutna datorer | Om entiteten som autentiseras är en domänansluten enhet utfärdar den här regeln kontotypen som DJ som anger en domänansluten enhet |
| Utfärda AccountType med värdet USER när det inte är ett datorkonto | Om den enhet som autentiseras är en användare utfärdar den här regeln kontotypen som |
| Problemproblem när det inte är ett datorkonto | Den här regeln utfärdar numretrId-värdet när den autentisera entiteten inte är en enhet. Värdet skapas via en regex, som konfigureras av Azure AD Connect. Regex skapas efter att ha tagit hänsyn till alla domäner som federerats med Azure AD Connect. |
| Issue issuerid för DJ-dator auth | Den här regeln utfärdar numretrId-värdet när den autentisera entiteten är en enhet |
| Problem onpremobjectguid för domänanslutna datorer | Om entiteten som autentiseras är en domänansluten enhet utfärdar den här regeln den lokala objektguidningen för enheten |
| Passera genom primära SID | Den här regeln utfärdar den primära SID-enheten för den autentiserade entiteten |
| Passera genom anspråk - inutiKororateNetwork | Den här regeln utfärdar ett anspråk som hjälper Azure AD att veta om autentiseringen kommer inifrån företagsnätverket eller externt |
| Passera genom anspråk - Psso |   |
| Anspråk på förfallodatum för lösenord | Den här regeln utfärdar tre anspråk på förfallotid för lösenord, antal dagar för att lösenordet ska upphöra att gälla för den enhet som autentiseras och URL:en var du ska cirkulera för att ändra lösenordet.|
| Passera genom anspråk – authnmethodsreferences | Värdet i det anspråk som utfärdats enligt den här regeln anger vilken typ av autentisering som utfördes för entiteten |
| Passera genom anspråk - multifactorauthenticationinstant | Värdet för det här anspråket anger den tid i UTC när användaren senast utförde multifaktorautentisering. |
| Skicka igenom anspråk - AlternateLoginID | Den här regeln utfärdar AlternateLoginID-anspråket om autentiseringen utfördes med hjälp av alternativt inloggnings-ID. |

> [!NOTE]
> Anspråksreglerna för Issue UPN och ImmutableId skiljer sig åt om du använder icke-standardval under Azure AD Connect-konfiguration

## <a name="restore-issuance-transform-rules"></a>Återställa regler för omvandling av utfärdande

Azure AD Connect version 1.1.873.0 eller senare gör en säkerhetskopia av Azure AD-förtroendeinställningarna när en uppdatering görs till Azure AD-förtroendeinställningarna. Azure AD-förtroendeinställningarna säkerhetskopieras på **%ProgramData%\AADConnect\ADFS**. Filnamnet är i följande format&lt;AadTrust- datumtid&gt;-&lt;&gt;.txt, till exempel - AadTrust-20180710-150216.txt

![En skärmbild av exempel på säkerhetskopiering av Azure AD-förtroende](./media/how-to-connect-azure-ad-trust/backup.png)

Du kan återställa reglerna för utfärdandetransformering med hjälp av de föreslagna stegen nedan

1. Öppna AD FS-hanteringsgränssnittet i Serverhanteraren
2. Öppna Azure AD-förtroendeegenskaperna genom att gå ad **FS &gt; Relying Party Trusts &gt; Microsoft Office 365 Identity Platform &gt; Edit Claims Issuance Policy**
3. Klicka på **Lägg till regel**
4. I anspråksregelmallen väljer du Skicka anspråk med hjälp av en anpassad regel och klickar på **Nästa**
5. Kopiera namnet på anspråksregeln från säkerhetskopian och klistra in den i fältet **Anspråksregelnamn**
6. Kopiera anspråksregeln från säkerhetskopian till textfältet för **anpassad regel** och klicka på **Slutför**

> [!NOTE]
> Se till att dina ytterligare regler inte strider mot de regler som konfigurerats av Azure AD Connect.

## <a name="next-steps"></a>Nästa steg
* [Hantera och anpassa Active Directory Federation Services med Azure AD Connect](how-to-connect-fed-management.md)
