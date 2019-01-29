---
title: Azure AD Connect – hantera AD FS-förtroendet med Azure AD med Azure AD Connect | Microsoft Docs
description: För ett Azure AD-förtroende som hantering av Azure AD connect.
keywords: AD FS, ADFS, AD FS-hantering, AAD Connect, Anslut, Azure AD, förtroende, AAD, anspråk, anspråk, anspråksregler, utfärdande, transform, regler, säkerhetskopiering, återställning
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
ms.topic: article
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.openlocfilehash: 660ded4cd5cfd4da3c04e1044c890612e92ea9c4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55161196"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Hantera AD FS-förtroende med Azure AD med Azure AD Connect

## <a name="overview"></a>Översikt

Azure AD Connect kan hantera federation mellan lokala Active Directory Federation Service (AD FS) och Azure AD. Den här artikeln innehåller en översikt över:

* Olika inställningar som konfigurerats på förtroendet av Azure AD Connect
* Utfärdandetransformering (anspråksregler) som anges av Azure AD Connect
* Säkerhetskopiera och återställa din anspråk regler mellan uppgraderingar och konfiguration uppdateringar. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Inställningar som styrs av Azure AD Connect

Azure AD Connect hanterar **endast** inställningar som är relaterade till Azure AD-förtroende. Azure AD Connect ändras inte alla inställningar på andra förtroenden för förlitande part i AD FS. I följande tabell anger inställningar som styrs av Azure AD Connect.

| Inställning | Beskrivning |
| :--- | :--- |
| Tokensigneringscertifikat | Azure AD Connect kan användas för att återställa och återskapa förtroendet med Azure AD. Azure AD Connect har en enstaka omedelbar förnyelse av certifikat för tokensignering för AD FS och uppdaterar Azure AD-federationsinställningar för domän.|
| Token Signeringsalgoritm | Microsoft rekommenderar att du använder SHA-256 som algoritmen för tokensignering. Azure AD Connect kan känna av om token Signeringsalgoritm anges till ett värde mindre säkert än SHA-256. Det kommer att uppdatera inställningen till SHA-256 nästa möjliga konfigurationen igen. Andra förlitande part måste uppdateras för att använda det nya certifikatet för tokensignering. |
| Azure AD-förtroende identifierare | Azure AD Connect anger värdet för rätt identifierare för Azure AD-förtroende. AD FS identifierar Azure AD-förtroende med hjälp av ID-värde. |
| Azure AD-slutpunkter | Azure AD Connect säkerställer att de slutpunkter som konfigurerats för Azure AD-förtroende är alltid enligt de senaste rekommenderade värdena för flexibilitet och prestanda. |
| Regler för utfärdandetransformering | Det finns mängder av anspråksregler som är nödvändiga för optimala prestanda med funktioner i Azure AD i en federerad miljö. Azure AD Connect säkerställer att Azure AD-förtroende är alltid konfigurerad med rätt uppsättning rekommenderade anspråksregler. |
| Alternate-id | Om synkronisering är konfigurerad för att använda alternativ-id, konfigurerar AD FS för att utföra autentisering med alternativ-id i Azure AD Connect. |
| Automatisk metadata update | Förtroende med Azure AD har konfigurerats för automatisk metadata update. AD FS övervakar kontinuerligt metadata för Azure AD-förtroende och håller den uppdaterade om ändras på Azure AD-sida. |
| Integrerad Windows-autentisering (IWA) | Under Hybrid Azure AD join-åtgärd, är IWA aktiverat för registrering av enheten att underlätta Hybrid Azure AD-anslutning för äldre enheter |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Köra flöden och federationsinställningar som konfigurerats av Azure AD Connect

Azure AD connect uppdaterar inte alla inställningar för Azure AD-förtroende under configuration flöden. Inställningarna ändras beror på vilken uppgift eller körning av flödet körs. I följande tabell visas de inställningar som påverkas i olika körning flöden.

| Körning av flöde | Inställningar som påverkas |
| :--- | :--- |
| Först klara installation (snabb) | Ingen |
| Först klara installation (ny AD FS-servergrupp) | En ny AD FS-servergrupp skapas och ett förtroende med Azure AD har skapats från grunden. |
| Först klara installation (befintliga AD FS-servergrupp, befintlig Azure AD-förtroende) | Azure AD-förtroende identifierare, utfärdande av transformeringsregler, Azure AD-slutpunkter, alternativ-id (vid behov), automatisk metadata update |
| Återställ Azure AD-förtroende | Token signeringscertifikatet, algoritmen, Azure AD-förtroende identifierare, utfärdande-transformering för tokensignering regler, Azure AD-slutpunkter, alternativ-id (vid behov), automatisk metadata update |
| Lägg till federationsserver | Ingen |
| Lägg till WAP-server | Ingen |
| Enhetsalternativ | Utfärdande av transformeringsregler, IWA för registrering av enheten |
| Lägg till federerad domän | Om domänen har lagts till för första gången, det vill säga installationen ändras från enkel domän federation till flera domäner federationen – Azure AD Connect kommer att återskapa förtroendet från början. Om förtroendet med Azure AD har redan konfigurerats för flera domäner, ändras endast regler för Utfärdandetransformering |
| Uppdatera SSL | Ingen |

Under alla åtgärder, där inställningar är ändrade, Azure AD Connect-gör en säkerhetskopia av de aktuella förtroendeinställningarna för på **%ProgramData%\AADConnect\ADFS**

![Azure AD Connect sidan som visar meddelandet om säkerhetskopiering av befintliga Azure AD-förtroende](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Före version 1.1.873.0 säkerhetskopieringen bestod av endast regler för utfärdandetransformering och de säkerhetskopierades i guiden spårningsloggen.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Utfärdande av transformeringsregler anges av Azure AD Connect

Azure AD Connect säkerställer att Azure AD-förtroende är alltid konfigurerad med rätt uppsättning rekommenderade anspråksregler. Microsoft rekommenderar att använda Azure AD connect för att hantera din Azure AD-förtroende. Det här avsnittet innehåller regeluppsättning för utfärdande transformeringen och deras beskrivning.

| Regelnamn | Beskrivning |
| --- | --- |
| Problemet UPN | Den här regeln frågar värdet för userprincipalname från attributet som konfigurerats i synkroniseringsinställningar för userprincipalname.|
| Fråga objectguid och msdsconsistencyguid för anpassade ImmutableId-anspråket | Den här regeln lägger till ett tillfälligt värde i pipeline för objectguid och msdsconsistencyguid värdet om det finns |
| Kontrollera om finns för msdsconsistencyguid | Baserat på om värdet för msdsconsistencyguid finns eller inte, ange vi en tillfällig flagga för att dirigera vad du ska använda som ImmutableId |
| Utfärda msdsconsistencyguid som oföränderligt ID om den finns | Utfärda msdsconsistencyguid som ImmutableId om värdet finns |
| Utfärda objectGuidRule om msdsConsistencyGuid regeln inte finns | Om värdet för msdsconsistencyguid inte finns, kommer värdet för objectguid att utfärdas som ImmutableId |
| Utfärda nameidentifier | Den här regeln utfärdar värde för nameidentifier-anspråket.|
| Utfärda accounttype för domänanslutna datorer | Om den entitet som autentiseras är en domänansluten enhet, utfärdar den här regeln kontotypen som DJ vilket tyder på en domänansluten enhet |
| Utfärda AccountType med värdet för användaren när det inte ett datorkonto | Om en användare är den entitet som autentiseras utfärdar med den här regeln kontotypen som användare |
| Utfärda issuerid när det inte ett datorkonto | Den här regeln utfärdar issuerId-värde när den autentiserande entiteten inte är en enhet. Värdet som skapas via ett regex som konfigureras av Azure AD Connect. Regex skapas när ta hänsyn till alla domäner federerad med Azure AD Connect. |
| Utfärda issuerid för DJ datorn auth | Den här regeln utfärdar issuerId-värde när den autentiserande entiteten är en enhet |
| Utfärda onpremobjectguid för domänanslutna datorer | Om den entitet som autentiseras är en domänansluten enhet, utfärdar den här regeln lokala objectguid för enheten |
| Passera primärt SID | Den här regeln utfärdar primärt SID för den autentiserande entitet |
| Passera anspråk - insideCorporateNetwork | Den här regeln problem med ett anspråk som hjälper till att Azure AD vet om autentiseringen kommer från i företagets nätverk eller externt |
| Passera anspråk – Psso |   |
| Utfärda anspråk för lösenord upphör att gälla | Den här regeln problem med tre anspråk för förfallotid för lösenord, antal dagar för att lösenordet ska upphöra att gälla för det entitet som autentiseras och URL: en var du vill dirigera för att ändra lösenordet.|
| Passera anspråk – authnmethodsreferences | Värdet i anspråk som utfärdats under den här regeln anger vilken typ av autentisering har utförts för entiteten |
| Passera anspråk - multifactorauthenticationinstant | Värdet för det här anspråket anger tiden i UTC, när användaren senast gjorde autentisering med flera faktorer. |
| Passera anspråk - AlternateLoginID | Den här regeln utfärdar AlternateLoginID-anspråk om autentiseringen har utförts med hjälp av alternativa inloggnings-ID. |

> [!NOTE]
> Anspråksregler för problemet UPN och ImmutableId skiljer sig om du använder icke-standard under konfigurationen av Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Återställa regler för utfärdandetransformering

Azure AD Connect-version 1.1.873.0 eller senare gör en säkerhetskopia av Azure AD litar inställningar när en uppdatering görs i inställningarna för Azure AD-förtroende. Inställningar för Azure AD-förtroende som säkerhetskopieras på **%ProgramData%\AADConnect\ADFS**. Filnamnet är i formatet AadTrust -&lt;datum&gt;-&lt;tid&gt;.txt, till exempel - AadTrust-20180710-150216.txt

![En sanpshot för till exempel som säkerhetskopiering av Azure AD-förtroende](./media/how-to-connect-azure-ad-trust/backup.png)

Du kan återställa de utfärdande av transformeringsregler med hjälp av de föreslagna åtgärderna nedan

1. Öppna AD FS-hantering Användargränssnittet i Serverhanteraren
2. Öppna egenskaperna för Azure AD-förtroende genom att gå **AD FS &gt; förlitande Partsförtroenden &gt; Microsoft Office 365-Identitetsplattform &gt; redigera anspråk utgivningsprinciper**
3. Klicka på **Lägg till regel**
4. Välj Skicka anspråk med hjälp av en anpassad regel i anspråksregelmall, och klicka på **nästa**
5. Kopiera namnet på regel för anspråk från säkerhetskopian och klistra in den i fältet **Regelnamn för anspråk**
6. Kopiera anspråksregeln från säkerhetskopian i textfältet för **anpassad regel** och klicka på **Slutför**

> [!NOTE]
> Se till att dina ytterligare regler inte står i konflikt med reglerna som konfigurerats av Azure AD Connect.

## <a name="next-steps"></a>Nästa steg
* [Hantera och anpassa Active Directory Federation Services med Azure AD Connect](how-to-connect-fed-management.md)
