---
title: Översikt över Microsoft Azure Data Box – tung| Microsoft Docs i data
description: Beskriver Azure Data Box, en hybridlösning som gör det möjligt att överföra enorma mängder data till Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 0a5b7f93f9ac6cc5b1076881727a42fd5b95ff4b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235164"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Vad är Azure Data Box – tung? (Förhandsversion)

Med Microsoft Azure Data Box-hybridlösningen kan du skicka hundratals terabyte data till Azure på ett snabbt, prisvärt och tillförlitligt sätt. Den säkra dataöverföringen påskyndas genom att skicka en upphovsrättsskyddad lagringsenhet med 1 PB lagringskapacitet via frakt. Enheten har ett robust hölje för att skydda data under överföringen.

Data Box – tung är för närvarande en förhandsversion och du kan registrera dig för att beställa en enhet via Azure-portalen. När enheten tas emot i ditt datacenter kan du konfigurera den med hjälp av det lokala webbgränssnittet. Kopiera data från dina servrar till enheten och skicka sedan tillbaka enheten till Azure. I Azure-datacentret överförs dina data automatiskt från enheten till Azure. Hela processen spåras från slutpunkt till slutpunkt av Data Box-tjänsten på Azure-portalen.


> [!IMPORTANT]
> - Data Box – tung är en förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen. 
> - Om du vill beställa en enhet, registrerar du dig i [Preview-portalen](https://aka.ms/).
> - Under förhandsversionen kan Data Box – tung levereras till kunder i USA och EU. Mer information finns i [Regional tillgänglighet](#region-availability).

## <a name="use-cases"></a>Användningsfall

Data Box – tung passar bäst för överföring av data som är större än 500 TB i scenarier med begränsad eller ingen nätverksanslutning. Dataflytten kan vara enstaka, periodisk eller en inledande massdataöverföring följt av periodiska överföringar. Här följer olika scenarier där Data Box – tung kan användas för dataöverföring.

 - **Engångsmigrering** – när stora mängder lokala data flyttas till Azure. 
     - Flytta ett mediebibliotek från offlineband i Azure för att skapa ett mediebibliotek online.
     - Migrera VM-servergrupp, SQLServer och program till Azure
     - Flytta historiska data till Azure för djupanalys och rapportering med hjälp av HDInsight

 - **Inledande bulköverföring** – när en inledande massöverföring utförs med hjälp av Data Box – tung (startvärde) följt av inkrementella överföringar över nätverket. 
     - Till exempel används partners med säkerhetskopieringslösningar som Commvault och Data Box – tung för att flytta den första stora historiska säkerhetskopieringen till Azure. När du är klar överförs inkrementella data via nätverket till Azure Storage.

 - **Periodiska uppladdningar** – när stora mängder data genereras med jämna mellanrum och behöver flyttas till Azure. Till exempel i energiutforskning, där videoinnehåll genereras på oljeplattformar och vindkraftsparker.      

## <a name="benefits"></a>Fördelar

Data Box – tung har utformats för att flytta stora mängder data till Azure med lite eller ingen inverkan på nätverket. Lösningen har följande fördelar:

- **Hastighet** – Data Box – tung 40 Gbit/s-nätverksgränssnitt med höga prestanda.

- **Skydd** – Data Box – tung har inbyggt säkerhetsskydd för enheten, data och tjänsten.
    - Enheten har ett robust enhetshölje som skyddas av manipuleringsskyddade skruvar och säkerhetsförslutna klistermärken. 
    - Data på enheten skyddas alltid med AES 256-bitars kryptering.
    - Enheten kan bara låsas upp med ett lösenord som anges i Azure-portalen.
    - Tjänsten skyddas av säkerhetsfunktionerna i Azure.
    - När dina data har laddats upp till Azure rensas diskarna på enheten i enlighet med standarderna NIST 800-88r1.


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>Komponenter

Data Box – tung innehåller följande komponenter:

* **Data Box – tung-enhet** – en fysisk enhet med robust utsida som lagrar data på ett säkert sätt. Den här enheten har en användbar lagringskapacitet på 800 TB. 

    
* **Data Box-tjänsten** – ett tillägg till Azure-portalen som gör att du kan hantera en Data Box – tung-enhet via ett webbgränssnitt som du kan komma åt från olika geografiska platser. Använd Data Box-tjänsten för att utföra daglig administration av din Data Box – tung-enhet. Tjänstens uppgifter är till exempel hur du skapar och hanterar beställningar, visar och hanterar aviseringar och hanterar filresurser.  

* **Lokalt webbgränssnitt** – ett webbaserat gränssnitt som används för att konfigurera enheten så att den kan ansluta till det lokala nätverket och sedan registrera enheten med Data Box-tjänsten. Använd även det lokala webbgränssnittet till att stänga av och starta om enheten, visa kopieringsloggar och kontakta Microsoft Support om du vill skicka en tjänstbegäran.


## <a name="the-workflow"></a>Arbetsflödet

Ett typiskt flöde omfattar följande steg:

1. **Beställning** – skapa en beställning i Azure-portalen och ange leveransinformation och Azure-mållagringskonto för dina data. Om enheten är tillgänglig förbereder och skickar Azure enheten med ett spårnings-ID.

2. **Ta emot** – när enheten har levererats kabelansluter du enheten för nätverket och startar med angivna kablar. Aktivera och anslut till enheten. Konfigurera enhetsnätverket och montera filresurser på värddatorn som du vill kopiera data från.

3. **Kopiera data** – Kopiera data till Data Box – tung-resurser.

4. **Returnera** – Förbered, stäng av och skicka tillbaka enheten till Azure-datacentret.

5. **Ladda upp** – data kopieras automatiskt från enheten till Azure. Enhetsdiskarna raderas på ett säkert sätt enligt riktlinjerna från National Institute of Standards and Technology (NIST).

Under den här processen meddelas du via e-post om alla statusändringar. 

## <a name="region-availability"></a>Regional tillgänglighet

Data Box – tung kan överföra data baserat på den region där tjänsten har distribuerats, det land som enheten levereras till och målkontot för lagring i Azure dit du överför data. 

- **Tjänstens tillgänglighet** – För den här versionen är Data Box – tung tillgänglig i följande regioner:
    - Alla offentliga molnregioner i USA – USA, västra centrala, USA, västra 2, USA, västra, USA, södra centrala, USA, centrala, USA, norra centrala, USA, östra och USA, östra 2.
    - EU – Europa, västra och Europa, norra.
    - Storbritannien – Storbritannien, södra och Storbritannien, västra.
    - Frankrike – Frankrike, centrala och Frankrike, södra.

- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner där tjänsten är tillgänglig. 

## <a name="sign-up"></a>Registrera dig

Data Box – tung är en förhandsversion och du måste registrera dig. Registrera dig för Data Box – tung via följande steg:

1. Logga in på Azure-portalen på: https://aka.ms/azuredatabox.
2. Klicka på **+** för att skapa en ny resurs. Sök efter **Azure Data Box**. Välj **Azure Data Box**-tjänsten.

    <!--![The Data Box Heavy sign up 1]()-->

3. Klicka på **Skapa**.

    <!--![The Data Box Heavy sign up 2]()-->

4. Välj den prenumeration som du vill använda för förhandsversionen av Data Box – tung. Välj den region där du vill distribuera Data Box – tung-resursen. I alternativet **Data Box – tung** klickar du på **Registrera dig**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Svara på frågor om datahemland, tidsperiod, Azure-måltjänst för dataöverföring, nätverkets bandbredd och dataöverföringsfrekvens. Granska sekretess och villkor och markera kryssrutan mot att Microsoft kan använda din e-postadress för att kontakta dig.

    <!--![The Data Box Heavy sign up 4]()-->

När du är registrerad och aktiverad för förhandsversionen kan du beställa Data Box – tung.




