---
title: Självstudie om du vill ställa in Azure Data Box tung | Microsoft Docs
description: Lär dig att ansluta och ansluta din Azure Data Box tung
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: alkohli
ms.openlocfilehash: d4cdc9b6c31c62080fed5a8528577fef72c01c3f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271721"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy-preview"></a>Självstudier: Ansluta och ansluta till din Azure Data Box tung (förhandsversion)


Den här självstudien beskrivs hur du kabelanslut, Anslut och aktivera din Azure Data Box tung.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kabelanslut din Data Box-aktiverat
> * Ansluta till din Data Box-aktiverat

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Ordna Azure Data Box aktiverat](data-box-heavy-deploy-ordered.md).
2. Du har fått din Data Box tung och orderstatus i portalen är **levererade**.
3. Du har granskat den [Data Box tung riktlinjer för säkerhet](data-box-safety.md).
4. Du måste ha åtkomst till en fast plats i datacenter med närhet till en tillgänglig nätverksanslutning som kan hantera en enhet med den här tjänsten. Den här enheten kan inte monteras i en ställning.
5. Du har fått fyra förankrad strömkablar ska användas med lagringsenheten.
6. Du bör ha en värddator som är ansluten till datacenternätverket. Din Data Box tung att kopiera data från den här datorn. Värddatorn måste köra en [stöds operativsystemet](data-box-heavy-system-requirements.md).
7. Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. 
8. Du måste ha en bärbar dator med RJ-45 kabeln för att ansluta till lokala Användargränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod i enheten en gång.
9. Du behöver en 40-Gbit/s- eller 10 Gbit/s kabel per enhetsnod.
    - Välj kablar som är kompatibla med den [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) nätverksgränssnitt.
    - Enheten änden av kabeln måste vara QSFP + för 40 Gbit/s-kabel.
    - För 10 Gbit/s-kabel behöver du en SFP +-kabel som ansluts till en 10 Gbit/s-växel på en end med en QSFP + SFP + nätverkskort (eller kortet QSA) för end som ansluts till enheten.

## <a name="cable-your-device-for-power"></a>Kabelanslut din enhet för kraft

Vidta följande åtgärder för att kabelanslut din enhet.

1. Inspektera enheten och leta efter tecken på manipulation eller andra uppenbara skador. Fortsätt inte om enheten har manipulerats eller skadats. [Kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md) direkt för att hjälpa dig att bedöma om enheten är i gott skick och om de behöver för att leverera du ersättning.
2. Flytta enheten på installationsplatsen.

    ![Data Box tung installationsplats för enhet](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Låsa bakre hjul på enheten som visas nedan.

    ![Data Box tung enheten hjul låst](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Leta upp rattar som låser du upp klientdelen och bakdörrar för enheten. Låsa upp och flytta åtkomsten tills den är tömning med delen av enheten. Upprepa detta med bakdörr samt.
    Båda dörrarna måste vara öppen när enheten är tillgängligt för att möjliggöra flöde optimala fram till bak via enheten.

    ![Data Box tung dörrar öppna](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Systemfältet på baksidan enheten ska ha fyra strömkablar. Ta bort alla kablar från fack och placera dem tagits ur bruk.

    ![Data Box tung strömkablar i systemfältet](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Nästa steg är att identifiera de olika portarna på baksidan av enheten. Det finns två enheten noder **NODE1** och **nod2**. Varje nod har fyra nätverksgränssnitt, **MGMT**, **fil1**, **fil2**, **DATA3**. **MGMT** används för att konfigurera management under den initiala konfigurationen av enheten. **Fil1**-**DATA3** finns dataportar. **MGMT** och **DATA3** portar är 1 Gbit/s, medan **fil1**, **fil2** kan fungera som 40 Gbit/s-portar eller 10 Gbit/s-portar. Längst ned på två enheten noder är fyra power anger enheter (PSUs) som delas mellan två enheten noder. När du träffar på den här enheten, den **PSUs** är **PSU1**, **PSU2**, **PSU3**, och **PSU4** från vänster till höger.

    ![Data Box tung portar](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Anslut alla fyra strömkablarna till strömförsörjning för enheten. Gröna aktivera led: ar och blinka.
8. Använd knapparna power i front plan för att aktivera enheten noder. Behåll strömknappen nedtryckt under några sekunder tills blå lamporna tänds. Nu bör alla de gröna led: ar för strömförsörjningar på baksidan enheten solid. Operativ frontpanel för enheten innehåller också fel led: ar. När ett fel som LED lyser anger en felaktig PSU eller en fläkt eller ett problem med diskenheterna.  

    ![Data Box tung front ops-panelen](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Kabel första noden för nätverk

Gör följande på en av noderna i enheten, till kabel för nätverket.

1. Använd en CAT 6 RJ-45 nätverkskabel (blå kabel i bilden) för att ansluta värddatorn till 1 Gbit/s-hanteringsporten.
2. Använd en QSFP + kabel (fiber eller koppar) för att ansluta till minst en 40-Gbit/s (helst över 1 Gbit/s) nätverksgränssnitt för data. Om du använder en 10 Gbit/s-växel, använda en SFP +-kabel med en QSFP + till SFP + kort (QSA-adapter) för att ansluta 40 Gbit/s-nätverksgränssnitt för data.

    ![Data Box tung portar kabelansluten](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 och Fil2 växlas och matchar inte det som visas i det lokala webbgränssnittet.
    > 40 Gbit/s kabel nätverkskortet ansluter när de infogas på sätt som visas nedan.

    ![Data Box tung 40 Gbit/s kabel adapter](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Konfigurera första nod

Vidta följande steg för att konfigurera din enhet med hjälp av den lokala konfigurationen och Azure-portalen.

1. Ladda ned enhetens autentiseringsuppgifter från portalen. Gå till **Allmänt > Enhetsinformation**. Kopiera **enhetslösenordet**. Lösenorden är knutna till en viss ordning i portalen. Motsvarar de två noderna i Data Box tung visas två enhetens serienummer. Enhetens administratörslösenord för båda noderna är samma.

    ![Data Box tung enhetens autentiseringsuppgifter](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Anslut din klient-arbetsstation till enheten via en CAT6 RJ-45-nätverkssladd.
3. Konfigurera Ethernet-adaptern på den dator som du använder för att ansluta till enheten med en statisk IP-adress på `192.168.100.5` och undernät `255.255.255.0`.

    ![Data Box tung ansluter till lokala webbgränssnittet](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Ansluta till det lokala webbgränssnittet för enheten på följande URL: `http://192.168.100.10`. Klicka på **Avancerat** och klicka sedan på **gå vidare till 192.168.100.10 (osäkra)** .
5. En **inloggningssida** visas för det lokala webbgränssnittet.
    
    - En av serienummer som nod på den här sidan matchar både portalens användargränssnitt och det lokala webbgränssnittet. Anteckna antal noder till serienummer mappningen. Det finns två noder och två Enhetsserienummer i portalen. Den här mappningen hjälper dig att förstå vilken nod som motsvarar vilka tal.
    - Enheten är låst i det här läget.
    - Ange enhetens administratörslösenord som du hämtade i föregående steg för att logga in på enheten. Klicka på **Logga in**.

    ![Logga in på Data Box tung lokala webbgränssnittet](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Se till att nätverksgränssnitt som är konfigurerade på instrumentpanelen. Det finns fyra nätverksgränssnitt på din enhetsnod, två 1 Gbit/s och två 40 Gbit/s. En av 1 Gbit/s-gränssnittet är ett hanteringsgränssnitt och kan därför inte konfigureras av användaren. De återstående tre nätverksgränssnitt är dedikerade till data och kan konfigureras av användaren.

- Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt.
- Om DHCP inte är aktiverad går att ställa in nätverksgränssnitt och tilldela statiska IP-adresser om det behövs.

    ![Data Box tung instrumentpanelen nod 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Konfigurera andra nod

Gör stegen som beskrivs i den [konfigurerar den första noden](#configure-first-node) för den andra noden på enheten.

![Data Box tung instrumentpanelen nod 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

När enhetskonfigurationen är klar kan du ansluta till enhetsresurserna och kopiera data från datorn till enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Data Box tung ämnen som:

> [!div class="checklist"]
> * Kabelanslut din Data Box-aktiverat
> * Ansluta till din Data Box-aktiverat

Gå vidare till nästa självstudie och lär dig hur du kopierar data på din Data Box tung.

> [!div class="nextstepaction"]
> [Kopiera data till Azure Data Box](./data-box-heavy-deploy-copy-data.md)
