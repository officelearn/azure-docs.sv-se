---
title: Vad är identitetssäkerhetspoäng? - Azure Active Directory (offentlig förhandsversion) | Microsoft Docs
description: Anvisningar för att använda identitetssäkerhetspoäng för att förbättra säkerhetspositionen för din Azure AD-klientorganisation.
services: active-directory
keywords: identity secure score, Azure AD, secure access to company resources
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8617442c811dc2fe8f961038f35ebcf218a58302
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163963"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory-public-preview"></a>Vad är identitetssäkerhetspoäng i Azure Active Directory? (Offentlig förhandsversion)

Hur säker är din Azure AD-klientorganisation? Om du inte vet hur du ska besvara den här frågan kan du läsa den här artikeln för att få reda på hur identitetssäkerhetspoäng hjälper dig att övervaka och förbättra din identitetssäkerhetsstatus. 

## <a name="what-is-an-identity-secure-score"></a>Vad är identitetssäkerhetspoäng?

Identitetssäkerhetspoäng är ett tal mellan 1 och 248 som fungerar som en indikator för hur väl du följer Microsofts rekommenderade metoder för säkerhet.


![Säkerhetspoäng](./media/identity-secure-score/01.png)



Resultatet hjälper dig att:

- Objektivt mäta din identitetssäkerhetsstatus

- Planera förbättringar i identitetssäkerheten

- Granska framgången för dina förbättringar 


Du kan komma åt poängen och tillhörande information på instrumentpanelen för identitetssäkerhetspoäng. På den här instrumentpanelen hittar du:

- Dina poäng

    ![Säkerhetspoäng](./media/identity-secure-score/02.png)

- Ett jämförelsediagram

    ![Säkerhetspoäng](./media/identity-secure-score/03.png)

- Ett trenddiagram

    ![Säkerhetspoäng](./media/identity-secure-score/04.png)

- En lista med metodtips för identitetssäkerhet. 

    ![Säkerhetspoäng](./media/identity-secure-score/05.png)


Med hjälp av följande förbättringsåtgärder kan du:

- Förbättra sin säkerhetsstatus och dina poäng.
 
- Utnyttja Microsofts identitetsfunktioner. 



## <a name="how-do-i-get-my-secure-score"></a>Hur får jag mina säkerhetspoäng?

Identitetssäkerhetspoäng finns i alla utgåvor av Azure AD.

Du kommer åt dina poäng på [översiktsinstrumentpanelen för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).



## <a name="how-does-it-work"></a>Hur fungerar det?

Var 48 timme tittar Azure på din säkerhetskonfiguration och jämför dina inställningar med de rekommenderade metoderna. Utifrån resultatet av utvärderingen beräknas nya poäng för din klientorganisation. Det betyder att det kan ta upp till 48 timmar innan en konfigurationsändring du har gjort visas i dina poäng. 

Varje rekommendation mäts baserat på din Azure AD-konfiguration. Om du använder produkter från tredje part för att aktivera en rekommenderad metod kan du ange detta i inställningarna för en förbättringsåtgärd.

![Säkerhetspoäng](./media/identity-secure-score/07.png)


Dessutom har du även alternativet att ange att rekommendationer ska ignoreras om de inte gäller för din miljö. En ignorerad rekommendation bidrar inte till beräkningen av dina poäng. 
 
![Säkerhetspoäng](./media/identity-secure-score/06.png)



## <a name="how-does-it-help-me"></a>Hur hjälper det här mig?

Med säkerhetspoängen kan du:

- Objektivt mäta din identitetssäkerhetsstatus

- Planera förbättringar i identitetssäkerheten

- Granska framgången för dina förbättringar



## <a name="what-you-should-know"></a>Det här bör du känna till

### <a name="who-can-use-the-identity-secure-score"></a>Vilka kan använda identitetssäkerhetspoängen?

Identitetssäkerhetspoängen kan användas av följande roller:

- Global administratör
- Säkerhetsadministratör 
- Säkerhetsläsare 

### <a name="what-does-not-scored-mean"></a>Vad betyder [Not Scored] ([Inte poängsatt])?

Åtgärder märkta med [Not Scored] ([Inte poängsatt]) är sådana du kan utföra i organisationen men som inte poängsätts eftersom de inte är anslutna till verktyget (än!). Du kan alltså fortfarande förbättra din säkerhet men du får inga poäng för de åtgärderna just nu.

### <a name="how-often-is-my-score-updated"></a>Hur ofta uppdateras mina poäng?

Poängen beräknas en gång per dag (kring 01:00 PST). Om du gör en ändring av en åtgärd som mäts uppdateras poängen automatiskt nästa dag. Det tar upp till 48 timmar innan en ändring återspeglas i dina poäng.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mina poäng har ändrats. Hur får jag reda på varför?

På sidan med poänganalyser i [portalen för säkerhetspoäng](https://securescore.microsoft.com/#!/score) kan du klicka på en datapunkt för en viss dag, sedan rulla ned för att se slutförda och ofullständiga åtgärder för den dagen för att få reda på vad som har ändrats.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Mäter säkerhetspoängen min risk för att drabbas av intrång?

Det korta svaret är nej. Säkerhetspoängen anger inte ett absolut mått på hur troligt det är att du drabbas av intrång. De anger i vilken utsträckning du har implementerat funktioner som kan motverka risken för intrång. Ingen tjänst kan garantera att du inte drabbas av intrång och säkerhetspoängen ska inte tolkas som en garanti på något sätt.

### <a name="how-should-i-interpret-my-score"></a>Hur ska jag tolka mina poäng?

Du får poäng när du konfigurerar rekommenderade säkerhetsfunktioner eller när du utför säkerhetsrelaterade uppgifter (som att läsa repporter). Vissa åtgärder poängsätts när de är delvis slutförda, som att aktivera multifaktorautentisering (MFA) för dina användare. Dina säkerhetspoäng står i direkt relation till vilka Microsoft-säkerhetstjänster du använder. Kom ihåg att säkerhet alltid ska balanseras med användbarhet. Alla säkerhetskontroller har en komponent med användarpåverkan. Kontroller med låg användarpåverkan bör ha lite eller ingen effekt på användarnas dagliga åtgärder.

Om du vill se din poänghistorik går du till poänganalyssidan i [portalen för säkerhetspoäng](https://securescore.microsoft.com/#!/score). Välj ett specifikt datum för att se vilka kontroller som hade aktiverats för den dagen och vilka poäng du har fått för varje kontroll.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hur är identitetssäkerhetspoäng relaterade till säkerhetspoäng i Office 365 Secure Score? 

[Office 365 Secure Score](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score) håller på att migreras till en sammanställning med fem olika poäng:

- Identitet

- Data

- Enheter

- Infrastruktur

- Program

Identitetssäkerhetspoängen representerar identitetsdelen av Office 365 Secure Score. Det betyder att dina rekommendationer för identitetssäkerhetspoängen och identitetspoängen i Office 365 är desamma. 


## <a name="next-steps"></a>Nästa steg

Om du vill titta på en video om Office 365 Secure Score klickar du [här](https://www.youtube.com/watch?v=jzfpDJ9Kg-A).
 
