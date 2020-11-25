---
title: Felsöka säkerhets-och åtkomst kontroll problem
description: Lär dig hur du felsöker säkerhets-och åtkomst kontroll problem i Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008741"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Felsöka problem med Azure Data Factory säkerhet och åtkomst kontroll

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för säkerhet och åtkomst kontroll i Azure Data Factory.

## <a name="common-errors-and-messages"></a>Vanliga fel och meddelanden

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Ogiltigt eller tomt problem med autentiserings nyckel efter inaktive ring av offentlig nätverks åtkomst

#### <a name="symptoms"></a>Symtom

Integration runtime med egen värd genererar fel meddelandet "Authentication Key är ogiltig eller tom" efter inaktive ring av offentlig nätverks åtkomst för Data Factory.

#### <a name="cause"></a>Orsak

Problemet beror troligen på problem med DNS-matchning, eftersom inaktive ring av offentliga anslutningar och etablering av en privat slut punkt inte hjälper till att återansluta.

#### <a name="resolution"></a>Lösning

1. Gjorde en PsPing till ADF-FQDN och påträffade att bufferten kom till en offentlig slut punkt för ADF, även när den har inaktiverats.

1. Ändra värd filen i den virtuella datorn för att mappa privat IP till FQDN och kör en PsPing igen. Bufferten kan gå till rätt privata IP-adress för ADF och sedan.

1. Registrera den egna värdbaserade integrerings körningen så att vi kan hitta den och köra den.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Det går inte att registrera IR-autentisering på lokala datorer med egen värd på grund av en privat länk

#### <a name="symptoms"></a>Symtom

Det går inte att registrera IR-autentisering på den lokala virtuella datorn på grund av att en privat länk är aktive rad.

Fel informationen visas som nedan:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Orsak

Problemet kan bero på den virtuella datorn där SHIR försöker installeras. Åtkomst till offentliga nätverk ska vara aktive rad för att ansluta till molnet.

#### <a name="resolution"></a>Lösning

 **Lösning 1:** Du kan följa stegen nedan för att lösa problemet:

1. Navigera till länken nedan: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Klicka på **prova** alternativet och fyll i all information som krävs. Klistra in nedanstående egenskap i **bröd texten** också:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Klicka på **Kör** i slutet av sidan för att köra funktionen. Se till att du får svars koden 200. Egenskapen som vi klistrade in visas också i JSON-definitionen.

1. Sedan kan du försöka lägga till IR-autentiseringsnyckel igen i integration Runtime.


**Lösning 2:** Du kan läsa följande artikel för lösningen:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Försök att aktivera åtkomst till det offentliga nätverket med användar gränssnittet.

![Aktivera offentlig nätverks åtkomst](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp med fel sökning kan du prova följande resurser:

*  [Privat länk för Data Factory](data-factory-private-link.md)
*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)