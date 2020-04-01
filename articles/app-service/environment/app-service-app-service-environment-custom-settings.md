---
title: Konfigurera anpassade inställningar
description: Konfigurera inställningar som gäller för hela Azure App Service-miljön. Lär dig hur du gör det med Azure Resource Manager-mallar.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 25393007a3cc878737ea5927cb65bcf7ef945313
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057572"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Anpassade konfigurationsinställningar för App Service-miljöer
## <a name="overview"></a>Översikt
Eftersom App Service-miljöer (ASE) är isolerade i en enda kund finns det vissa konfigurationsinställningar som kan användas exklusivt för App Service-miljöer. I den här artikeln beskrivs de olika anpassningar som är tillgängliga för App Service-miljöer.

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

1. I Resource Explorer går du till noden för App Service Environment (**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**). Klicka sedan på den specifika App Service-miljö du vill uppdatera.
2. I den högra rutan klickar du på **Läs/Skriv** i det övre verktygsfältet för att tillåta interaktiv redigering i resursutofrskaren.  
3. Klicka på den blå **redigeringsknappen** att Resource Manager-mallen kan redigeras.
4. Rulla längst ned i den högra rutan. Attributet **clusterSettings** är allra längst ned på sidan, där du kan ange eller uppdatera dess värde.
5. Ange (eller kopiera och klistra in) matrisen med konfigurationsvärden som du vill ha i attributet **clusterSettings**.  
6. Klicka på den gröna knappen **PUT** (Placera) som finns högst upp i det högra fönstret för att genomföra ändringen i App Service-miljön.

Du skickar hur som helst in ändringen. Det tar ungefär 30 minuter gånger antalet klientdelar i App Service-miljön innan ändringen träder i kraft.
Om en App Service-miljö exempelvis har fyra klientdelar tar det ungefär två timmar för konfigurationsuppdateringen att slutföras. Medan konfigurationsändringen distribueras kan inga andra skalningsåtgärder eller ändra konfigurationsåtgärder äga rum i App Service-miljön.

## <a name="enable-internal-encryption"></a>Aktivera intern kryptering

App Service Environment fungerar som ett svart låda system där du inte kan se de interna komponenterna eller kommunikationen i systemet. Kryptering aktiveras som standard inte mellan interna komponenter för att aktivera högre dataflöde. Systemet är säkert eftersom trafiken är helt otillgänglig för att övervakas eller nås. Om du har ett efterlevnadskrav men som kräver fullständig kryptering av datasökvägen från till, finns det ett sätt att aktivera detta med en clusterSetting.  

        "clusterSettings": [
            {
                "name": "InternalEncryption",
                "value": "1"
            }
        ],
 
När InternalEncryption clusterSetting är aktiverat kan det påverka systemets prestanda. När du gör ändringen för att aktivera InternalEncryption, kommer DIN ASE att vara i ett instabilt tillstånd tills ändringen är helt spridd. Fullständig spridning av ändringen kan ta några timmar att slutföra, beroende på hur många instanser du har i din ASE. Vi rekommenderar starkt att du inte aktiverar detta på en ASE när den används. Om du behöver aktivera detta på en aktivt använd ASE rekommenderar vi starkt att du dirigerar om trafiken till en säkerhetskopieringsmiljö tills åtgärden är klar. 

## <a name="disable-tls-10-and-tls-11"></a>Inaktivera TLS 1.0 och TLS 1.1

Om du vill hantera TLS-inställningar separat för varje app kan du använda de riktlinjer som tillhandahålls med dokumentationen för att [framtvinga TLS-inställningar](../configure-ssl-bindings.md#enforce-tls-versions). 

Om du vill inaktivera all inkommande TLS 1.0- och TLS 1.1-trafik för alla appar i en ASE kan du ange följande **clusterSettings**-post:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

Namnet på inställningen har värdet 1.0, men när den konfigureras inaktiverar den både TLS 1.0 och TLS 1.1.

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
