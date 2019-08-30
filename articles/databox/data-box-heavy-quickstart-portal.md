---
title: Snabb start för Microsoft Azure Data Box Heavy | Microsoft Docs
description: Lär dig hur du snabbt distribuerar dina Azure Data Box Heavy i Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 433c3ac08d8d7d8158d4cc958979782e3b3192e1
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164425"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Snabbstart: Distribuera Azure Data Box Heavy med hjälp av Azure Portal

I den här snabb starten beskrivs hur du distribuerar Azure Data Box Heavy med hjälp av Azure Portal. Stegen omfattar hur du kan kabel, konfigurera och kopiera data till Data Box Heavy så att de överförs till Azure. Snabbstarten utförs i Azure-portalen och på enhetens lokala webbgränssnitt.

Detaljerade stegvisa distributions- och spårningsinstruktioner finns i [Självstudie: Beställ Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Förutsättningar

Slutför följande konfigurations krav för installations platsen, Data Box-enhet tjänsten och enheten innan du distribuerar enheten.

### <a name="for-installation-site"></a>För installations plats

Innan du börjar ska du kontrollera att:

- Enheten kan anpassas till alla dina entryways. Enhets mått är: bredd: 26-"längd: 48 "höjd: 28 ".
- Du har åtkomst till enheten via en hiss eller en ramp om du planerar att installera på en annan våning än golvet.
- Du har två personer som hanterar enheten. Enheten väger cirka ~ 500 kg. och kommer på hjul.
- Du har en platt plats i data centret med närhet till en tillgänglig nätverks anslutning som kan hantera en enhet med det här utrymmet.

### <a name="for-service"></a>För tjänsten

Innan du börjar ska du kontrollera att:

- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
- Prenumerationen som du använder för Data Box-enhet-tjänsten är [Microsoft Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)eller [Microsoft Azure-sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Du har ägar-eller deltagar åtkomst till prenumerationen för att skapa en Data Box Heavys order.

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:

- Du har granskat [säkerhets rikt linjerna för din data Box Heavy](data-box-safety.md).
- Du har en värddator som är ansluten till data Center nätverket. Data Box Heavy kommer att kopiera data från den här datorn. Värd datorn måste köra ett [operativ system som stöds](data-box-heavy-system-requirements.md).
- Du har en bärbar dator med RJ-45-kabel för att ansluta till det lokala användar gränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod av enheten en gång.
- Ditt data Center har snabba nätverk och du har minst 1 10 GbE-anslutning.
- Du behöver 1 40 Gbit/s kabel eller 10 Gbit/s-kabel per enhet-nod. Välj kablar som är kompatibla med Mellanox MCX314A-BCCT Network Interface:
    - För kabeln på 40 Gbit/s måste enhetens slut punkt vara QSFP +.
    - För 10 Gbit/s-kabeln behöver du en SFP + kabel som ansluts till en 10-G-växel i ena änden, med en QSFP + till SFP + adapter (eller uppfyller kraven enligt-adaptern) för det slut som kopplas till enheten.
- Ström kablarna ingår i ett fack på bak sidan av enheten.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Beställa

Det här steget tar ungefär 5 minuter.

1. Skapa en ny Azure Data Box-resurs på Azure-portalen.
2. Välj en befintlig prenumeration som har aktiverats för den här tjänsten och välj överföringstypen **Importera**. Ange **ursprungslandet** där dina data finns och **Azure-målregionen** för dataöverföringen.
3. Välj **data Box Heavy**. Den största användbara kapaciteten är 770 TB och du kan skapa flera beställningar för större data storlekar.
4. Ange order- och leveransinformationen. Om tjänsten är tillgänglig i din region anger du e-postadresser för aviseringsmeddelanden, läser sammanfattningen och skapar sedan ordern.

När ordern har skapats förbereds enheten för leverans.

::: zone-end

::: zone target = "chromeless"

# <a name="cable-and-connect-to-your-device"></a>Kabel och Anslut till din enhet

När du har granskat kraven kan du kontakta din enhet och ansluta till enheten.

::: zone-end

## <a name="cable-for-power"></a>Ström kabel

Det här steget tar cirka 5 minuter.

När du får Data Box Heavy ska du utföra följande steg för att sätta enheten i kraft och slå på enheten.

1. Fortsätt inte om det finns tecken på att enheten har manipulerats eller skadats. Kontakta Microsoft Support så att de kan skicka en ersättningsenhet.
2. Flytta enheten till installations platsen och lås bak hjulen.
3. Anslut alla fyra ström kablar till strömförsörjningen på bak sidan av enheten.
4. Använd ström knapparna i det främre planet för att aktivera enhetens noder.

## <a name="cable-first-node-for-network"></a>Kabelansluten första nod för nätverk

Det här steget tar cirka 10-15 minuter att slutföra.

1. Använd RJ-45 CAT 6-nätverkskabeln till att ansluta värddatorn till hanteringsporten (MGMT) på enheten.
2. Använd twinax-QSFP + koppar kabeln för att ansluta minst 1 40 Gbit/s (helst över 1 Gbit/s) nätverks gränssnitt, DATA 1 eller DATA 2 för data. Om du använder en 10 Gbit/s-växel använder du en twinax SFP + koppar-kabel med QSFP + till SFP + adapter (uppfyller kraven enligt adapter) för att ansluta 40-Gbit/s-datagränssnittet för data.
3. Kabelanslut enheten enligt nedan.  

    ![Data Box Heavy kabelansluten](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Konfigurera första noden

Det här steget tar cirka 5–7 minuter att slutföra.

1. Hämta lösenordet för enheten på **Allmänt > Enhetsinformation** på [Azure-portalen](https://portal.azure.com). Samma lösen ord används för båda noderna i enheten.
2. Tilldela en statisk IP-adress för 192.168.100.5 och undernät 255.255.255.0 till Ethernet-kortet på den dator som du använder för att ansluta till Data Box Heavy. Öppna det lokala webbgränssnittet på enheten på `https://192.168.100.10`. Anslutningen kan upp till 5 minuter att slutföra efter att du slagit på enheten.
3. Logga in med lösenordet från Azure-portalen. Ett felmeddelande visas som anger ett problem med webbplatsens säkerhetscertifikat. Följ de webbläsarspecifika instruktionerna för att fortsätta till webbsidan.
4. Som standard konfigureras nätverks inställningarna för gränssnitten (förutom HANTERINGs tjänsten) som DHCP. Om det behövs kan du konfigurera dessa gränssnitt som statiska och ange en IP-adress.

## <a name="cable-and-configure-the-second-node"></a>Kabel och konfigurera den andra noden

Det här steget tar cirka 15-20 minuter att slutföra.

Följ stegen som används för den första noden för att kabelansluta och konfigurera den andra noden på enheten.  


::: zone target = "docs"

## <a name="copy-data"></a>Kopiera data

Hur lång tid det tar att slutföra den här åtgärden beror på din data storlek och hastigheten på det nätverk som data kopieras.
 
1. Kopiera data till båda enhets noderna med hjälp av data gränssnitten 40-Gbit/s parallellt.

    - Om du använder en Windows-värd använder du ett SMB-kompatibelt fil kopierings verktyg, till exempel [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - För en NFS-värd använder du kommandot `cp` eller `rsync` för att kopiera data.
2. Anslut till resurserna på enheten med hjälp av sökvägen:`\\<IP address of your device>\ShareName`. För att få åtkomst till delnings behörigheter går du till sidan **anslut & kopia** i det lokala webb gränssnittet för data Box Heavy.
3. Kontrol lera att resurs-och mappnamnen och de data som följer rikt linjerna som beskrivs i [Azure Storage och data Box Heavy tjänst begränsningar](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Förbered för att skicka

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När data kopieringen har slutförts utan fel går du till **Förbered för att skicka** sida i det lokala webb gränssnittet och startar leverans förberedelsen.
2. När **Förbered för att skicka** har slutförts på båda noderna stänger du av enheten från det lokala webb gränssnittet.

## <a name="ship-to-azure"></a>Skicka till Azure

Den här åtgärden tar cirka 15-20 minuter att slutföra.

1. Ta bort kablarna och returnera dem till facket på bak sidan av enheten.
2. Schemalägg en upphämtning med din regionala operatör.
3. Kontakta [data Box-enhet åtgärder](mailto:DataBoxOps@microsoft.com) för att informera om hämtningen och för att hämta etiketten för retur leverans.
4. Etiketten för retur leverans bör vara synlig på enhetens front panel.

## <a name="verify-data"></a>Verifiera data

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När den Data Box Heavy enheten är ansluten till Azure datacenter-nätverket överförs data automatiskt till Azure.
2. Data Box-enhet tjänsten meddelar dig att data kopieringen har slutförts via Azure Portal.

    1. Kontrollera felloggarna för eventuella fel och vidta lämpliga åtgärder.
    2. Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan.

## <a name="clean-up-resources"></a>Rensa resurser

Det här steget tar 2–3 minuter att slutföra.

- Du kan avbryta Data Box Heavy ordningen i Azure Portal innan ordern bearbetas. När orderbehandlingen har påbörjats kan du inte avbryta ordern. Ordern behandlas tills den når slutskedet i processen. Om du vill avbryta beställningen går du till **Översikt** och klickar på **Avbryt** i kommandofältet.

- Du kan ta bort ordern när statusen visas som **slutförd** eller **avbruten** på Azure Portal. Om du vill ta bort ordern går du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Data Box Heavy som hjälper dig att importera dina data till Azure. Om du vill veta mer om Azure Data Box Heavy hantering går du vidare till följande självstudie:

> [!div class="nextstepaction"]
> [Använd Azure Portal för att administrera Data Box Heavy](data-box-portal-admin.md)

::: zone-end
