---
title: Check lista för för distribution för att distribuera Azure Stack Edge-enhet | Microsoft Docs
description: Den här artikeln beskriver den information som kan samlas in innan du distribuerar din Azure Stack Edge-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: e1233e17dfe2a56e6ea995190b243ed9c64b8945
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181365"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-device"></a>Distributions check lista för din Azure Stack Edge-enhet  

Den här artikeln beskriver den information som kan samlas in före den faktiska distributionen av Azure Stack Edge-enheten. 

Använd följande check lista för att se till att du har den här informationen när du har placerat en order för en Azure Stack Edge-enhet och innan du har fått enheten. 

## <a name="deployment-checklist"></a>Check lista för distribution 

| Fas                             | Parameter                                                                                                                                                                                                                           | Information                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Enhets hantering               | <li>Microsoft Azure prenumeration</li><li>Azure Active Directory Graph API</li><li>Microsoft Azure Storage konto</li>|<li>Aktive rad för Azure Stack Edge/Data Box Gateway, deltagar behörighet</li><li>Se till att administratörs-eller användar åtkomst</li><li>Behöver autentiseringsuppgifter för åtkomst</li> |
| Enhets installation               | Ström kablar i paketet. <br>För oss kommer en SVE 18/3-kabel som klassats för 125 V och 15 ampere med en NEMA på 5-15P till C13 (indata till utdata) att skickas.                                                                                                                                                                                                          | Medföljer enheten.<br>Mer information finns i listan över [ström sladdar som stöds efter land](azure-stack-edge-technical-specifications-power-cords-regional.md)                                                                                        |
|                                   | <li>Minst 1 X 1 – GbE RJ-45-nätverks kabel för port 1  </li><li> Minst 1 X 25-GbE SFP + koppar kabel för Port 3, Port 4, Port 5 eller port 6</li>| Kunden måste köpa dessa kablar.<br>En fullständig lista över nätverks kablar, växlar och Sänd tagare som stöds för enhets nätverkskort finns i [Cavium FastlinQ 41000-serien Interoperabilitets mat ris](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) och [Mellanox dual port 25G ConnectX-4 Channel-kompatibla produkter](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Första gången enhets anslutning      | Port 1 har en fast IP-adress (192.168.100.10/24) för inledande anslutning. <li>Kräv en bärbar dator för direkt anslutning till port 1 med en IP-adress på 192.168.100.0/24-nätverket.</li><li> Använd enhetens lokala användar gränssnitt på: `https://192.168.100.10` för ytterligare konfiguration.</li><li> Minst 1 GbE-växel måste användas för enheten när den första installationen är klar. Det lokala webb gränssnittet kan inte nås om den anslutna växeln inte är minst 1 GbE.</li>|                                                                                                                   |
| Enhets inloggning                      | Lösen ordet för enhets administratören måste bestå av mellan 8 och 16 tecken. <br>Det måste innehålla tre av följande tecken: versaler, gemener, numeriska tecken och specialtecken.                                            | Standard lösen ordet är *Password1* som går ut vid första inloggningen.                                                     |
| Nätverksinställningar                  | Enheten levereras med 2 x 1 GbE, 4 x 25 GbE-nätverks portar. <li>Port 1 används endast för att konfigurera hanterings inställningar. En eller flera data portar kan vara anslutna och konfigurerade. </li><li> Minst ett data nätverks gränssnitt från port 2-port 6 måste vara anslutet till Internet (med anslutning till Azure).</li><li> IP-inställningar stöder DHCP/statisk IPv4-konfiguration. | Statisk IPv4-konfiguration kräver IP, DNS-server och standard-gateway.                                                                                                                  |
| Beräkna nätverks inställningar     | <li>Kräv 2 statiska offentliga IP-adresser för Kubernetes-noder och minst 1 statisk IP för Azure Stack Edge Hub-tjänsten för att få åtkomst till Compute-moduler.</li><li>Kräv en IP för varje extra tjänst eller behållare som behöver nås externt från utanför Kubernetes-klustret.</li>                                                                                                                       | Endast statisk IPv4-konfiguration stöds.                                                                      |
| Valfritt Webbproxy-inställningar     | <li>Webb-proxyserverns IP/FQDN, port </li><li>Webbproxy-användarnamn, lösen ord</li>                                                                                                                                                                                                    | Stöds för närvarande inte med Compute setup.                                                                     |
| Brand vägg och port inställningar        | Använd de [listade URL-mönster och portar](azure-stack-edge-system-requirements.md#networking-port-requirements) som ska tillåtas för enhetens IP-adresser.                                                                                                                                                  |                                                                                                                   |
| Valfritt MAC-adress            | Om MAC-adressen måste vara vit listas hämtar du adressen till den anslutna porten från enhetens lokala användar gränssnitt. |                                                                                                                   |
| Valfritt Nätverks växel port    | Enhet är värd för virtuella Hyper-V-datorer för beräkning. Vissa konfigurationer av nätverks växel portar hanterar inte dessa inställningar som standard.                                                                                                        |                                                                                                                   |
| Rekommenderas Tids inställningar       | Konfigurera tids zon, primär NTP-server, sekundär NTP-server.                                                                                                                                                                    | Konfigurera den primära och sekundära NTP-servern i det lokala nätverket.<br>Om den lokala servern inte är tillgänglig kan offentliga NTP-servrar konfigureras.                                                    |
| Valfritt Uppdatera Server inställningar | <li>Kräv IP-adress för uppdaterings server i lokalt nätverk, sökväg till WSUS-server. </li> | Som standard används den offentliga Windows Update-servern.|
| Enhetsinställningar                   | <li>Enhets namn registrerat i DNS-organisationen </li><li>DNS-domän</li> |                                                                                                                   |
| Valfritt Intyg                      | Använd de certifikat som genereras av enheten <br><br> Om du tar med dina egna certifikat behöver du: <li>Betrott rot signerings certifikat i DER-format med *. cer* </li><li>Slut punkts certifikat i *PFX* -format</li>|Slut punkts certifikat är för Azure Resource Manager, Blob Storage, lokalt webb gränssnitt.                                                                                                                   |
| Aktivering                        | Kräv aktiverings nyckel från Azure Stack Edge/Data Box Gateway-resursen.                                                                                                                                                       | När den har genererats går nyckeln ut om tre dagar.                                                                        |


## <a name="next-steps"></a>Nästa steg

Förbered för att distribuera [Azure Stack Edge-enheten](azure-stack-edge-gpu-deploy-prep.md).



