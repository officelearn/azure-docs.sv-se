---
title: Översikt över Microsoft Azure Data Box – tung| Microsoft Docs i data
description: Beskriver Azure Data Box, en hybridlösning som gör det möjligt att överföra enorma mängder data till Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: 0f71d9b4400041db50cb3e24940e922acde55edc
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991693"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Vad är Azure Data Box – tung? (förhandsversion)

Azure Data Box tung kan du skicka hundratals terabyte data till Azure i en snabb, prisvärd, och tillförlitligt sätt. Data överförs till Azure genom att skicka en Data Box tung enhet med 1 PB lagringskapacitet, där du fyller med dina data och skickar tillbaka till Microsoft. Enheten har en robust gemener och versaler att skydda och skydda dina data under överföringen.

Data Box tung förhandsvisas just nu. Registrera dig att begära för en enhet via Azure portal. När enheten tas emot i ditt datacenter, konfigurera den med hjälp av det lokala webbgränssnittet. Kopiera data från dina servrar till enheten och skicka sedan tillbaka enheten till Azure. Dina data har överförts till Azure Storage-konton i Azure-datacenter. Du kan följa hela slutpunkt till slutpunkt-processen i Azure-portalen.


> [!IMPORTANT]
> - Data Box – tung är en förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen.
> - Om du vill beställa en enhet, registrerar du dig i [Preview-portalen](https://aka.ms/azuredatabox).
> - Under förhandsversionen kan Data Box – tung levereras till kunder i USA och EU. Mer information finns i [Regional tillgänglighet](#region-availability).

## <a name="use-cases"></a>Användningsfall

Data Box tung passar bäst för datamängder i hundratals terabyte, där nätverksanslutning är tillräckligt för att ladda upp data till Azure. Dataflytten kan vara enstaka, periodisk eller en inledande massdataöverföring följt av periodiska överföringar. Här följer olika scenarier där Data Box – tung kan användas för dataöverföring.

 - **Engångsmigrering** – när stora mängder lokala data flyttas till Azure.
     - Flytta ett mediebibliotek från offlineband till Azure för att skapa en online mediabibliotek.
     - Migrera dina VM-servergrupp, SQLServer och program till Azure.
     - Flytta historiska data till Azure för detaljerad analys och rapport med hjälp av HDInsight.

 - **Inledande bulköverföring** – när en inledande massöverföring utförs med hjälp av Data Box – tung (startvärde) följt av inkrementella överföringar över nätverket.
     - Till exempel används Data Box stor och säkerhetskopieringslösningar partner för att flytta första stora historiska säkerhetskopiering till Azure. När du är klar överförs inkrementella data via nätverket till Azure Storage.

 - **Periodiska uppladdningar** – när stora mängder data genereras med jämna mellanrum och behöver flyttas till Azure. Till exempel i energiutforskning, där videoinnehåll genereras på oljeplattformar och vindkraftsparker.

## <a name="benefits"></a>Fördelar

Data Box tung har utformats för att flytta stora mängder data till Azure med lite eller ingen inverkan på ditt nätverk. Lösningen har följande fördelar:

- **Hastighet** -Data Box tung använder högpresterande 40-Gbit/s-nätverksgränssnitt.

- **Security** -Data Box tung har inbyggda säkerhetsskydd för enheten, data och tjänsten.
    - Enheten har ett robust enhetshölje som skyddas av manipuleringsskyddade skruvar och säkerhetsförslutna klistermärken.
    - Data på enheten skyddas alltid med AES 256-bitars kryptering.
    - Enheten kan bara låsas upp med ett lösenord som anges i Azure-portalen.
    - Tjänsten skyddas av säkerhetsfunktionerna i Azure.
    - När dina data har överförts till Azure, rensas diskar på enheten, i enlighet med standarder för National Institute of Standards and Technology (NIST) 800 88r1.


## <a name="features-and-specifications"></a>Funktioner och specifikationer

Data Box tung enheten har följande funktioner i den här versionen.

| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Vikt                                                  | ~ 500 lbs.                |
| Mått                                              | Bredd: 26 tum höjd: 28 tum längd: 48 tum |
| Rackutrymme                                              | Det går inte att vara rackmonterade|
| Kablar som krävs                                         | 4 X förankrad 120 V/10 A strömkablar (NEMA 5 till 15) ingår <br> Enheten har stöd för upp till 240 V power och har C-13 power behållare <br> Använda nätverkskablarna som är kompatibel med [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
|Power                                                    | 4 inbyggda power anger enheter (PSUs) delas mellan båda enheten noder|
| Lagringskapacitet                                        | ~ 1-PB raw, 70 diskar på 14 TB <br> 770 TB användbar kapacitet|
|Antal noder                                          | 2 oberoende noder per enhet (500 TB som är varje) |
| Nätverksgränssnitt per nod                             | 4 nätverksgränssnitt per nod <br> MGMT, DATA3 <ul><li> 2 x 1 GbE-gränssnitt </li><li> MGMT för hantering, inte konfigureras av användaren, används för den första installationen </li><li> DATA3 är ett gränssnitt för användarangiven data, DHCP Dynamic Host Configuration Protocol () som standard</li><li>1 GbE-nätverksgränssnitt kan också konfigureras som 10 GbE-gränssnitt</li></ul>Fil1 fil2-gränssnitt <ul><li>2 x 40 GbE-gränssnitt </li><li> DHCP som standard eller statisk, konfigurerbara data användargränssnitt</li>|


## <a name="components"></a>Komponenter

Data Box – tung innehåller följande komponenter:

* **Data Box – tung-enhet** – en fysisk enhet med robust utsida som lagrar data på ett säkert sätt. Den här enheten har en användbar lagringskapacitet på 770 TB.
    
* **Data Box-tjänsten** – ett tillägg till Azure-portalen som gör att du kan hantera en Data Box – tung-enhet via ett webbgränssnitt som du kan komma åt från olika geografiska platser. Använda Data Box-tjänsten för att administrera din Data Box tung enhet. Tjänstens uppgifter är till exempel hur du skapar och hanterar beställningar, visar och hanterar aviseringar och hanterar filresurser.  

* **Lokalt webbgränssnitt** – ett webbaserat gränssnitt som används för att konfigurera enheten så att den kan ansluta till det lokala nätverket och sedan registrera enheten med Data Box-tjänsten. Använd även det lokala webbgränssnittet till att stänga av och starta om enheten, visa kopieringsloggar och kontakta Microsoft Support om du vill skicka en tjänstbegäran.


## <a name="the-workflow"></a>Arbetsflödet

Ett typiskt flöde omfattar följande steg:

1. **Beställning** – skapa en beställning i Azure-portalen och ange leveransinformation och Azure-mållagringskonto för dina data. Om enheten är tillgänglig förbereder och skickar Azure enheten med ett spårnings-ID.

2. **Ta emot** – när enheten har levererats kabelansluter du enheten för nätverket och startar med angivna kablar. Aktivera och anslut till enheten. Konfigurera enhetsnätverket och montera filresurser på värddatorn som du vill kopiera data från.

3. **Kopiera data** – Kopiera data till Data Box – tung-resurser.

4. **Returnera** – Förbered, stäng av och skicka tillbaka enheten till Azure-datacentret.

5. **Ladda upp** – data kopieras automatiskt från enheten till Azure. Enhetsdiskarna raderas på ett säkert sätt enligt riktlinjerna från National Institute of Standards and Technology (NIST).

Hela processen meddelas via e-post på alla status ändras.

## <a name="region-availability"></a>Regional tillgänglighet

Data Box tung kan överföra data baserat på den region där tjänsten distribueras, land/region som enheten levereras och mål-Azure storage-konto där du kan överföra data.

- **Tjänstens tillgänglighet** – För den här versionen är Data Box – tung tillgänglig i följande regioner:
    - Alla offentliga molnregioner i USA – USA, västra centrala, USA, västra 2, USA, västra, USA, södra centrala, USA, centrala, USA, norra centrala, USA, östra och USA, östra 2.
    - EU – Europa, västra och Europa, norra.
    - Storbritannien – Storbritannien, södra och Storbritannien, västra.
    - Frankrike – Frankrike, centrala och Frankrike, södra.

- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner där tjänsten är tillgänglig.

Gå till den senaste informationen om regiontillgänglighet för Data Box tung [Azure-produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Registrera dig

Data Box – tung är en förhandsversion och du måste registrera dig. Vidta följande steg för att registrera dig för Data Box tung:

1. Logga in på Azure-portalen på: https://aka.ms/azuredatabox.
2. Klicka på **+ skapa en resurs** att skapa en ny resurs. Sök efter **Azure Data Box**. Välj **Azure Data Box**-tjänsten.

    <!--![The Data Box Heavy sign up 1]()-->

3. Klicka på **Skapa**.

    <!--![The Data Box Heavy sign up 2]()-->

4. Välj den prenumeration som du vill använda för förhandsversionen av Data Box – tung. Välj den region där du vill distribuera Data Box – tung-resursen. I alternativet **Data Box – tung** klickar du på **Registrera dig**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Svara på frågor om data bosatt land/region, tidsperiod, target Azure-tjänsten för dataöverföring, nätverkets bandbredd och data transfer frekvens. Granska sekretess och villkor och markera kryssrutan mot att Microsoft kan använda din e-postadress för att kontakta dig.

    <!--![The Data Box Heavy sign up 4]()-->

När du är registrerad och aktiverad för förhandsversionen kan beställa du en Data Box tung.

    