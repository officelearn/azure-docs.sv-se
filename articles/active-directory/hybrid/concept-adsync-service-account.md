---
title: 'Azure AD Connect: ADSync-tjänstkonto | Microsoft-dokument'
description: I det här avsnittet beskrivs ADSync-tjänstkontot och de bästa metoderna för kontot.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478724"
---
# <a name="adsync-service-account"></a>ADSync-tjänstkonto
Azure AD Connect installerar en lokal tjänst som dirigerar synkronisering mellan Active Directory och Azure Active Directory.  AdSync (Microsoft Azure AD Sync Sync-synkroniseringstjänsten) körs på en server i din lokala miljö.  Autentiseringsuppgifterna för tjänsten anges som standard i Express-installationerna men kan anpassas för att uppfylla organisationens säkerhetskrav.  Dessa autentiseringsuppgifter används inte för att ansluta till lokala skogar eller Azure Active Directory.

Att välja ADSync-tjänstkontot är ett viktigt planeringsbeslut att fatta innan du installerar Azure AD Connect.  Alla försök att ändra autentiseringsuppgifterna efter installationen kommer att resultera i att tjänsten inte startar, förlorar åtkomst till synkroniseringsdatabasen och misslyckas med att autentisera med dina anslutna kataloger (Azure och AD DS).  Ingen synkronisering kommer att ske förrän de ursprungliga autentiseringsuppgifterna har återställts.

## <a name="the-default-adsync-service-account"></a>AdSync-tjänstkontot för standard

När den körs på en medlemsserver körs AdSync-tjänsten i samband med ett VSA-konto (Virtual Service Account).  På grund av en produktbegränsning skapas ett anpassat tjänstkonto när det installeras på en domänkontrollant.  Om tjänstkontot för Express-inställningar inte uppfyller dina organisatoriska säkerhetskrav distribuerar du Azure AD Connect genom att välja alternativet Anpassa.  Välj sedan det servicekontoalternativ som uppfyller organisationens krav.

>[!NOTE]
>Standardtjänstkontot när det installeras på en domänkontrollant är av formuläret Domän\AAD_InstallationIdentifier.  Lösenordet för det här kontot genereras slumpmässigt och innebär stora utmaningar för återställning och lösenordsrotation.  Microsoft rekommenderar att du anpassar tjänstkontot under den första installationen på en domänkontrollant för att använda antingen ett fristående eller gruppiserat hanterat tjänstkonto (sMSA / gMSA)

|Azure AD Connect-plats|Tjänstkonto har skapats|
|-----|-----|
|Medlemsserver|NT-TJÄNST\ADSync|
|Domänkontrollant|Domän\AAD_74dc30c01e80 (se anmärkning)|

## <a name="custom-adsync-service-accounts"></a>Anpassade ADSync-tjänstkonton
Microsoft rekommenderar att adsync-tjänsten körs i samband med antingen ett virtuellt tjänstkonto eller ett fristående eller gruppkonto för hanterade tjänster.  Domänadministratören kan också välja att skapa ett tjänstkonto som har etablerats för att uppfylla dina specifika organisatoriska säkerhetskrav.   Om du vill anpassa det tjänstkonto som används under installationen väljer du alternativet Anpassa på sidan Expressinställningar nedan.   Följande alternativ är tillgängliga:

- standardkonto – Azure AD Connect etablerar tjänstkontot enligt beskrivningen ovan
- hanterat tjänstkonto – använd en fristående eller grupp-MSA som har etablerats av administratören
- domänkonto – använd ett domäntjänstkonto som har etablerats av administratören

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnostisera ändringar i ADSync-tjänstkonto
Om du ändrar autentiseringsuppgifterna för ADSync-tjänsten efter installationen kommer tjänsten att inte startas, åtkomsten till synkroniseringsdatabasen går förlorad och dina anslutna kataloger (Azure och AD DS) inte autentiseras.  Det räcker inte att bevilja databasåtkomst till det nya ADSync-tjänstkontot för att återställa från det här problemet. Ingen synkronisering kommer att ske förrän de ursprungliga autentiseringsuppgifterna har återställts.

ADSync-tjänsten utfärdar ett felmeddelande på felnivå till händelseloggen när den inte kan starta.  Innehållet i meddelandet varierar beroende på om den inbyggda databasen (localdb) eller full SQL används.  Följande är exempel på de händelseloggposter som kan finnas.

### <a name="example-1"></a>Exempel 1

Det gick inte att hitta krypteringsnycklarna för AdSync-tjänsten och har återskapats.  Synkroniseringen sker inte förrän problemet har korrigerats.

Felsöka det här problemet Microsoft Azure AD Sync-krypteringsnycklarna blir inte tillgängliga om autentiseringsuppgifterna för AdSync-tjänsten Logga in ändras.  Om autentiseringsuppgifterna har ändrats använder du programmet Tjänster för att ändra tillbaka inloggningskontot till det ursprungligen konfigurerade värdet (t.ex. NT SERVICE\AdSync) och starta om tjänsten.  Detta återställer omedelbart korrekt drift av AdSync-tjänsten.

Se följande [artikel](https://go.microsoft.com/fwlink/?linkid=2086764) för ytterligare information.

### <a name="example-2"></a>Exempel 2

Tjänsten kunde inte starta eftersom en anslutning till den lokala databasen (localdb) inte kunde upprättas.

Felsöka det här problemet Microsoft Azure AD Sync-tjänsten förlorar behörigheten att komma åt den lokala databasleverantören om autentiseringsuppgifterna för AdSync-tjänsten Logga in ändras.  Om autentiseringsuppgifterna har ändrats använder du programmet Tjänster för att ändra tillbaka inloggningskontot till det ursprungligen konfigurerade värdet (t.ex. NT SERVICE\AdSync) och starta om tjänsten.  Detta återställer omedelbart korrekt drift av AdSync-tjänsten.

Se följande [artikel](https://go.microsoft.com/fwlink/?linkid=2086764) för ytterligare information.

Ytterligare information Följande felinformation returnerades av leverantören:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).