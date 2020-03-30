---
title: Översikt över Microsoft Azure Data Box – tung| Microsoft Docs i data
description: Beskriver Azure Data Box, en hybridlösning som gör det möjligt att överföra enorma mängder data till Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 362b7457538a16d389c3cc40fc44da19b073c0b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "70142923"
---
# <a name="what-is-azure-data-box-heavy"></a>Vad är Azure Data Box – tung?

Med Azure Data Box Heavy kan du skicka hundratals terabyte data till Azure på ett snabbt, billigt och tillförlitligt sätt. Data överförs till Azure genom att skicka en Data Box Heavy-enhet med 1 PB-lagringskapacitet, som du fyller med dina data och skickar tillbaka till Microsoft. Enheten har ett robust hölje för att skydda och skydda dina data under överföringen.

När enheten har tagits emot i ditt datacenter konfigurerar du den med det lokala webbgränssnittet. Kopiera data från dina servrar till enheten och skicka sedan tillbaka enheten till Azure. I Azure-data överförs dina data till dina Azure Storage-konton. Du kan spåra hela slut-till-slut-processen i Azure-portalen.


> [!IMPORTANT]
> - Om du vill begära en enhet registrerar du dig i [Azure-portalen](https://portal.azure.com).


## <a name="use-cases"></a>Användningsfall

Data Box Heavy passar bäst för datastorlekar i hundratals terabyte, där nätverksanslutningen är otillräcklig för att överföra data till Azure. Dataflytten kan vara enstaka, periodisk eller en inledande massdataöverföring följt av periodiska överföringar. Här följer olika scenarier där Data Box – tung kan användas för dataöverföring.

 - **Engångsmigrering** – när stora mängder lokala data flyttas till Azure.
     - Flytta ett mediebibliotek från offlineband till Azure för att skapa ett online-mediebibliotek.
     - Migrera din VM-servergrupp, SQL-server och program till Azure.
     - Flytta historiska data till Azure för djupgående analys och rapport med HDInsight.

 - **Inledande bulköverföring** – när en inledande massöverföring utförs med hjälp av Data Box – tung (startvärde) följt av inkrementella överföringar över nätverket.
     - Data Box Heavy och en partner för säkerhetskopieringslösningar används till exempel för att flytta inledande stor historisk säkerhetskopiering till Azure. När du är klar överförs inkrementella data via nätverket till Azure Storage.

 - **Periodiska uppladdningar** – när stora mängder data genereras med jämna mellanrum och behöver flyttas till Azure. Till exempel i energiutforskning, där videoinnehåll genereras på oljeplattformar och vindkraftsparker.

## <a name="benefits"></a>Fördelar

Data Box Heavy är utformad för att flytta stora mängder data till Azure med liten eller ingen inverkan på ditt nätverk. Lösningen har följande fördelar:

- **Hastighet** - Data Box Heavy använder högpresterande 40-Gbps nätverksgränssnitt.

- **Säkerhet** - Data Box Heavy har inbyggda säkerhetsskydd för enheten, data och tjänsten.
    - Enheten har ett robust enhetshölje som skyddas av manipuleringsskyddade skruvar och säkerhetsförslutna klistermärken.
    - Data på enheten skyddas alltid med AES 256-bitars kryptering.
    - Enheten kan bara låsas upp med ett lösenord som anges i Azure-portalen.
    - Tjänsten skyddas av säkerhetsfunktionerna i Azure.
    - När dina data har överförts till Azure rensas diskarna på enheten rensas i enlighet med National Institute of Standards and Technology (NIST) 800-88r1-standarder.


## <a name="features-and-specifications"></a>Funktioner och specifikationer

Data Box Heavy-enheten har följande funktioner i den här versionen.

| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Vikt                                                  | ~ £ 500 lbs. <br>Anordning på låshjul för transport|
| Dimensioner                                              | Bredd: 26 tum Höjd: 28 tum Längd: 48 tum |
| Rackutrymme                                              | Det går inte att rackmonteras|
| Kablar som krävs                                         | 4 jordad 120 V / 10 A nätsladdar (NEMA 5-15) ingår <br> Enheten stöder upp till 240 V-effekt och har C-13 kraftkärl <br> Använd nätverkskablar som är kompatibla med [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Power                                                    | 4 inbyggda nätaggregat som delas mellan båda enhetsnoderna <br> 1 200 watt typiskt effektdrag|
| Lagringskapacitet                                        | ~ 1-PB rå, 70 diskar med 14 TB vardera <br> 770 TB användbar kapacitet|
| Antal noder                                          | 2 oberoende noder per enhet (500 TB vardera) |
| Nätverksgränssnitt per nod                             | 4 nätverksgränssnitt per nod <br><br> MGMT, DATA3 <ul><li> 2 X 1 GBE-gränssnitt </li><li> MGMT är för hantering och första installation, inte användaren konfigurerbar </li><li> DATA3 är SOM STANDARD ANVÄNDARkonfigurerbart och DHCP (Dynamic Host Configuration Protocol)</li></ul>DATA1, DATA2-datagränssnitt <ul><li>2 X 40 GBE-gränssnitt </li><li> Användaren kan konfigureras för DHCP (standard) eller statisk</li></ul>|


## <a name="components"></a>Komponenter

Data Box – tung innehåller följande komponenter:

* **Data Box – tung-enhet** – en fysisk enhet med robust utsida som lagrar data på ett säkert sätt. Den här enheten har en användbar lagringskapacitet på 770 TB.
    
* **Data Box-tjänsten** – ett tillägg till Azure-portalen som gör att du kan hantera en Data Box – tung-enhet via ett webbgränssnitt som du kan komma åt från olika geografiska platser. Använd databox-tjänsten för att administrera din Data Box Heavy-enhet. Tjänstens uppgifter är till exempel hur du skapar och hanterar beställningar, visar och hanterar aviseringar och hanterar filresurser.  

* **Lokalt webbgränssnitt** – ett webbaserat gränssnitt som används för att konfigurera enheten så att den kan ansluta till det lokala nätverket och sedan registrera enheten med Data Box-tjänsten. Använd även det lokala webbgränssnittet till att stänga av och starta om enheten, visa kopieringsloggar och kontakta Microsoft Support om du vill skicka en tjänstbegäran.


## <a name="the-workflow"></a>Arbetsflödet

Ett typiskt flöde omfattar följande steg:

1. **Beställning** – skapa en beställning i Azure-portalen och ange leveransinformation och Azure-mållagringskonto för dina data. Om enheten är tillgänglig förbereder och skickar Azure enheten med ett spårnings-ID.

2. **Ta emot** – när enheten har levererats kabelansluter du enheten för nätverket och startar med angivna kablar. Aktivera och anslut till enheten. Konfigurera enhetsnätverket och montera filresurser på den värddator som du vill kopiera data från.

3. **Kopiera data** – Kopiera data till Data Box – tung-resurser.

4. **Returnera** – förbered, stäng av och skicka tillbaka enheten till Azure-datacentret.

5. **Ladda upp** – data kopieras automatiskt från enheten till Azure. Enhetsdiskarna raderas på ett säkert sätt enligt riktlinjerna från National Institute of Standards and Technology (NIST).

Under hela den här processen får du ett meddelande via e-post om alla statusändringar.

## <a name="region-availability"></a>Regional tillgänglighet

Data Box Heavy kan överföra data baserat på den region där tjänsten distribueras, land/region dit enheten levereras och målet Azure-lagringskonto där du överför data.

- **Tjänstens tillgänglighet** – För den här versionen är Data Box – tung tillgänglig i följande regioner:
    - Alla offentliga molnregioner i USA – USA, västra centrala, USA, västra 2, USA, västra, USA, södra centrala, USA, centrala, USA, norra centrala, USA, östra och USA, östra 2.
    - EU – Europa, västra och Europa, norra.
    - Storbritannien – Storbritannien, södra och Storbritannien, västra.
    - Frankrike – Frankrike, centrala och Frankrike, södra.

- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner där tjänsten är tillgänglig.

Den senaste informationen om regiontillgänglighet för Data Box Heavy finns i [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Registrera dig

Gör så här för att registrera dig för Data Box Heavy:

1. Logga in på Azure-portalen på: https://portal.azure.com.
2. Klicka på **+ Skapa en resurs** för att skapa en ny resurs. Sök efter **Azure Data Box**. Välj **Azure Data Box**-tjänsten.
3. Klicka på **Skapa**.
4. Välj den prenumeration som du vill använda för Data Box Heavy. Välj den region där du vill distribuera Data Box – tung-resursen. I alternativet **Data Box – tung** klickar du på **Registrera dig**.
5. Svara på frågor om databostad land/region, tidsram, mål Azure-tjänst för dataöverföring, nätverksbandbredd och dataöverföringsfrekvens. Granska sekretess och villkor och markera kryssrutan mot att Microsoft kan använda din e-postadress för att kontakta dig.

När du har registrerat dig kan du beställa en Data Box Heavy.

    
