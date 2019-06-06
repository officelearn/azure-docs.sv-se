---
title: Snabbstart för Microsoft Azure Data Box aktiverat | Microsoft Docs
description: Lär dig hur du snabbt distribuera din Azure Data Box tung i Azure-portalen
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: alkohli
ms.openlocfilehash: 930da7367b3f5d7e20617afedef007efea97c51a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66518584"
---
# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Snabbstart: Distribuera Azure Data Box tung med Azure portal

Den här snabbstarten beskriver hur du distribuerar på Azure Data Box tung med Azure portal. Stegen omfattar kabelanslut, konfigurera och kopiera data till Data Box tung så att den laddas upp till Azure. Snabbstarten utförs i Azure-portalen och på enhetens lokala webbgränssnitt.

Detaljerade stegvisa distributions- och spårningsinstruktioner finns i [Självstudie: Ordning Azure Data Box aktiverat](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför följande konfigurationskraven för av installationsplatsen, Data Box-tjänsten och enheten innan du distribuerar enheten.

### <a name="for-installation-site"></a>För installationsplats

Innan du börjar ska du kontrollera att:

- Enheten får plats via alla entryways. Enheten är dimensionerna: bredd: 26 ”längd: 48 ”höjd: 28”.
- Du har åtkomst för enheten via en hiss eller en ramp om du planerar att installera på en vägg än maskinrummet.
- Du har två personer att hantera enheten. Enheten väger cirka 500 ~ lbs. och hämtas på hjul.
- Du har en fast plats i datacentret med närhet till en tillgänglig nätverksanslutning som kan hantera en enhet med den här tjänsten.

### <a name="for-service"></a>För tjänsten

Innan du börjar ska du kontrollera att:

- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
- Den prenumeration som du använder för Data Box-tjänsten är [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview), eller [Microsoft Azure sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Du har ägare eller deltagare åtkomst till prenumerationen för att kunna skapa en Data Box tung order.

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:

- Du har granskat den [riktlinjer för säkerhet för dina Data Box tung](data-box-safety.md).
- Du har en värddator som är anslutet till datacenternätverket. Data Box tung att kopiera data från den här datorn. Värddatorn måste köra en [operativsystem som stöds](data-box-heavy-system-requirements.md).
- Du har en bärbar dator med RJ-45 kabeln för att ansluta till lokala Användargränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod i enheten en gång.
- Datacentret har höghastighetsnätverk och du behöver minst ett 10 GbE-anslutning.
- Du behöver en 40-Gbit/s- eller 10 Gbit/s kabel per enhetsnod. Välj kablar som är kompatibla med Mellanox MCX314A-BCCT nätverksgränssnitt:
    - Enheten änden av kabeln måste vara QSFP + för 40 Gbit/s-kabel.
    - För 10 Gbit/s-kabel behöver du en SFP +-kabel som ansluts till en 10-G-växel på en end med en QSFP + SFP + nätverkskort (eller kortet QSA) för end som ansluts till enheten.
- Strömkablarna ingår i ett fack på baksidan av enheten.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="order"></a>Beställa

Det här steget tar ungefär 5 minuter.

1. Skapa en ny Azure Data Box-resurs på Azure-portalen.
2. Välj en befintlig prenumeration som har aktiverats för den här tjänsten och välj överföringstypen **Importera**. Ange **ursprungslandet** där dina data finns och **Azure-målregionen** för dataöverföringen.
3. Välj **Data Box aktiverat**. Den maximala användbar kapaciteten är 770 TB och du kan skapa flera order för större datamängder.
4. Ange order- och leveransinformationen. Om tjänsten är tillgänglig i din region anger du e-postadresser för aviseringsmeddelanden, läser sammanfattningen och skapar sedan ordern.

När ordern har skapats förbereds enheten för leverans.

## <a name="cable-for-power"></a>Kabel kraft

Det här steget tar cirka 5 minuter.

När du tar emot Data Box-tung gör du följande för att ansluta enheten kraft och aktivera enheten.

1. Fortsätt inte om det finns tecken på att enheten har manipulerats eller skadats. Kontakta Microsoft Support så att de kan skicka en ersättningsenhet.
2. Ändra enheten till installationsplats och låsa bakre hjul.
3. Anslut alla fyra strömkablarna till strömförsörjningar på baksidan av enheten.
4. Använd knapparna power i front plan för att aktivera enheten noder.

## <a name="cable-first-node-for-network"></a>Kabel första noden för nätverk

Det här steget tar cirka 10 – 15 minuter för att slutföra.

1. Använd RJ-45 CAT 6-nätverkskabeln till att ansluta värddatorn till hanteringsporten (MGMT) på enheten.
2. Använda en Twinax QSFP +-kopparkabel kabel för att ansluta minst ett 40 Gbit/s (helst över 1 Gbit/s) nätverksgränssnitt, DATA 1 eller 2 av DATA för. Om du använder en 10 Gbit/s-växel, Använd en Twinax SFP +-kopparkabel kabel med en QSFP + till SFP + kort (QSA-adapter) för att ansluta 40 Gbit/s-nätverksgränssnitt för data.
3. Kabelanslut enheten enligt nedan.  

    ![Data Box tung kabelansluten](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Konfigurera första nod

Det här steget tar cirka 5–7 minuter att slutföra.

1. Hämta lösenordet för enheten på **Allmänt > Enhetsinformation** på [Azure-portalen](http://portal.azure.com). Samma lösenord ska användas för båda noderna i enheten.
2. Tilldela en statisk IP-adress på 192.168.100.5 och undernät 255.255.255.0 till Ethernet-adaptern på den dator som du använder för att ansluta till Data Box tung. Öppna det lokala webbgränssnittet på enheten på `https://192.168.100.10`. Anslutningen kan upp till 5 minuter att slutföra efter att du slagit på enheten.
3. Logga in med lösenordet från Azure-portalen. Ett felmeddelande visas som anger ett problem med webbplatsens säkerhetscertifikat. Följ de webbläsarspecifika instruktionerna för att fortsätta till webbsidan.
4. Nätverksinställningar för de gränssnitt (förutom MGMT) är konfigurerade som DHCP som standard. Om det behövs kan du konfigurera dessa gränssnitt som statiskt och ange en IP-adress.

## <a name="cable-and-configure-the-second-node"></a>Ansluta och konfigurera den andra noden

Det här steget tar ungefär 15-20 minuter för att slutföra.

Följ stegen som används för den första noden för att ansluta och konfigurera den andra noden på enheten.  

## <a name="copy-data"></a>Kopiera data

Tid att slutföra den här åtgärden beror på datastorleken på din och hastigheten på nätverket som data kopieras.
 
1. Kopiera data till både de enheten noder som använder både 40 Gbit/s data gränssnitten parallellt.

    - Om med hjälp av en Windows-värd som använder en SMB-kompatibelt filkopieringsverktyg [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - För en NFS-värd använder du kommandot `cp` eller `rsync` för att kopiera data.
2. Ansluta till resurser på enheten med hjälp av sökvägen:`\\<IP address of your device>\ShareName`. Autentiseringsuppgifter för resursåtkomst, gå till den **Connect & copy** sida i det lokala webbgränssnittet på Data Box-tung.
3. Se till att resursen och mappnamn och data följer riktlinjer som beskrivs i den [tjänstbegränsningar för Azure Storage och Data Box tunga](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Förbereda för att skicka

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När Datakopieringen har slutförts utan fel, går du till **Förbered för att skicka** i enhetens lokala webbgränssnitt och börja förbereda frakten.
2. Efter den **Förbered för att skicka** har slutförts på båda noderna, inaktivera enheten från det lokala webbgränssnittet.

## <a name="ship-to-azure"></a>Skicka till Azure

Den här åtgärden tar ungefär 15-20 minuter för att slutföra.

1. Ta bort kablarna och återställa dem i fack på baksidan av enheten.
2. Schemalägga en upphämtning med din regionala operatör.
3. Nå ut till [Box dataåtgärder](mailto:DataBoxOps@microsoft.com) att informera om för upphämtningen och för att få returetiketten.
4. Returetiketten ska vara synliga på Rensa frontpanel för enheten.

## <a name="verify-data"></a>Verifiera data

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När Data Box tung enheten är ansluten till nätverket för Azure-datacenter, överför automatiskt data till Azure.
2. Data Box-tjänsten meddelar dig att Datakopieringen är klar via Azure portal.

    1. Kontrollera felloggarna för eventuella fel och vidta lämpliga åtgärder.
    2. Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan.

## <a name="clean-up-resources"></a>Rensa resurser

Det här steget tar 2–3 minuter att slutföra.

- Du kan avbryta Data Box tung ordningen i Azure-portalen innan ordningen som bearbetas. När orderbehandlingen har påbörjats kan du inte avbryta ordern. Ordern behandlas tills den når slutskedet i processen. Om du vill avbryta beställningen går du till **Översikt** och klickar på **Avbryt** i kommandofältet.

- Du kan ta bort ordern när statusen visas som **slutförd** eller **avbruten** på Azure Portal. Om du vill ta bort ordern går du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en Data Box tung för att importera dina data till Azure. Gå vidare till nästa självstudie om du vill veta mer om Azure Data Box tung management:

> [!div class="nextstepaction"]
> [Använda Azure-portalen för att administrera Data Box tung](data-box-portal-admin.md)
