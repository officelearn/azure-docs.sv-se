---
title: Webhook-aktiviteten i Azure Data Factory | Microsoft Docs
description: Webhook-aktiviteten fortsätter inte körning av pipelinen förrän den validerar anslutna datauppsättningen med vissa kriterier som användaren anger.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 0b8b892f02e54c3b0ddb155af97ce63ff115bb1f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523005"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-aktiviteten i Azure Data Factory
Du kan använda en hook webbaktivitet för att styra körningen av pipelines med hjälp av din anpassade kod. Med webhook-aktiviteten kan kan kunder anropa en slutpunkt och skicka en Motringnings-URL. Pipeline-körning väntar tills återanropet som anropas innan du fortsätter till nästa aktivitet.

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Egenskaperna för anslutningstypen



Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på web hook-aktivitet | String | Ja |
typ | Måste vara inställt på **WebHook**. | String | Ja |
metod | REST API-metoden för mål-slutpunkten. | sträng. Typer som stöds: 'POST' | Ja |
url | Mål-slutpunkten och sökvägen | Sträng (eller uttryck med resultType av sträng). | Ja |
Rubriker | Rubriker som skickas till begäran. Till exempel vill ange språk och typ för en begäran: ”headers”: {”Accept-språk” ”: en-us”, ”Content-Type”: ”application/json”}. | Sträng (eller ett uttryck med resultType av sträng) | Ja, krävs Content-type-huvud. ”headers”: {”Content-Type”: ”application/json”} |
brödtext | Representerar den nyttolast som skickas till slutpunkten. | Texten som skickas tillbaka till anropet tillbaka URI: N ska vara en giltig JSON. Se schemat för nyttolasten i begäran i [begäran nyttolast schemat](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) avsnittet. | Ja |
autentisering | Autentiseringsmetod som används för att anropa slutpunkten. Typer som stöds är ”Basic” eller ”ClientCertificate”. Mer information finns i [autentisering](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) avsnittet. Undanta den här egenskapen om autentisering inte krävs. | Sträng (eller ett uttryck med resultType av sträng) | Nej |
timeout | Hur länge aktiviteten ska vänta tills den &#39;callbackuri&#39; anropas. Hur länge aktiviteten väntar på callbackuri anropas. Standardvärdet är 10mins (”00: 10:00”). Formatet är Timespan d.v.s. d.hh:mm:ss | String | Nej |

## <a name="additional-notes"></a>Ytterligare information

Azure Data Factory skickar ytterligare en egenskap ”callbackuri” i texten till url-slutpunkten och förväntas innehålla URI: n anropas innan timeout-värdet som angetts. Om URI: n inte har anropats misslyckas aktiviteten med status 'Nådde sin tidsgräns'.

Web hook aktiviteten själva inte bara när anropet till den anpassa slutpunkten misslyckas. Eventuella felmeddelanden kan läggs till i brödtexten i motringningen och används i en efterföljande aktivitet.

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
