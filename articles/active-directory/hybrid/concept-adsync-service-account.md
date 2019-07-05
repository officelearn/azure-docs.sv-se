---
title: 'Azure AD Connect: ADSync tjänstkonto | Microsoft Docs'
description: Det här avsnittet beskriver ADSync-tjänstkontot och innehåller metodtips om kontot.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478724"
---
# <a name="adsync-service-account"></a>ADSync-tjänstkontot
Azure AD Connect installerar en lokal tjänst som samordnar synkronisering mellan Active Directory och Azure Active Directory.  Microsoft Azure AD Sync-synkroniseringstjänsten (ADSync) körs på en server i din lokala miljö.  Autentiseringsuppgifter för tjänstens anges som standard i Express-installationer, men kan anpassas efter din organisations säkerhetskrav.  Dessa autentiseringsuppgifter används inte för att ansluta till din lokala skogar eller Azure Active Directory.

Välja ADSync är tjänstkontot ett viktigt beslut som planerar att göra innan du installerar Azure AD Connect.  Något försök att ändra autentiseringsuppgifterna när installationen leder till tjänsten misslyckas med att starta, att förlora åtkomsten till synkroniseringsdatabasen och inte kan autentiseras med dina anslutna kataloger (Azure och AD DS).  Ingen synkronisering sker tills de ursprungliga autentiseringsuppgifterna har återställts.

## <a name="the-default-adsync-service-account"></a>Standardkontot ADSync-tjänsten

När du kör på en medlemsserver körs AdSync-tjänsten i kontexten för ett virtuellt Service konto (VSA).  På grund av en produktbegränsning skapas en anpassad tjänstkontot när installerad på en domänkontrollant.  Om kontot Express inställningar inte uppfyller din organisations säkerhetskrav kan du distribuera Azure AD Connect genom att klicka på Anpassa.  Välj sedan alternativet service-konto som uppfyller organisationens krav.

>[!NOTE]
>Standard-tjänstkontot när installerade på en domänkontrollant är i formatet Domain\AAD_InstallationIdentifier.  Lösenordet för det här kontot genereras slumpmässigt och medför betydande utmaningar för återställning och lösenord rotation.  Microsoft rekommenderar att anpassa kontot under den första installationen på en domänkontrollant att använda antingen fristående eller grupphanterat tjänstkonto (sMSA / gMSA)

|Azure AD Connect-plats|Tjänstkonto som har skapats|
|-----|-----|
|Medlemsserver|NT SERVICE\ADSync|
|Domänkontrollant|Domain\AAD_74dc30c01e80 (se anmärkning)|

## <a name="custom-adsync-service-accounts"></a>Anpassade ADSync-tjänstkonton
Microsoft rekommenderar att köra ADSync-tjänsten i kontexten för ett virtuellt tjänstkonto eller ett fristående eller grupphanterat tjänstkonto.  Domänadministratören kan också välja att skapa ett tjänstkonto som har etablerats för att uppfylla dina specifika organisationens säkerhetskrav.   Om du vill anpassa tjänstkontot som används under installationen väljer du det anpassa alternativet på sidan standardinställningar.   Följande alternativ är tillgängliga:

- standardkontot – Azure AD Connect kommer att etablera tjänstkontot enligt beskrivningen ovan
- Hanterat tjänstkonto – använda en fristående eller grupp-MSA som tillhandahålls av administratören
- domänkonto – Använd en domän tjänstkontot som tillhandahålls av administratören

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnostisera ADSync-tjänsten kontoändringar
Ändra autentiseringsuppgifterna för ADSync-tjänsten när installationen leder till tjänsten misslyckas med att starta, att förlora åtkomsten till synkroniseringsdatabasen och inte kan autentiseras med dina anslutna kataloger (Azure och AD DS).  Att bevilja databasåtkomst till det nya ADSync-tjänstkontot är tillräckligt för att åtgärda det här problemet. Ingen synkronisering sker tills de ursprungliga autentiseringsuppgifterna har återställts.

ADSync-tjänsten utfärdar ett felmeddelande på visas i händelseloggen när det inte går att starta.  Innehållet i meddelandet varierar beroende på om den inbyggd databas (localdb) eller en fullständig SQL är i användning.  Här följer några exempel på posterna i händelseloggen som kan finnas.

### <a name="example-1"></a>Exempel 1

Krypteringsnycklarna AdSync-tjänsten kunde inte hittas och har återskapats.  Synkronisering utförs inte förrän problemet har åtgärdats.

Felsöka det här problemet i Microsoft Azure AD Sync blir krypteringsnycklar otillgängliga om de AdSync logga in autentiseringsuppgifterna för tjänsten har ändrats.  Om autentiseringsuppgifterna har ändrats, Använd tjänster för att ändra kontot logga in till dess ursprungligen konfigurerade värde (t.ex.) NT SERVICE\AdSync) och starta om tjänsten.  Då återställs direkt AdSync-tjänsten ska fungera korrekt.

Se följande [artikeln](https://go.microsoft.com/fwlink/?linkid=2086764) för ytterligare information.

### <a name="example-2"></a>Exempel 2

Tjänsten kunde inte starta eftersom inte gick att upprätta en anslutning till den lokala databasen (localdb).

Felsökning av problem i Microsoft Azure AD Sync tjänsten förlorar behörighet att komma åt lokala databasprovidern om AdSync logga in autentiseringsuppgifterna för tjänsten ändras.  Om autentiseringsuppgifterna har ändrats Använd tjänster för att ändra kontot logga in till dess ursprungligen konfigurerade värde (t.ex.) NT SERVICE\AdSync) och starta om tjänsten.  Då återställs direkt AdSync-tjänsten ska fungera korrekt.

Se följande [artikeln](https://go.microsoft.com/fwlink/?linkid=2086764) för ytterligare information.

Ytterligare information om följande fel returnerades av providern:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).