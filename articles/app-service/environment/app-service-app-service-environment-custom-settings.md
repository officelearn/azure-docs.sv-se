---
title: Anpassade inställningar för Apptjänstmiljöer
description: Anpassade konfigurationsinställningar för Apptjänstmiljöer
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
ms.custom: mvc
ms.openlocfilehash: d60cdca78c143996fa5935726db0631321c9e2fe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129523"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Anpassade konfigurationsinställningar för Apptjänstmiljöer
## <a name="overview"></a>Översikt
Eftersom Apptjänstmiljöer är isolerad för att en kund är vissa konfigurationsinställningar som kan tillämpas enbart på Apptjänstmiljöer. Den här artikeln beskrivs de olika anpassningar som är tillgängliga för Apptjänstmiljöer.

Om du inte har en Apptjänst-miljö, se [så här skapar du en Apptjänst-miljö](app-service-web-how-to-create-an-app-service-environment.md).

Du kan lagra Apptjänstmiljö anpassningar med hjälp av en matris i den nya **clusterSettings** attribut. Det här attributet finns i ”egenskaper” ordlistan av den *hostingEnvironments* Azure Resource Manager-entiteten.

Följande förkortas Resource Manager mallen fragment visas den **clusterSettings** attribut:

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

Den **clusterSettings** attribut kan ingå i en Resource Manager-mall för att uppdatera Apptjänst-miljön.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Använda Azure Resource Explorer för att uppdatera en Apptjänst-miljö
Du kan också uppdatera Apptjänst-miljön med hjälp av [resursutforskaren Azure](https://resources.azure.com).  

1. Gå till noden i resursutforskaren, för Apptjänst-miljön (**prenumerationer** > **resursgrupper** > **providers** > **Microsoft.Web** > **hostingEnvironments**). Klicka på den specifika Apptjänstmiljö som du vill uppdatera.
2. I den högra rutan, klickar du på **läsning och skrivning** i det övre verktygsfältet för att tillåta interaktiva redigering i Resursläsaren.  
3. Klicka på blå **redigera** knappen så att Resource Manager-mall kan redigeras.
4. Bläddra längst ned i den högra rutan. Den **clusterSettings** attributet är på längst ned, där du kan ange eller uppdatera dess värde.
5. Skriv (eller kopiera och klistra in) matrisen konfigurationsvärden som du vill ha i den **clusterSettings** attribut.  
6. Klicka på gröna **PLACERA** knappen som finns längst upp i den högra rutan för att utföra ändringen i Apptjänst-miljön.

Men du skickar ändringen tar ungefär 30 minuter multiplicerat med antalet synliga komponenter i Apptjänst-miljön för att ändringarna ska börja gälla.
Till exempel om en Apptjänst-miljö har fyra frontwebbservrarna, tar ungefär två timmar för av konfigurationsuppdateringen ska slutföras. Medan konfigurationsändringen lyfts, kan någon annan skalning operations eller ändra konfigurationsåtgärder ske i Apptjänst-miljön.

## <a name="disable-tls-10"></a>Inaktivera TLS 1.0
En återkommande fråga från kunder, särskilt kunder som arbetar med PCI-överensstämmelse granskningar, så att uttryckligen inaktivera TLS 1.0 för sina appar.

TLS 1.0 kan inaktiveras via följande **clusterSettings** post:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Ändra TLS cipher suite order
En annan fråga från kunder är om de kan ändra listan över chiffer som förhandlas fram av servern, och detta kan uppnås genom att ändra den **clusterSettings** enligt nedan. Listan över tillgängliga krypteringssviter kan hämtas från [MSDN-artikel](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Om felaktiga värden har angetts för chiffersviten SChannel inte förstår kan alla TLS-kommunikation till servern sluta fungera. I så fall behöver du ta bort den *FrontEndSSLCipherSuiteOrder* post från **clusterSettings** och skicka den uppdaterade mallen Resource Manager om du vill återgå till standardinställningarna cipher suite.  Använd den här funktionen med försiktighet.
> 
> 

## <a name="get-started"></a>Kom igång
Mallwebbplatsen för Azure Quickstart Resource Manager-innehåller en mall med basdefinitionen för [att skapa en Apptjänst-miljö](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
