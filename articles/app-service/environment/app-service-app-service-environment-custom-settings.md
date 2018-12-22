---
title: Anpassade konfigurationsinställningar för App Service-miljöer – Azure
description: Anpassade konfigurationsinställningar för App Service-miljöer
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: de68c59987a7ec1198c344cc22978ebed09c75e8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271367"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Anpassade konfigurationsinställningar för App Service-miljöer
## <a name="overview"></a>Översikt
Eftersom App Service-miljöer är isolerade i en enda kund finns det vissa konfigurationsinställningar som kan användas exklusivt för App Service-miljöer. I den här artikeln beskrivs de olika anpassningar som är tillgängliga för App Service-miljöer.

Om du inte har en App Service-miljö kan du läsa [Skapa en App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

Du kan lagra App Service-miljöanpassningar med hjälp av en matris i det nya attributet **clusterSettings**. Det här attributet finns i ordlistan ”Egenskaper” för *hostingEnvironments* Azure Resource Manager-entiteten.

Följande förkortade Resource Manager-mallfragment visar attributet **clusterSettings**:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

Attributet **clusterSettings** kan ingå i en Resource Manager-mall för att uppdatera App Service-miljön.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Använda Azure Resource Explorer för att uppdatera en App Service-miljö
Du kan också uppdatera App Service-miljön med hjälp av [Azure Resource Explorer](https://resources.azure.com).  

1. Gå till noden för App Service-miljön i resursutforskaren (**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**). Klicka sedan på den specifika App Service-miljö du vill uppdatera.
2. I den högra rutan klickar du på **Läs/Skriv** i det övre verktygsfältet för att tillåta interaktiv redigering i resursutofrskaren.  
3. Klicka på den blå **redigeringsknappen** att Resource Manager-mallen kan redigeras.
4. Rulla längst ned i den högra rutan. Attributet **clusterSettings** är allra längst ned på sidan, där du kan ange eller uppdatera dess värde.
5. Ange (eller kopiera och klistra in) matrisen med konfigurationsvärden som du vill ha i attributet **clusterSettings**.  
6. Klicka på den gröna knappen **PUT** (Placera) som finns högst upp i det högra fönstret för att genomföra ändringen i App Service-miljön.

Du skickar hur som helst in ändringen. Det tar ungefär 30 minuter gånger antalet klientdelar i App Service-miljön innan ändringen träder i kraft.
Om en App Service-miljö exempelvis har fyra klientdelar tar det ungefär två timmar för konfigurationsuppdateringen att slutföras. Medan konfigurationsändringen distribueras kan inga andra skalningsåtgärder eller ändra konfigurationsåtgärder äga rum i App Service-miljön.

## <a name="disable-tls-10"></a>Inaktivera TLS 1.0
En återkommande fråga från kunder, särskilt de kunder som arbetar med PCI-efterlevnadsgranskning, är hur man explicit inaktiverar TLS 1.0 för appar.

TLS 1.0 kan inaktiveras via följande **clusterSettings**-post:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Ändra ordning på TLS-chiffersvit
En annan fråga från kunder är om de kan ändra listan över chiffer som förhandlas av servern. Det gör man genom att ändra **clusterSettings** enligt nedan. Listan över tillgängliga chiffersviter kan hämtas från [den här MSDN-artikeln](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Om felaktiga värden har angetts för chiffersviten som SChannel inte förstår kan all TLS-kommunikation till servern sluta fungera. I sådana fall måste du ta bort posten *FrontEndSSLCipherSuiteOrder* post från **clusterSettings** och skicka den uppdaterade Resource Manager-mallen för att återgå till standardchiffersvitinställningarna.  Använd den här funktionen med försiktighet.
> 
> 

## <a name="get-started"></a>Kom igång
På mallwebbplatsen för Azure-snabbstarten för Resource Manager finns en mall med basdefinitionen för att [skapa en App Service-miljö](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
