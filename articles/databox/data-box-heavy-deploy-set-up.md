---
title: Självstudie för att konfigurera Azure Data Box Heavy | Microsoft Docs
description: Lär dig hur du kopplar och ansluter din Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: b6b353e0e01f3f598048e5fbb2682603045b1037
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164465"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Självstudier: Kabel och Anslut till din Azure Data Box Heavy

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Kom igång med Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

I den här självstudien beskrivs hur du sätter kablar, ansluter och aktiverar din Azure Data Box Heavy.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kabelanslut din Data Box Heavy
> * Anslut till din Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Den här guiden innehåller anvisningar om hur du granskar krav, kablar och ansluter din enhet, kopierar data, laddar upp till Azure och sedan verifierar överförda data.

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Beställ Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Du har fått din Data Box Heavy och order statusen i portalen har levererats.
3. Du har granskat [rikt linjerna för data Box Heavy säkerhet](data-box-safety.md).
4. Du måste ha åtkomst till en platt plats i data centret med närhet till en tillgänglig nätverks anslutning som kan hantera en enhet med det här utrymmet. Den här enheten kan inte monteras på en rack.
5. Du har fått fyra jord-och likströms kablar som du kan använda med lagrings enheten.
6. Du bör ha en värddator som är ansluten till datacenternätverket. Din Data Box Heavy kommer att kopiera data från den här datorn. Värd datorn måste köra ett [operativ system som stöds](data-box-heavy-system-requirements.md).
7. Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. 
8. Du måste ha en bärbar dator med RJ-45-kabel för att kunna ansluta till det lokala användar gränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod av enheten en gång.
9. Du behöver 1 40 Gbit/s kabel eller 10 Gbit/s-kabel per enhet-nod.
    - Välj kablar som är kompatibla med [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) Network Interface.
    - För kabeln på 40 Gbit/s måste enhetens slut punkt vara QSFP +.
    - För 10 Gbit/s-kabeln behöver du en SFP +-kabel som ansluts till en 10 Gbit/s-växel i ena änden, med en QSFP + till SFP + adapter (eller uppfyller kraven enligt-adaptern) för det slut som kopplas till enheten.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Kabelanslut enheten för strömförsörjning

Utför följande steg för att kabelansluta enheten.

1. Inspektera enheten och leta efter tecken på manipulation eller andra uppenbara skador. Fortsätt inte om enheten har manipulerats eller skadats. [Kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md) omedelbart för att få hjälp att utvärdera om enheten är i fungerande skick och om de behöver leverera en ersättning.
2. Flytta enheten till installations platsen.

    ![Installations plats för Data Box Heavy enhet](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Lås de bakre omvandlarna på enheten enligt nedan.

    ![Data Box Heavy enhets avsändare låsta](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Leta upp de rattar som låser upp-och bak-dörrarna på enheten. Lås upp och flytta den främre dörren tills den är rensad med sidan av enheten. Upprepa detta även med back luckan.
    Båda dörrarna måste vara öppna när enheten fungerar för att möjliggöra optimalt ström-till-tillbaka-luft-flöde genom enheten.

    ![Data Box Heavy dörrar öppna](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Facket på bak sidan av enheten bör ha fyra ström kablar. Ta bort alla kablar från facket och placera dem i undan.

    ![Data Box Heavy ström sladdar i facket](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Nästa steg är att identifiera de olika portarna på bak sidan av enheten. Det finns två enhetsspecifika, **Nod1** och **NOD2**. Varje nod har fyra nätverks gränssnitt, **MGMT**, **fil1**, **data2**, **DATA3**. **MGMT** används för att konfigurera hantering under den inledande konfigurationen av enheten. DATA1-**DATA3** är data portar. **Hanterings** -och **DATA3** -portar är 1 Gbit/ s, men **fil1**kan fungera som 40 Gbit/s-portar eller 10 Gbit/s-portar. Längst ned på de två enhets noderna är fyra strömförsörjnings enheter (PSUs) som delas mellan de två enhets noderna. När du möter den här enheten är **PSUs** **PSU1**, **PSU2**, **PSU3**och **PSU4** från vänster till höger.

    ![Data Box Heavy portar](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Anslut alla fyra ström kablar till enhetens strömförsörjnings enheter. De gröna lysdioderna sätter på och blinkar.
8. Använd ström knapparna i det främre planet för att aktivera enhetens noder. Låt ström knappen vara nedtryckt under några sekunder tills de blå lamporna är på. Alla gröna lysdioder för strömförsörjningen på enhetens bak enhet bör nu vara heldragna. Den främre drift panelen på enheten innehåller också fel lampor. När en fel indikator lyser, tyder det på en felaktig PSU eller en fläkt eller ett problem med disk enheterna.  

    ![Panelen Data Box Heavy-front-OPS](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Kabelansluten första nod för nätverk

Utför följande steg för kabel för nätverk på en av noderna i enheten.

1. Använd en CAT 6 RJ-45-nätverks kabel (blå kabel i bilden) för att ansluta värddatorn till en hanterings port på 1 Gbit/s.
2. Använd en QSFP + kabel (fiber eller koppar) för att ansluta minst 1 40 Gbit/s (helst över 1 Gbit/s) nätverks gränssnitt för data. Om du använder en 10 Gbit/s-växel använder du en SFP +-kabel med en QSFP + till SFP + adapter (uppfyller kraven enligt adapter) för att ansluta 40 Gbit/s nätverks gränssnitt för data.

    ![Data Box Heavy portar som är kabelanslutna](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 och DATA2 växlas och matchar inte vad som visas i det lokala webb gränssnittet.
    > 40 Gbit/s kabel kort ansluter när det infogas på det sätt som visas nedan.

    ![Data Box Heavy 40-Gbit/s kabel adapter](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Konfigurera första noden

Utför följande steg för att konfigurera din enhet med hjälp av den lokala konfigurationen och Azure Portal.

1. Ladda ned enhetens autentiseringsuppgifter från portalen. Gå till **Allmänt > Enhetsinformation**. Kopiera **enhetslösenordet**. Dessa lösen ord är knutna till en speciell ordning i portalen. Som motsvarar de två noderna i Data Box Heavy ser du de två enhets serie numren. Enhetens administratörs lösen ord för båda noderna är samma.

    ![Data Box Heavy autentiseringsuppgifter för enhet](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Anslut klient arbets stationen till enheten via en CAT6 RJ-45-nätverks kabel.
3. Konfigurera Ethernet-kortet på den dator som du använder för att ansluta till enheten med en statisk IP- `192.168.100.5` adress och `255.255.255.0`ett undernät.

    ![Data Box Heavy ansluter till lokalt webb gränssnitt](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Anslut till det lokala webb gränssnittet på enheten på följande URL: `http://192.168.100.10`. Klicka på **Avancerat** och klicka sedan på **Fortsätt till 192.168.100.10 (osäker)** .
5. En **inloggningssida** visas för det lokala webbgränssnittet.
    
    - Ett av nodens serie nummer på den här sidan matchar både Portal gränssnittet och det lokala webb gränssnittet. Anteckna antalet noder till serie nummer mappningen. Det finns två noder och två enhets serie nummer i portalen. Den här mappningen hjälper dig att förstå vilken nod som motsvarar vilket serie nummer.
    - Enheten är låst i det här läget.
    - Ange enhetens administratörs lösen ord som du fick i föregående steg för att logga in på enheten. Klicka på **Logga in**.

    ![Logga in på Data Box Heavy lokalt webb gränssnitt](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. På instrument panelen kontrollerar du att nätverks gränssnitten har kon figurer ATS. Det finns fyra nätverks gränssnitt på din enhets nod, två 1 Gbit/s och 2 40 Gbit/s. Ett av 1 Gbit/s-gränssnittet är ett hanterings gränssnitt och kan därför inte konfigureras av användaren. De återstående tre nätverks gränssnitten är dedikerade till data och kan konfigureras av användaren.

- Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt.
- Om DHCP inte är aktive rad går du till ange nätverks gränssnitt och tilldelar statiska IP-adresser om det behövs.

    ![Data Box Heavy-instrumentpanel Node 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Konfigurera andra noden

Gör stegen som beskrivs i [Konfigurera den första noden](#configure-first-node) för den andra noden av enheten.

![Data Box Heavy-instrumentpanel Node 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

När enhetskonfigurationen är klar kan du ansluta till enhetsresurserna och kopiera data från datorn till enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Data Box Heavy ämnen som:

> [!div class="checklist"]
> * Kabelanslut din Data Box Heavy
> * Anslut till din Data Box Heavy

Gå vidare till nästa självstudie för att lära dig hur du kopierar data på din Data Box Heavy.

> [!div class="nextstepaction"]
> [Kopiera data till Azure Data Box](./data-box-heavy-deploy-copy-data.md)

::: zone-end
