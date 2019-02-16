---
title: Azure Stack-logg- och datahantering | Microsoft Docs
description: Läs mer om hur Azure Stack samlar in information.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 7beb9dc31e8987d69c2cf9cf64be36caf0e0e308
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56318929"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack logg- och hanterar data 
*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*  

I utsträckning som Microsoft är en processor- eller subprocessor av personuppgifter i samband med Azure Stack, Microsoft gör för alla kunder, gällande den 25 maj 2018 åtaganden i a i ”-bearbetning av personuppgifter. GDPR ”tillhandahållande av avsnittet” Dataskyddsvillkor ”i den [villkoren för Online Services](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0) och (b) i den Europeiska unionen Allmänt förordning Dataskyddsvillkor i bilaga 4 i den [villkoren för Online Services](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0). 

Eftersom Azure Stack finns i kunddatacenter, Microsoft är registeransvarig enbart för de data som delas med Microsoft via [diagnostik](azure-stack-diagnostics.md), [telemetri](azure-stack-telemetry.md), och [fakturering](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Dataåtkomstkontroller 
Microsoft-anställda som har tilldelats undersöka specifika supportärende, beviljas skrivskyddad åtkomst till krypterade data. Microsoft-anställda har också tillgång till verktyg som kan användas för att ta bort data om det behövs. All åtkomst till kunddata granskas och loggas.  

Dataåtkomstkontroller:
1.  Endast bevaras i 90 dagar efter att Stäng fallet maximalt.
2.  Kunden har alltid möjlighet att ha data tas bort när som helst i denna 90-dagarsperiod.
3.  Microsoft-anställda får åtkomst till data på en-fall till fall och endast efter behov för att lösa problemet support. 
4.  I fall är där Microsoft måste dela kunddata med OEM-partners, kundernas medgivande obligatoriskt.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Begäranden om DSR (Data Subject) kontroller gör kunder har?
Såsom nämnts tidigare stöder Microsoft på begäran Databorttagning per kundbegäran. Kunder kan begära att vår supporttekniker tar bort alla loggar för ett visst fall när som helst för att välja kundens innan data raderas permanent.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Microsoft meddelar kunder när data tas bort?
För automatiserade borttagning av åtgärden (90 dagar efter att Stäng fall), vi inte proaktivt nå ut till kunder och meddela dem om borttagningen. 

Microsoft-supporttekniker har åtkomst till verktyget där de kan inleda databorttagningen på begäran och användaren kan ange bekräftelse på telefonen med kunden när den är klar för åtgärden för borttagning av data på begäran.

## <a name="diagnostic-data"></a>Diagnostikdata
Som en del av supportprocessen, Azure Stack-operatörer kan [dela diagnostikloggar](azure-stack-diagnostics.md) med Azure Stack stöd och engineering team för att underlätta felsökningen.

Microsoft tillhandahåller ett verktyg och skript för kunder att samla in och ladda upp begärt diagnostikloggfilerna. När samlats in filerna överförs via en HTTPS-skyddad krypterad anslutning till Microsoft. Eftersom HTTPS ger krypteringen över nätverket, det finns inga lösenord krävs för kryptering under överföring. När de tas emot loggar krypteras och lagras tills de tas bort 90 dagar efter supportärende stängs automatiskt.

## <a name="telemetry-data"></a>Telemetridata
[Azure Stack-telemetri](azure-stack-telemetry.md) överför systemdata automatiskt till Microsoft via användarupplevelsen är ansluten. Azure Stack-operatörer har kontroller för att anpassa telemetrifunktioner och sekretessinställningar när som helst.

Microsoft avser inte att samla in känsliga data, till exempel kreditkortsnummer, användarnamn och lösenord, e-postadresser eller liknande känslig information. Om vi fastställer att känslig information har mottagits av misstag, vi ta bort den. 

## <a name="billing-data"></a>Faktureringsinformation
[Fakturering för Azure Stack](azure-stack-usage-reporting.md) använder globala Azure fakturering och användning i pipeline och är därför i enlighet med riktlinjerna för Microsofts efterlevnad.

Azure Stack-operatörer kan konfigurera Azure Stack för att vidarebefordra användningsinformation till Azure för fakturering. Detta krävs för flera noder Azure Stack-kunder som väljer faktureringsmodellen för betalning som du-användning. Användningsrapport styrs oberoende från telemetri och krävs inte för flera noder kunder som väljer att modellen kapacitet eller Azure Stack Development Kit användare. Dessa scenarier kan användningsrapportering kan inaktiveras med hjälp av [registrering skriptet](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Nästa steg 
[Läs mer om Azure Stack-säkerhet](azure-stack-security-foundations.md) 
